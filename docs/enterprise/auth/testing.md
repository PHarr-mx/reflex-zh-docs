---
title: Testing Guarded Code
---

_reflex-enterprise v0.9.1 新增。_

# 测试受保护的代码（Testing Guarded Code）

当 [AuthPlugin](/docs/enterprise/auth/overview/) 启用时，所有未豁免的页面、事件处理器、基础字段和计算变量都是[默认安全](/docs/enterprise/auth/secure-by-default/)的。值得测试的逻辑通常是决定谁可以查看值或运行处理器的 `auth=<callable>` 授权检查。

由于检查是一个接收上下文对象并返回 bool 的普通函数，你可以直接测试它，无需网络、IdP 或浏览器。当 OIDC 逻辑本身需要测试时，使用本地模拟提供商。

```md alert info
# 在 pytest 下运行这些测试
这些代码片段直接构造 `AuthUserState()`，Reflex 仅在 pytest 环境中允许这样做。使用 `pytest` 运行它们，而非作为独立脚本。异步检查测试使用 [`pytest-asyncio`](https://pypi.org/project/pytest-asyncio/)（`uv add --dev pytest-asyncio`）；下面显示的 `@pytest.mark.asyncio` 标记无论你的项目是否在 `pyproject.toml` 中设置了 `asyncio_mode = "auto"` 都能工作。
```

## 检查就是一个函数

授权检查接收单个上下文对象并返回 bool（或其可等待形式）。它从 `ctx.auth_user_state.userinfo` 读取用户声明：

```python
from reflex_enterprise.auth import VarAuthContext


def is_staff(ctx: VarAuthContext) -> bool:
    return "staff" in (ctx.auth_user_state.userinfo.get("groups") or [])
```

要测试它，围绕一个携带该用例声明的 `AuthUserState` 构建上下文，然后调用检查。在私有 `_userinfo` 属性上设置声明；检查通过公开的 `userinfo` 属性读取它们：

```python
from reflex_enterprise.auth import AuthUserState, VarAuthContext

from my_app.auth import is_staff


def _ctx(userinfo) -> VarAuthContext:
    """一个将 ``userinfo`` 作为当前用户的 VarAuthContext。"""
    user = AuthUserState()
    user._userinfo = userinfo
    return VarAuthContext(auth_user_state=user, field_or_var=None)


def test_is_staff_allows_member():
    assert is_staff(_ctx({"sub": "u1", "groups": ["staff"]})) is True


def test_is_staff_denies_non_member():
    assert is_staff(_ctx({"sub": "u2", "groups": ["guests"]})) is False
```

上下文类从 `reflex_enterprise.auth` 导出。构建与被保护界面匹配的那个：

| 上下文 | 构造方式 |
| --- | --- |
| `VarAuthContext` | `VarAuthContext(auth_user_state=user, field_or_var=None)` |
| `EventAuthContext` | `EventAuthContext(auth_user_state=user, event_handler=None, payload={})` |
| `PageAuthContext` | `PageAuthContext(auth_user_state=user)` |

用 `AuthContext` 联合类型注解的检查适用于任何界面。通过最简单的匹配上下文测试它，通常是 `VarAuthContext`。

```md alert info
# 检查永远不会为匿名调用者运行
插件在任何检查之前解析认证。在检查内部 `ctx.auth_user_state` 始终是已登录用户。在受保护界面级别测试匿名行为，而非在检查函数中。
```

## 异步检查

异步检查以相同方式测试。将声明传入测试上下文并等待检查：

```python
import pytest


@pytest.mark.asyncio
async def test_async_check():
    assert await my_async_check(_ctx({"sub": "u1", "groups": ["admins"]})) is True
```

当 OIDC 逻辑本身或实时 Reflex 状态需要测试时，使用下面的模拟 IdP 流程。

## 针对模拟 IdP 的端到端测试

要验证 OIDC 流程，包括登录重定向、`/callback` 令牌交换、JWKS 验证、userinfo 获取以及接触真实状态的异步检查，针对本地模拟身份提供商运行应用。

[`oidc-provider-mock`](https://pypi.org/project/oidc-provider-mock/) 是一个在进程内运行的小型 OIDC 服务器。将其添加为开发依赖：

```bash
uv add --dev oidc-provider-mock
```

在后台线程中运行它，并在应用启动前将 `OIDC_*` 环境变量指向它。它默认接受任何客户端凭证并签发刷新令牌：

```python
import os

import pytest
from oidc_provider_mock import User, run_server_in_thread


@pytest.fixture(scope="session")
def mock_idp():
    """运行本地模拟 OIDC IdP 并将 OIDC_* 环境变量指向它。"""
    env = {
        # 让模拟 IdP（authlib，服务端）通过纯 HTTP 提供 OAuth。
        # 对于 localhost 颁发者是多余的，对于非 localhost HTTP 主机是必需的。
        "AUTHLIB_INSECURE_TRANSPORT": "1",
        "OIDC_CLIENT_ID": "test-client",
        "OIDC_CLIENT_SECRET": "test-secret",
    }
    # 保存和恢复以避免 OIDC_* 泄漏到其他测试。
    saved = {key: os.environ.get(key) for key in [*env, "OIDC_ISSUER_URI"]}
    os.environ.update(env)
    users = [
        User(sub="user-1", claims={"name": "Ada Lovelace", "groups": ["admins"]}),
    ]
    try:
        with run_server_in_thread(user_claims=users) as server:
            os.environ["OIDC_ISSUER_URI"] = f"http://localhost:{server.server_port}"
            yield server
    finally:
        for key, value in saved.items():
            if value is None:
                os.environ.pop(key, None)
            else:
                os.environ[key] = value
```

登录流程由浏览器驱动：重定向、Cookie 和 WebSocket 状态。使用 `AppHarness`（来自 `reflex.testing`）和 Playwright 等浏览器驱动来验证它。在上述 `mock_idp` fixture 激活的情况下，驱动认证特定步骤并断言受保护的值已送达：

```python
import re

from playwright.sync_api import Page, expect


def test_login_delivers_protected_value(auth_app, page: Page):
    base = auth_app.frontend_url.rstrip("/")
    # 受保护页面将匿名访客弹回 /login。
    page.goto(f"{base}/dashboard")
    page.wait_for_url(re.compile(r"/login"))
    # 点击应用的登录按钮（标签为 "Login with {display_name()}"）。
    # 这会启动到 IdP 授权页面的 OIDC 重定向。
    page.get_by_role("button", name="Login with Generic").click()
    # 在 IdP 上授权。oidc-provider-mock 对每个预定义用户一键无密码授权；
    # 真实 IdP 在此显示自己的登录/同意表单。
    page.locator('button[name="sub"][value="user-1"]').click()
    # 控制返回应用；回调交换令牌，受保护的值现在已送达。
    page.wait_for_url(lambda url: url.startswith(base) and "callback" not in url)
    expect(page.get_by_text("Ada Lovelace")).to_be_visible()
```

`auth_app` fixture 在 `mock_idp` 激活的情况下使用 `AppHarness` 启动你的应用；`page` 是标准的 Playwright fixture。`oidc-provider-mock` 还提供了用于独立手动测试的 CLI。

```md alert info
# 先直接测试检查；测试逻辑时使用模拟 IdP
使用手工构建的上下文调用检查更快且不需要服务器。当 OIDC 逻辑本身（重定向、回调、令牌交换、刷新）或接触实时状态的异步检查需要测试时，使用 `oidc-provider-mock`。
```

## 相关内容

- [默认安全](/docs/enterprise/auth/secure-by-default/)：`auth=`、上下文对象和执行语义。
- [概述](/docs/enterprise/auth/overview/)：插件设置和当前用户访问。
- [提供商](/docs/enterprise/auth/providers/)：提供商配置。
