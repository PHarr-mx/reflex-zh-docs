---
title: Authentication Overview
---

_reflex-enterprise v0.9.1 新增。_

# 认证概述（Authentication Overview）

`rxe.AuthPlugin` 为 Reflex 应用添加 [OIDC](https://openid.net/developers/how-connect-works/)（OpenID Connect）认证。将插件添加到 `rxe.Config(plugins=[...])`，设置提供商环境变量，然后使用 `rxe.App()` 即可。

默认情况下，每个页面、事件处理器（Event Handler）、基础字段（Base Field）和计算变量（Computed Var）都需要已认证的用户。对于需要公开访问的界面，使用 `auth=False`。

该插件针对你的身份提供商（Identity Provider，IdP）运行 OIDC 授权码 + PKCE 流程，并注册 `/login`、`/logout`、`/callback` 和 `/forbidden` 路由。

```md alert warning
# 前提条件
认证插件随 `reflex-enterprise`（v0.9.1+）一起提供。你的应用**必须**使用 `rxe.App()`（而非 `rx.App()`），并且必须通过环境变量配置一个 OIDC 身份提供商。在插件中使用普通的 `rx.App()` 会在启动时抛出 `ConfigError`。
```

## 快速开始

**1. 将 `rxe.AuthPlugin()` 添加到 `rxconfig.py`**，并通过 `OIDC_*` 环境变量配置你的 OIDC 提供商：

```python
import os

import reflex as rx
import reflex_enterprise as rxe

os.environ.setdefault("OIDC_ISSUER_URI", "https://your-idp.example.com")
os.environ.setdefault("OIDC_CLIENT_ID", "your-client-id")
os.environ.setdefault("OIDC_CLIENT_SECRET", "your-client-secret")  # 使用 PKCE 时可选

config = rxe.Config(
    app_name="my_app",
    plugins=[
        rxe.AuthPlugin(),
    ],
)
```

设置好 `OIDC_*` 变量后，应用在 IdP 可达之前就能导入和编译。OIDC 发现仅在用户登录时运行。占位符值足以满足本地构建和 CI 的需求。

**2. 在应用模块中使用 `rxe.App()`**（而非 `rx.App()`）：

```python
import reflex_enterprise as rxe

app = rxe.App()
```

**3. 在 IdP 中注册重定向 URI。** 将插件的 `auth_callback_endpoint`（默认为 `/callback`）作为允许的重定向 URI 添加到身份提供商的客户端设置中。为每个环境注册**完整的** URL（协议、主机和路径）。值不匹配会产生 `redirect_uri_mismatch` 错误；请参阅[部署到生产环境](/docs/enterprise/auth/deployment/)了解确切的回调 URL。

你无需编写提供商类。插件使用内置的 `GenericOIDCAuthState`，它会读取这些环境变量。有关命名提供商和多提供商配置，请参阅[提供商](/docs/enterprise/auth/providers/)。

## 四种受保护的界面

激活后，插件默认保护四种界面：

| 界面 | 默认行为 | 如何限制 | 退出方式 |
| --- | --- | --- | --- |
| 页面（`@rxe.page` / `@rx.page` / `app.add_page`） | 需要登录 | 重定向到 `/login` | `auth=False`，或使用 `@rxe.page(auth=True)` 强制登录 |
| 事件处理器（`@rxe.event`） | 需要登录 | 阻止 + 重定向/提示 | `@rxe.event(auth=False)` 或 `auth=<check>` |
| 基础字段（`rxe.field` / 普通 `rx.field`） | 登录前隐藏 | 替换为声明的默认值 | `rxe.field(default, auth=False)` 或 `auth=<check>` |
| 计算变量（`@rxe.var`） | 登录前隐藏 | 替换为 `initial_value`（如果没有则丢弃） | `@rxe.var(auth=False)` 或 `auth=<check>` |

`auth=True` 是所有界面的默认值，因此普通的 `rx.field(...)` 或裸 `@rxe.var` 已经受到保护。设置 `auth=False` 可使界面公开。

```python
import reflex as rx
import reflex_enterprise as rxe


class DashboardState(rx.State):
    # 默认受保护；登录前不会发送到客户端。
    revenue: rx.Field[float] = rx.field(0.0)

    # 公开；始终发送到客户端。
    theme: rx.Field[str] = rxe.field("light", auth=False)

    @rxe.event  # 默认 auth=True：匿名调用者会被重定向到 /login
    async def refresh(self): ...

    @rxe.event(auth=False)  # 公开处理器，任何人都可以调用
    def toggle_theme(self):
        self.theme = "dark" if self.theme == "light" else "light"
```

有关完整的执行模型、四种 `auth=` 包装器和授权检查函数，请参阅[默认安全](/docs/enterprise/auth/secure-by-default/)。

## 读取当前用户

`reflex_enterprise.auth.User` 是 `reflex_enterprise.auth.AuthUserState` 的别名。直接在组件中使用其类级别的 Var。它们由认证用户的提供商填充：

```python
import reflex as rx
from reflex_enterprise.auth import User


def profile() -> rx.Component:
    return rx.hstack(
        rx.avatar(src=User.picture, fallback="U"),
        rx.vstack(
            rx.heading(User.name),
            rx.text(User.email, color_scheme="gray"),
        ),
    )
```

在事件处理器中，`await User.current()` 返回用户的 `OIDCUserInfo` 声明字典（匿名时返回 `None`）：

```python
import reflex as rx
import reflex_enterprise as rxe
from reflex_enterprise.auth import User


class GreetState(rx.State):
    @rxe.event  # 默认 auth=True
    async def greet(self):
        user = await User.current() or {}
        return rx.toast(f"Hello {user.get('name') or user.get('sub')}!")
```

有关完整的 `User` API（包括前端 Var、`current()` 和 `current_provider()`），请参阅[读取当前用户](/docs/enterprise/auth/secure-by-default/#reading-the-current-user)。

## 退出登录

通过链接到 `/logout` 或绑定 `User.logout` 事件来让用户退出登录：

```python
import reflex as rx
from reflex_enterprise.auth import User

# 方式一：链接到退出登录路由。
rx.link("Sign out", href="/logout")

# 方式二：将退出登录事件绑定到任意组件。
rx.button("Sign out", on_click=User.logout)
```

`User.logout` 会退出用户登录时使用的提供商，因此它支持多提供商。如果没有人登录，则无需退出，只会将用户送回首页（`/`）。

页头示例：

```python
import reflex as rx
from reflex_enterprise.auth import User


def header() -> rx.Component:
    return rx.cond(
        User.sub != "",
        rx.hstack(
            rx.text(f"Signed in as {User.name}"),
            rx.link("Sign out", href="/logout"),
        ),
        rx.link("Log in", href="/login"),
    )
```

## 登录流程端到端解析

1. 匿名访客访问受保护的页面（或调用受保护的处理器），被重定向到 `/login`，请求的页面作为 `redirect_to` 查询参数保留。
2. `/login` 为每个已配置的提供商渲染一个按钮。访客点击其中一个，被发送到 IdP 的授权端点（授权码 + PKCE）。
3. IdP 认证用户并重定向回 `/callback`，该端点验证 OAuth `state`（CSRF），用授权码交换令牌，并将其存储在安全 Cookie 中。
4. 用户被重定向回 `redirect_to`。受保护的字段、变量、页面和处理器现在针对已认证的用户进行解析。
5. `/logout` 清除令牌，重置每个状态的受保护界面，并链接提供商的退出登录。CSRF 防护会阻止跨站退出登录请求（参阅[默认安全](/docs/enterprise/auth/secure-by-default/#logout-is-protected-against-csrf)）。

## 相关内容

- [默认安全](/docs/enterprise/auth/secure-by-default/)：执行机制、`auth=`、授权检查和 `User` 门面。
- [提供商](/docs/enterprise/auth/providers/)：提供商类、环境变量、作用域和多提供商配置。
- [自定义页面](/docs/enterprise/auth/custom-pages/)：自定义 `/login`、`/callback`、`/logout` 和 `/forbidden` 组件。
- [测试](/docs/enterprise/auth/testing/)：授权检查和模拟 IdP 集成测试。
- [部署到生产环境](/docs/enterprise/auth/deployment/)：HTTPS、回调 URL、反向代理和故障排除。
- [企业版概述](/docs/enterprise/overview/)：其他企业版功能。
