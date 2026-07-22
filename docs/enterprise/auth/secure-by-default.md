---
title: Secure by Default
---

_reflex-enterprise v0.9.1 新增。_

# 默认安全（Secure by Default）

配置好 `rxe.AuthPlugin` 后（参阅[概述](/docs/enterprise/auth/overview/)），应用中**所有**未豁免的页面、事件处理器、基础字段和计算变量都需要已登录用户，除非你明确选择退出。

```md alert warning
# 需要 `rxe.App` 和已配置的提供商
默认安全仅在应用使用 `rxe.App()`（而非 `rx.App()`）且 `rxe.AuthPlugin` 位于 `rxe.Config(plugins=[...])` 中并配置了 OIDC 提供商时生效。参阅[概述](/docs/enterprise/auth/overview/)。
```

## `auth=` 值

`auth=` 接受三种值：

| `auth=` 值 | 含义 |
| --- | --- |
| `True` | 需要已认证用户。这是所有界面的默认值。 |
| `False` | 公开。允许所有人。 |
| 可调用检查 | 仅在认证成功后运行的授权检查。返回真值则允许；返回假值或抛出异常则拒绝。 |

四个包装器在顶层导出：`rxe.page`、`rxe.event`、`rxe.field` 和 `rxe.var`。`rxe.event`、`rxe.field` 和 `rxe.var` 接受所有三种值。`rxe.page` 仅接受 **bool**。

### 全局默认值

`AuthPlugin(auth=...)` 设置应用于所有未携带显式 `auth=` 的界面的默认值。它接受相同的三种值：

```python
rxe.AuthPlugin(auth=True)  # 任何已认证用户（默认）
rxe.AuthPlugin(auth=False)  # 默认开放
rxe.AuthPlugin(auth=my_org_check)  # 登录后的组织级授权检查
```

普通的 `rx.field(...)`、裸 `@rxe.var`、裸 `@rxe.event` 以及没有 `auth=` 的 `@rxe.page()` 都**继承**此全局默认值。显式的每界面 `rxe.*(auth=...)` 始终覆盖它。更改 `AuthPlugin(auth=...)` 会改变应用基线，而无需编辑每个字段、变量、事件或页面。

### 用角色门控整个应用

可调用全局默认值在每个未标记的页面加载时运行：

```python
# my_app/auth.py
from reflex_enterprise.auth import AuthContext


def is_member_of(ctx: AuthContext) -> bool:
    """仅允许 ``admins`` 组的成员。"""
    return "admins" in (ctx.auth_user_state.userinfo.get("groups") or [])
```

```python
# rxconfig.py
import reflex_enterprise as rxe

from my_app.auth import is_member_of

config = rxe.Config(
    app_name="my_app",
    plugins=[
        rxe.AuthPlugin(
            auth=is_member_of,
            extra_scopes=["groups"],
        )
    ],
)
```

当检查需要 `groups` 声明时，请求 `extra_scopes=["groups"]`。参阅[提供商](/docs/enterprise/auth/providers/#the-claims-a-provider-returns)。未通过检查的已认证用户会被发送到 `/forbidden`，而非回到登录页面。用[自定义禁止访问页面](/docs/enterprise/auth/custom-pages/#a-custom-forbidden-page)替换该界面。

## 页面

使用 `@rxe.page` 保护页面。对于页面，`auth` 仅为 **bool**。可调用检查不支持作为每页面参数，尽管全局默认值仍然可以是可调用的。

```python
def page(
    route: str | None = None,
    *,
    auth: bool | None = None,
    **page_kwargs,
)
```

- `auth=None`（默认）：遵循已配置的全局默认值（`AuthPlugin(auth=...)`）。
- `auth=True`：始终需要已认证用户，无论全局默认值如何。
- `auth=False`：公开页面。

受保护的页面将守卫注入为**第一个** `on_load` 事件。匿名访客被重定向到登录端点，请求的页面作为 `redirect_to` 查询参数保留。登录后的流程会将他们送回该页面。

- 登录后的 `redirect_to` 目标会针对应用的源进行验证。只有同源绝对路径（如 `/dashboard`）或与应用共享协议和主机的 URL 才会被接受。任何跨源、协议相对（`//evil.test`）或反斜杠目标都会回退到首页。

```python
@rxe.page(
    route="/dashboard", title="Dashboard"
)  # auth=None：遵循全局默认值
def dashboard() -> rx.Component:
    """受保护：匿名访客被重定向到 /login。"""
    ...


@rxe.page(route="/", title="Home", auth=False)
def index() -> rx.Component:
    """公开着陆页（选择退出默认安全）。"""
    ...
```

任何额外的 `**page_kwargs` 都会转发给 `rx.page`：`title`、`image`、`description`、`meta`、`script_tags` 和 `on_load`。当提供了 `on_load` 时，守卫会被前置。

### 在页面加载时读取用户

由于守卫被前置，页面的 `on_load` 在认证之后运行。在那里 `await User.current()`（或 `ctx.auth_user_state.userinfo`）不为 `None`：

```python
@rxe.page(route="/dashboard", title="Dashboard", on_load=DashboardState.load)
def dashboard() -> rx.Component: ...


class DashboardState(rx.State):
    greeting: str = ""

    @rxe.event
    async def load(self):
        user = await User.current() or {}
        self.greeting = f"Welcome, {user.get('name') or user.get('sub')}"
```

`User.current()` 仅限事件使用；`on_load` 处理器是一个事件，从守卫中解析已认证的用户。参阅[读取当前用户](#reading-the-current-user)。

### 未使用 `@rxe.page` 添加的页面

插件激活后，`rxe.App()` 默认将**所有**页面设为需要登录，包括通过 `app.add_page(...)` 或普通 `@rx.page` 添加的页面。使用 `auth=False` 退出：

```python
app.add_page(index, route="/", auth=False)
```

由于普通 `@rx.page` 不接受 `auth` 参数，使用 `@rxe.page(auth=False)` 来让装饰的页面退出。

### 当全局默认值是可调用的

页面不能直接接受可调用的 `auth=`。如果 `AuthPlugin(auth=...)` 是可调用的，未标记的页面（`@rxe.page()` / `@rx.page` / `app.add_page`）在加载时运行它。**未通过**检查的已认证访客被重定向到 `forbidden_endpoint`（默认为 `/forbidden`）。显式的 `@rxe.page(auth=True)` 仅要求登录，**不**运行可调用默认值。

## 事件处理器

使用 `@rxe.event` 保护事件处理器。这里 `auth` 接受 bool 或可调用检查。

```python
def event(
    fn=None,
    *,
    auth: bool | EventAuthCheck = ...,
    **event_kwargs,
)
```

可裸用（`@rxe.event`）或调用（`@rxe.event(auth=...)`），可包装原始函数或已转换的 `EventHandler`。额外的 `**event_kwargs` 转发给 `rx.event`：`background`、`stop_propagation`、`prevent_default`、`throttle`、`debounce` 和 `temporal`。

```python
class DemoState(rx.State):
    @rxe.event  # 默认 auth=True：匿名调用者被重定向到 /login
    async def protected_action(self):
        user = await User.current() or {}
        return rx.toast(f"Hello {user.get('name') or user.get('sub')}!")

    @rxe.event(auth=False)
    def public_action(self):
        """任何人都可以调用。"""
        ...
```

事件处理器上的授权检查失败会显示 `"Action not allowed"` 提示（参阅[认证与授权](#authentication-vs-authorization)）。

## 基础字段

基础（状态）字段默认受保护。状态类上的普通 `rx.field(...)` 或裸注解是受保护的，在用户被解析之前会从状态增量中丢弃。使用 `rxe.field` 来让字段退出或附加检查。

```python
def field(
    default=...,
    *,
    auth: bool | FieldVarAuthCheck = ...,
    default_factory=None,
    is_var=True,
)
```

```python
class DemoState(rx.State):
    # 默认受保护；登录前从增量中丢弃。
    notes: rx.Field[str] = rx.field("These notes are only sent once you log in.")

    # 公开；始终发送到客户端。
    loading: rx.Field[bool] = rxe.field(False, auth=False)
```

被隐藏的字段在增量中替换为其**声明的默认值**（烘焙到前端包中的值），直到用户被解析。

## 计算变量

计算变量默认受保护，登录前被隐藏。使用 `@rxe.var` 包装它们。

```python
def var(
    fget=None,
    *,
    auth: bool | FieldVarAuthCheck = ...,
    **var_kwargs,
)
```

可裸用（`@rxe.var`）或调用（`@rxe.var(auth=..., initial_value=...)`）。额外的 `**var_kwargs` 原样转发给 `rx.var`：`initial_value`、`cache`、`deps`、`auto_deps`、`interval` 和 `backend`。

```md alert info
# 始终为受保护的变量配对 `initial_value`
受保护的变量在用户被解析之前被隐藏。设置 `initial_value=` 为一个烘焙到前端包中的占位符，在登录后真实值送达之前显示。
```

```python
class DemoState(rx.State):
    @rxe.var(initial_value="🔒 log in to reveal this")
    def protected_tip(self) -> str:
        """默认受保护；在用户被解析之前显示占位符。"""
        return "✅ Delivered only to logged-in users."

    @rxe.var(auth=False)
    def public_label(self) -> str:
        """对匿名访客开放。"""
        return "Anyone can read this."
```

## 授权检查

对于比"任何已认证用户"更细粒度的控制，将**可调用** `auth=` 检查附加到需要它的特定事件、变量或字段上。检查是一个接收单个**上下文对象**并返回 bool（或其可等待形式）的函数：

```python
def check(ctx) -> bool: ...  # 同步
async def check(ctx) -> bool: ...  # 异步
```

检查**仅在认证成功后**运行。匿名调用者在检查运行之前被重定向到登录页面，检查内部始终存在已解析的用户。

### 上下文对象

每个界面传递不同的上下文，都携带当前用户作为 `ctx.auth_user_state`（一个 `AuthUserState`）。从 `reflex_enterprise.auth` 导入它们：

| 上下文 | 界面 | 额外属性 |
| --- | --- | --- |
| `EventAuthContext` | 事件处理器 | `event_handler`（被门控的处理器），`payload`（事件载荷字典） |
| `VarAuthContext` | 字段 / 计算变量 | `field_or_var`（`Var`，或 `None`） |
| `PageAuthContext` | 页面（仅可调用全局默认值） | 无 |

从 `ctx.auth_user_state.userinfo`（一个普通字典）读取用户声明：

```python
from reflex_enterprise.auth import EventAuthContext, VarAuthContext


def is_admin(ctx: EventAuthContext) -> bool:
    """事件检查：仅允许 ``admins`` 组的成员。"""
    return "admins" in (ctx.auth_user_state.userinfo.get("groups") or [])


def is_staff(ctx: VarAuthContext) -> bool:
    """字段/变量检查：仅允许员工。"""
    return "staff" in (ctx.auth_user_state.userinfo.get("groups") or [])


class DemoState(rx.State):
    @rxe.event(auth=is_admin)  # 授权失败 -> "Action not allowed" 提示
    def admin_action(self):
        return rx.toast.success("Admin action executed.")

    # 除非员工检查通过否则隐藏；否则显示占位符。
    audit_log: rx.Field[list[str]] = rxe.field([], auth=is_staff)

    @rxe.var(auth=is_staff, initial_value="🔒 staff only")
    def staff_view(self) -> str:
        return "✅ staff-only computed var."
```

### 一个检查适用于任何界面

用 `AuthContext` 联合类型注解 `ctx`，使一个函数可用于**任何**界面；用单个上下文类型注解以限制它（并获得精确的自动补全）。`isinstance(ctx, EventAuthContext)` 在函数体内缩小联合类型：

```python
from reflex_enterprise.auth import AuthContext, EventAuthContext


def is_admin(ctx: AuthContext) -> bool:
    """可用作事件、字段、变量或全局默认值检查。"""
    return "admins" in (ctx.auth_user_state.userinfo.get("groups") or [])
```

```md alert warning
# 缓存的声明可能滞后 IdP 最多 30 分钟
`ctx.auth_user_state.userinfo` 从服务器端缓存提供，最多每 30 分钟刷新一次。访问令牌最多约每 60 秒重新读取一次。依赖 `groups` 或角色的检查可能会在一个令牌的生命周期内最多延迟 30 分钟才看到 IdP 端的更改（如组撤销）。每当访问令牌更改时缓存都会失效，因此使用刷新令牌（`extra_scopes=["offline_access"]`）的短生命周期令牌配置会更早获取更改。如果撤销必须立即生效，在异步检查中查询你的数据库或授权服务，而非依赖缓存的声明。
```

### 异步检查

大多数检查可以是同步的。从 `ctx.auth_user_state.userinfo` 读取 OIDC 声明：

```python
import reflex as rx
import reflex_enterprise as rxe
from reflex_enterprise.auth import AuthContext


def is_org_admin(ctx: AuthContext) -> bool:
    return "admins" in (ctx.auth_user_state.userinfo.get("groups") or [])


class DemoState(rx.State):
    @rxe.event(auth=is_org_admin)
    async def privileged_action(self):
        return rx.toast.success("Done.")

    @rxe.var(auth=is_org_admin, initial_value="🔒 admins only")
    def admin_view(self) -> str:
        return "✅ admin-only value."
```

当检查依赖 `groups` 声明时，在 OAuth 登录期间请求 `extra_scopes=["groups"]`。

检查也可以是异步的。框架在正确的时机等待它：处理器的每事件门控，或字段和变量的增量解析。当检查调用异步 API 时使用异步检查，例如：

- 查询数据库。
- 调用远程授权服务，如 OpenFGA 或其他 ReBAC 后端。
- 当策略输入存储在状态中时，使用 `await ctx.auth_user_state.get_state(...)` 访问另一个 Reflex 状态。

```md alert warning
# 一个函数，一个 auth 值
同一个函数不能支持两个具有不同 `auth` 值的界面，例如一个 `auth=True` 的变量和另一个 `auth=False` 的变量共享同一个 getter。这会抛出 `ValueError`。复用具有相同 auth 值的函数是支持的；否则为每个界面定义单独的函数。
```

## 认证与授权

两种失败模式是刻意不同的。针对已解析的用户按界面应用：

| 情况 | 结果 |
| --- | --- |
| `auth=False` | **允许。** |
| 未登录（未解析到用户） | **认证失败**。在任何检查运行之前处理。 |
| 已登录且 `auth=True` | **允许。** |
| 已登录且检查返回真值 | **允许。** |
| 已登录且检查返回假值或抛出异常 | **授权失败。** 永远不会重定向到登录。 |

每种失败的具体行为取决于界面：

| 界面 | 认证失败（匿名） | 授权失败（检查拒绝） |
| --- | --- | --- |
| 事件处理器 | 阻止 + 重定向到 `/login` | 阻止 + `"Action not allowed"` 提示 |
| 页面 | 重定向到 `/login`（带 `redirect_to`） | 重定向到 `/forbidden` |
| 字段 / 计算变量 | 隐藏（显示占位符 / 默认值） | 隐藏（显示占位符 / 默认值） |

从顺序中得出两个属性：检查**永远不会为匿名调用者运行**，并且**抛出异常的检查会失败关闭**。异常被视为拒绝结果。

## 隐藏机制如何工作

受保护的基础字段从状态增量中丢弃，受保护的计算变量被隐藏，对任何无权查看它们的调用者都是如此。同步检查在构建增量时内联求值；异步检查被延迟并在增量解析期间等待。

`hydrate` 事件在认证 Cookie 已知**之前**运行。即使对于已登录用户，受保护的值最初也会被隐藏，因为用户尚未被解析。一旦某个事件解析了已认证的用户（例如受保护页面上的页面守卫），受保护的名称会在该事件的增量中重新送达，针对已解析的用户进行过滤。

为受保护的计算变量设置 `initial_value`。占位符被烘焙到前端包中，在登录后真实值到达之前显示。

## 退出登录重置受保护状态

退出登录时，每个未豁免状态的**受保护**界面被重置。这防止一个用户的会话数据泄漏到同一客户端令牌上的下一个用户：

- 受保护的基础变量恢复为声明的默认值。
- 受保护的缓存计算变量被丢弃。
- 仅服务端的后端变量被清除。

**公开（`auth=False`）的字段和变量在退出登录时被保留**。它们不属于已认证的会话。

### 退出登录受 CSRF 保护

插件为配置的 `logout_endpoint` 安装中间件。当浏览器发送 `Sec-Fetch-Site: cross-site` 时，跨站 GET 导航到 `/logout` 会被阻止；这些请求被重定向到前端根路径。同源退出登录请求正常继续。无需配置。

```md alert warning
# 仅覆盖后端提供的前端
此守卫仅在后端提供编译后的前端时适用，这是默认的全栈部署。在前后端分离部署中，退出登录页面由其他地方提供，**不**受保护。在那里添加跨站守卫。
```

## 豁免状态

某些状态类永远不受保护，也永远不会被门控：

- 在 `reflex` 或 `reflex_enterprise` 内部定义的状态类。
- 任何 `OIDCAuthState` 子类，包括用户定义的认证提供商。

这些豁免让提供商状态在登录前读取认证 Cookie，并让页面守卫解析当前用户而不被门控。

## 读取当前用户

导入 `User` 门面以从前端或后端读取当前用户：

```python
from reflex_enterprise.auth import User
```

`User` 是 `reflex_enterprise.auth.AuthUserState` 的别名，可以互换使用。

**前端 Var**：将这些类级别描述符直接嵌入组件中。它们绑定到 `AuthUserState`，在登录后由认证用户的提供商填充。每个类型为 `str`，登录前为空（`""`）：

| 属性 | 值 |
| --- | --- |
| `User.name` | 用户的 name 声明。 |
| `User.email` | 用户的 email 声明。 |
| `User.sub` | 用户的主体标识符。 |
| `User.picture` | 用户的头像 URL。 |

```python
rx.avatar(src=User.picture, fallback="U", size="5")
rx.heading(User.name, size="6")
rx.text(User.email, color_scheme="gray")
rx.cond(User.sub != "", rx.text("Signed in"), rx.link("Log in", href="/login"))
```

### 在组件中读取其他声明

常见的 OIDC 声明 `name`、`email`、`sub` 和 `picture` 被投射为前端 Var。完整的 `userinfo` 字典仅限服务端，永远不会被序列化。要在组件中渲染任何其他声明（`groups`、角色、自定义字段），通过在 `AuthUserState` 子状态上声明计算变量将其投射到前端：

```python
import reflex as rx
from reflex_enterprise.auth import AuthUserState


class UserExtras(AuthUserState):
    @rx.var
    def groups(self) -> list[str]:
        return self.userinfo.get("groups") or []


# 在组件中：
rx.cond(UserExtras.groups.contains("admins"), rx.badge("Admin"), rx.fragment())
```

对于后端检查，通过 `await User.current()` 或 `ctx.auth_user_state.userinfo.get(...)` 读取相同的声明。参阅[提供商返回的声明](/docs/enterprise/auth/providers/#the-claims-a-provider-returns)。

**后端**：在事件处理器中使用这些。`current()` 和 `current_provider()` 是异步的：

| 调用 | 返回值 |
| --- | --- |
| `await User.current()` | 当前事件的用户 `OIDCUserInfo` 声明字典，匿名时为 `None`。 |
| `await User.current_provider()` | 实际认证此事件用户的提供商**类**，或 `None`。在多提供商配置中正确。 |
| `User.logout` | 将当前用户登出的事件处理器。绑定它（`on_click=User.logout`）或从处理器中返回它。匿名时重定向到首页（`/`）。 |

在授权检查中，`ctx.auth_user_state.provider` 返回相同的提供商类。

`OIDCUserInfo` 在运行时是一个普通字典。使用 `.get(...)` 读取声明：

```python
class DemoState(rx.State):
    @rxe.event  # 默认 auth=True
    async def whoami(self):
        user = await User.current() or {}
        return rx.toast(f"You are {user.get('name') or user.get('sub')}.")
```

## 相关内容

- [概述](/docs/enterprise/auth/overview/)：插件设置和登录流程。
- [提供商](/docs/enterprise/auth/providers/)：提供商配置。
- [自定义页面](/docs/enterprise/auth/custom-pages/)：自定义登录、回调、退出登录和禁止访问页面。
- [测试](/docs/enterprise/auth/testing/)：单元测试和模拟 IdP 流程测试。
