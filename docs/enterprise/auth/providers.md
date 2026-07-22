---
title: OIDC Providers
---

_reflex-enterprise v0.9.1 新增。_

# OIDC 提供商（OIDC Providers）

OIDC 提供商是针对你的身份提供商（IdP）运行 OpenID Connect 授权码 + PKCE 流程的状态类。`rxe.AuthPlugin` 内置了一个从环境变量解析配置的提供商。

有关插件如何保护页面、事件、字段和计算变量，请参阅[默认安全](/docs/enterprise/auth/secure-by-default/)；有关设置步骤，请参阅[概述](/docs/enterprise/auth/overview/)。

## 默认提供商

`GenericOIDCAuthState` 是内置提供商。它读取三个环境变量：

```bash
OIDC_ISSUER_URI=https://your-issuer.example.com
OIDC_CLIENT_ID=your-client-id
OIDC_CLIENT_SECRET=your-client-secret   # 可选；没有它 PKCE 也能工作
```

`AuthPlugin.auth_providers` 默认为 `[GenericOIDCAuthState]`。设置好 `OIDC_*` 变量后，无需提供商类：

```python
import reflex as rx
import reflex_enterprise as rxe

config = rxe.Config(
    app_name="my_app",
    plugins=[rxe.AuthPlugin()],  # 使用 GenericOIDCAuthState + OIDC_* 环境变量
)
```

将插件的 `auth_callback_endpoint`（默认 `/callback`）注册为 IdP 的重定向 URI。

## 命名提供商

要使用提供商特定的环境变量（以及注册多个不同的 IdP），继承 `OIDCAuthState` 并设置 `__provider__`。子类还必须继承 `rx.State`：

```python
import reflex as rx
from reflex_enterprise.auth import OIDCAuthState


class OktaAuthState(OIDCAuthState, rx.State):
    __provider__ = "okta"
```

设置 `__provider__ = "okta"` 后，配置解析优先使用 `OKTA_ISSUER_URI` / `OKTA_CLIENT_ID` / `OKTA_CLIENT_SECRET` 变量，回退到共享的 `OIDC_*` 键：

```bash
OKTA_ISSUER_URI=https://your-org.okta.com
OKTA_CLIENT_ID=your-okta-client-id
OKTA_CLIENT_SECRET=your-okta-client-secret
```

注册提供商后，默认的 `/login` 页面会为其显示一个登录按钮。`display_name()` 控制按钮标签。默认情况下，它返回 `__provider__` 值的首字母大写形式（`"okta"` -> `"Okta"`）：

```python
class OktaAuthState(OIDCAuthState, rx.State):
    __provider__ = "okta"

    @classmethod
    def display_name(cls) -> str:
        return "Okta SSO"
```

## 在 iframe 中运行

嵌入式应用使用弹窗登录/退出流程，而非顶层重定向。弹窗从用户点击时打开，并通过 `postMessage` 将令牌发送回应用自身的源。

自定义 `/login` 时，为每个提供商调用 `provider.get_login_button(*children)`。这会挂载弹窗流程使用的消息监听器。直接调用 `redirect_to_login` 不会挂载该监听器。

在大多数应用中，将用户链接到 `/login`，当默认布局不够用时自定义登录页面。参阅[自定义页面](/docs/enterprise/auth/custom-pages/#a-custom-login-page)。

在提供商子类上覆盖 `_use_popup_flow(self) -> bool` 可强制启用或禁用弹窗流程。默认返回应用是否处于嵌入状态。

## 环境变量

每个提供商通过先尝试提供商特定的 `{PROVIDER}_{KEY}` 变量，然后回退到共享的 `OIDC_{KEY}` 来解析每个配置键。`{PROVIDER}` 是大写的 `__provider__`。

| 键 | 提供商特定 | 共享回退 | 说明 |
| --- | --- | --- | --- |
| 颁发者 | `{PROVIDER}_ISSUER_URI` | `OIDC_ISSUER_URI` | IdP 颁发者 URL（其 `.well-known/openid-configuration` 从这里发现）。 |
| 客户端 ID | `{PROVIDER}_CLIENT_ID` | `OIDC_CLIENT_ID` | OAuth 客户端 ID。 |
| 客户端密钥 | `{PROVIDER}_CLIENT_SECRET` | `OIDC_CLIENT_SECRET` | 可选；没有它 PKCE 也能工作。 |

默认的 `GenericOIDCAuthState`（`__provider__ = "generic"`）先解析 `GENERIC_*` 再解析 `OIDC_*`。对于默认提供商，只需设置 `OIDC_*` 键。

## 向插件注册提供商

`AuthPlugin(auth_providers=[...])` 接受提供商**类**或 `"module.ClassName"` 导入路径**字符串**。字符串在编译时延迟解析。顺序保持不变，两种形式可以混用。默认值为 `[GenericOIDCAuthState]`。

```md alert warning
# 在 `rxconfig.py` 中使用导入路径字符串
提供商模块会导入 `reflex_enterprise`，而后者在导入时加载 `rxconfig`。在 `rxconfig.py` 中直接导入提供商类会导致重新进入配置。在那里以 `"module.ClassName"` 字符串的形式传递提供商。插件会在配置存在后延迟解析它们。
```

在 `rxconfig.py` 中，传递字符串：

```python
import reflex_enterprise as rxe

config = rxe.Config(
    app_name="my_app",
    plugins=[
        rxe.AuthPlugin(
            auth_providers=["my_app.auth.OktaAuthState"],
        ),
    ],
)
```

在 `rxconfig.py` 之外（例如在测试中），你可以直接传递类本身：

```python
from my_app.auth import OktaAuthState

rxe.AuthPlugin(auth_providers=[OktaAuthState])
```

## 作用域和刷新令牌

`extra_scopes` 会转发给每个已配置的提供商，并合并到每个提供商请求的作用域中。合并会去重并保留现有作用域（默认为 `openid email profile`）：

```python
rxe.AuthPlugin(
    auth_providers=["my_app.auth.OktaAuthState"],
    extra_scopes=["offline_access"],
)
```

- `extra_scopes=["offline_access"]` 请求 IdP 签发**刷新令牌**。一旦授予，框架会在访问令牌即将过期时刷新它，并在浏览器标签页间协调。刷新请求仅包含 IdP 最初**授予**的作用域；被请求但未被 IdP 授予的作用域（如 `offline_access`）不会在刷新时触发 `invalid_scope`。
- 没有 `offline_access` 就没有刷新令牌。会话在访问令牌过期时结束，用户被送回 `/login`。
- 如果刷新失败（刷新令牌被撤销或过期），会话将被重置，用户被登出。
- `extra_scopes=["groups"]` 请求组声明，适用于针对 `ctx.auth_user_state.userinfo.get("groups")` 的授权检查。

与令牌过期无关，每个认证 Cookie 都有固定的 7 天有效期。这是有效的最大会话时长。有关 HTTPS 和 Cookie 要求，参阅[部署指南](/docs/enterprise/auth/deployment/)。

### 每个提供商的作用域

`extra_scopes` 统一应用于每个已配置的提供商，且仅**添加**到默认值。要为单个提供商设置不同的作用域集，在该子类上设置 `_requested_scopes` 类属性。与 `extra_scopes` 不同，这会**替换**默认的 `"openid email profile"` 而非合并。

```python
import reflex as rx
from reflex_enterprise.auth import OIDCAuthState


class DatabricksAuthState(OIDCAuthState, rx.State):
    __provider__ = "databricks"
    _requested_scopes: str = "all-apis offline_access openid email profile"
```

### 读取已授予的作用域

每个提供商都暴露一个 `granted_scopes` Var，保存 IdP 实际授予的以空格分隔的作用域。`await User.current_provider()` 解析认证当前用户的提供商，因此在你自己的状态上派生一个读取活跃提供商作用域的计算变量——然后 `rx.cond` 根据它进行门控，无论用户使用哪个提供商登录：

```python
import reflex as rx
from reflex_enterprise.auth import User


class DemoState(rx.State):
    @rx.var(initial_value=False, auto_deps=False, deps=[])
    async def has_offline_access(self) -> bool:
        """活跃提供商是否被授予了 `offline_access`。"""
        provider = await User.current_provider()
        if provider is None:
            return False
        scopes = (await self.get_state(provider)).granted_scopes.split()
        return "offline_access" in scopes
```

```python
rx.cond(
    DemoState.has_offline_access,
    rx.text("Long-lived session enabled"),
    rx.text("Session ends at token expiry"),
)
```

已授予的作用域在用户登录后固定不变，因此该变量不携带响应式依赖（`auto_deps=False, deps=[]`）：它在登录时解析并在整个会话期间保持稳定。

## 多提供商

`/login` 为每个提供商显示一个登录按钮，即使只有一个也是如此——它永远不会自动重定向到 IdP。回调从 OAuth `state` 参数解析**发起**提供商；退出登录解析当前持有令牌的**活跃**提供商。

```python
rxe.AuthPlugin(
    auth_providers=[
        "my_app.auth.OktaAuthState",
        "my_app.auth.AzureAuthState",
    ],
)
```

```md alert warning
# 运行多个提供商时为每个提供商提供独立配置
如果两个或更多提供商对必需键（颁发者或客户端 ID）回退到共享的 `OIDC_*` 配置，它们将解析为相同的 IdP 值。插件会在启动时抛出 `ConfigError` 并列出受影响的提供商。为每个提供商设置特定的 `{PROVIDER}_*` 变量，例如 `OKTA_ISSUER_URI` 和 `AZURE_ISSUER_URI`。
```

读取用户是提供商无关的：`User.name` / `.email` / `.sub` / `.picture` 绑定到 `AuthUserState`，由完成登录的提供商填充。要在后端代码中根据提供商进行分支，使用 `await User.current_provider()`：

```python
import reflex as rx
import reflex_enterprise as rxe
from reflex_enterprise.auth import User


class DemoState(rx.State):
    @rxe.event
    async def show_provider(self):
        provider = await User.current_provider()
        return rx.toast(provider.__provider__ if provider else "anonymous")
```

## 提供商返回的声明

`OIDCUserInfo` 是一个 `TypedDict`（`total=False`），仅声明了 `sub`。OIDC 规范要求该声明。`name`、`email` 和 `picture` 等个人资料声明仅在其作用域被授予时出现。在运行时，`OIDCUserInfo` 是一个普通字典；使用 `.get(...)` 读取声明。

要记录提供商返回的额外声明，在你的提供商上声明一个嵌套的 `UserInfo(OIDCUserInfo, total=False)`：

```python
from reflex_enterprise.auth import OIDCAuthState, OIDCUserInfo


class OktaAuthState(OIDCAuthState, rx.State):
    __provider__ = "okta"

    class UserInfo(OIDCUserInfo, total=False):
        name: str
        email: str
        picture: str
        groups: list[str]
```

常见声明被投射为 `User` 上的只读 Var（`User.name`、`.email`、`.sub`、`.picture`）；任何其他声明通过 `await User.current()` 或 `ctx.auth_user_state.userinfo.get(...)` 从字典中读取。

## 高级扩展点

`OIDCAuthState` 暴露了可覆盖的异步钩子，用于提供商特定的行为。仅覆盖你的提供商子类所需的钩子：

| 钩子 | 用途 |
| --- | --- |
| `_validate_tokens(self) -> bool` | 验证当前的访问令牌和 ID 令牌；返回它们是否有效。 |
| `_verify_jwt(self, token_json) -> Token` | 验证 ID 令牌 JWT；覆盖以自定义验证。 |
| `_valid_issuers(self) -> list[str] \| None` | 可接受的 `iss` 声明值；覆盖以支持例如 Azure 多租户。 |
| `_set_tokens(self, access_token, id_token=None, refresh_token=None, granted_scopes=None, **kwargs)` | 交换后持久化令牌；覆盖以处理额外的响应数据。 |
| `_set_tokens_payload_from_exchange(self, exchange) -> dict` | 构建传递给 `_set_tokens` 的 kwargs；覆盖以转发令牌交换响应中的额外字段。 |
| `_validate_auth_callback_exchange(self, exchange) -> dict \| None` | 验证回调的令牌交换响应。 |
| `_fetch_userinfo(self) -> OIDCUserInfo` | 从 IdP 的 userinfo 端点获取声明；覆盖以获取或重塑声明。 |
| `_redirect_to_login_payload(self) -> dict` | 构建授权请求查询参数（scope、state、PKCE challenge）；覆盖以支持非标准登录参数。 |
| `_redirect_to_logout_payload(self) -> dict[str, str]` | 构建 IdP 结束会话参数（`state`、`id_token_hint`、`post_logout_redirect_uri`）；覆盖以自定义 `post_logout_redirect_uri` 或非标准结束会话。 |
| `_on_access_token_change(self, new_access_token, refresh=False)` | 在访问令牌被设置或刷新时响应。 |
| `_on_refresh_access_token(self, new_access_token)` | 专门在访问令牌被刷新时响应。 |

```md alert info
# 退出登录行为
退出登录会链接提供商发现的 `end_session_endpoint`，附带 `id_token_hint` 和 `post_logout_redirect_uri`。如果 IdP 未公布 `end_session_endpoint`，退出登录会清除本地令牌并重定向到应用首页。IdP 会话仍然活跃，后续登录可能会复用它。
```

## 使用访问令牌调用 API

在**你的** `OIDCAuthState` 子类的 `@rx.event`（或计算变量）中，`await self._access_token` 获取当前的 OAuth 访问令牌，然后将其作为 Bearer 令牌发送到下游服务或 IdP。这里的前导下划线不表示直接字段访问：`_access_token` 是一个仅服务端的可等待 Var，永远不会暴露给浏览器，并由后台刷新保持最新。

```python
import httpx
import reflex as rx
from reflex_enterprise.auth import OIDCAuthState


class OktaAuthState(OIDCAuthState, rx.State):
    __provider__ = "okta"

    @rx.event
    async def fetch_profile(self):
        access_token = await self._access_token
        async with httpx.AsyncClient() as client:
            resp = await client.get(
                "https://api.example.com/me",
                headers={"Authorization": f"Bearer {access_token}"},
            )
            resp.raise_for_status()
```

要在令牌签发或刷新时响应，在子类上覆盖 `_on_access_token_change` 或 `_on_refresh_access_token`。

## 跨提供商共享行为

要在多个提供商上放置相同的字段、变量、事件处理器或钩子覆盖而不重复代码，定义一个 `mixin=True` 的 `rx.State` 混入，并在每个提供商的基类中将其列在 `OIDCAuthState` **之前**：

```python
import reflex as rx
from reflex_enterprise.auth import OIDCAuthState


class SharedAuthBehavior(rx.State, mixin=True):
    async def _on_access_token_change(
        self, new_access_token, refresh=False
    ): ...  # 对每个混入此类的提供商运行


class OktaAuthState(SharedAuthBehavior, OIDCAuthState, rx.State):
    __provider__ = "okta"


class AzureAuthState(SharedAuthBehavior, OIDCAuthState, rx.State):
    __provider__ = "azure"
```

基类顺序很重要：混入必须在 `OIDCAuthState`/`rx.State` 之前。每个提供商读取自己的 `__provider__` 命名空间 Cookie。共享代码操作该提供商的令牌。

## 持久化额外的令牌交换数据

`_set_tokens_payload_from_exchange` 从 IdP 的令牌交换响应构建有效载荷（`access_token`，加上交换返回的 `id_token`、`refresh_token` 和 `granted_scopes` 中的任意项），该有效载荷是 `_set_tokens` 在回调和刷新路径上接收的全部 kwargs。

要持久化交换中的自定义字段，覆盖**两个**钩子：`_set_tokens_payload_from_exchange` 传递该字段，`_set_tokens` 接收并存储它。

```python
import reflex as rx
from reflex_enterprise.auth import OIDCAuthState


class OrgAuthState(OIDCAuthState, rx.State):
    __provider__ = "myorg"
    _org_id: str = ""

    async def _set_tokens_payload_from_exchange(self, exchange):
        payload = await super()._set_tokens_payload_from_exchange(exchange)
        if "org_id" in exchange:
            payload["org_id"] = exchange["org_id"]
        return payload

    async def _set_tokens(
        self,
        access_token,
        id_token=None,
        refresh_token=None,
        granted_scopes=None,
        org_id="",
        **kwargs,
    ):
        await super()._set_tokens(
            access_token, id_token, refresh_token, granted_scopes, **kwargs
        )
        self._org_id = org_id
```

在弹窗/iframe 流程下，弹窗和打开者是独立的客户端。弹窗运行回调并在自己的状态中捕获字段，然后将令牌发送给打开者，打开者通过 `on_iframe_auth_success` 应用它们，而非 `_set_tokens_payload_from_exchange`。只有发送的令牌会跨越，因此存储在弹窗 `self` 上的自定义字段不会自动到达打开者。

为额外的 `_set_tokens` 关键字参数提供默认值（如上面的 `org_id=""`），以便打开者的调用正常工作。

## 从 `register_auth_endpoints` 迁移

`OIDCAuthState.register_auth_endpoints(app)` 已弃用（自 reflex-enterprise v0.9.1 起，将在 1.0 中移除）。改为在 `rxe.Config(plugins=[...])` 中注册 `rxe.AuthPlugin`。插件注册 `/login`、`/logout`、`/callback` 和 `/forbidden`，并应用默认安全保护。

## 相关内容

- [默认安全](/docs/enterprise/auth/secure-by-default/)：受保护的界面和授权检查。
- [自定义页面](/docs/enterprise/auth/custom-pages/)：自定义登录、回调、退出登录和禁止访问页面构建器。
- [测试](/docs/enterprise/auth/testing/)：受保护界面和模拟 IdP 测试。
