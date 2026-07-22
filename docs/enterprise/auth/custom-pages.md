---
title: Customizing the Auth Pages
---

_reflex-enterprise v0.9.1 新增。_

# 自定义认证页面（Customizing the Auth Pages）

`rxe.AuthPlugin` 注册四个认证路由并拥有其协议逻辑。每个路由上渲染的组件可以自定义；OIDC 重定向、回调令牌交换和退出登录调度仍由插件拥有。

| 端点 | 默认路由 | 插件拥有的逻辑 | 构建器 |
| --- | --- | --- | --- |
| `login_endpoint` | `/login` | 渲染登录面板并启动 OIDC 重定向。 | `login_page` |
| `auth_callback_endpoint` | `/callback` | CSRF（OAuth `state`）检查 + 授权码令牌交换，然后重定向回去。 | `callback_page` |
| `logout_endpoint` | `/logout` | 调度当前活跃提供商的退出登录；CSRF 防护阻止跨站退出登录（参阅[默认安全](/docs/enterprise/auth/secure-by-default/#logout-is-protected-against-csrf)）。 | `logout_page` |
| `forbidden_endpoint` | `/forbidden` | 当已认证用户无权查看某页面时显示。 | `forbidden_page` |

路由本身可通过 `login_endpoint`、`logout_endpoint`、`auth_callback_endpoint` 和 `forbidden_endpoint` 进行配置。有关配置身份提供商，请参阅[提供商](/docs/enterprise/auth/providers/)页面；有关插件的整体架构，请参阅[概述](/docs/enterprise/auth/overview/)。

```md alert warning
# 在 IdP 中注册回调 URI
如果你更改了 `auth_callback_endpoint`，请将该确切的 URI 注册为身份提供商的 OAuth 重定向 URI，否则令牌交换将被拒绝。
```

## 页面构建器约定

页面构建器是一个可调用对象，以**关键字参数**的形式接收构建上下文：

| 关键字 | 类型 | 含义 |
| --- | --- | --- |
| `providers` | `Sequence[type[OIDCAuthState]]` | 已解析的提供商状态类。 |
| `plugin` | `AuthPlugin` | 插件实例。 |

命名所需的条目并添加 `**context` 以忽略其余部分：

```python
import reflex as rx


def custom_login_page(providers, **context) -> rx.Component: ...
```

构建器也可以只接受 `**context`。相同的约定适用于登录、回调、退出登录和禁止访问构建器。

## 自定义登录页面

在大多数应用中，将用户引导到 `/login`。当默认登录按钮需要不同的布局时，自定义 `login_page`。

调用每个提供商的 `get_login_button(*children)` 并将可点击元素作为 children 传入。这会保留 OIDC 重定向逻辑和 iframe 弹窗监听器。`provider.display_name()` 返回提供商标签；默认情况下，它是 `__provider__` 值的首字母大写形式：

```python
import reflex as rx


def custom_login_page(providers, **context) -> rx.Component:
    return rx.center(
        rx.vstack(
            rx.heading("Sign in"),
            *[
                provider.get_login_button(
                    rx.button(f"Continue with {provider.display_name()}")
                )
                for provider in providers
            ],
            spacing="4",
        ),
        min_height="60vh",
    )
```

如果有两个或更多提供商，为每个提供商渲染一个 `get_login_button()`。有关弹窗流程要求，请参阅[在 iframe 中运行](/docs/enterprise/auth/providers/#running-inside-an-iframe)。

## 自定义回调和退出登录页面

回调和退出登录路由仅在其插件拥有的 `on_load` 运行时显示一个过渡页面。复用 `providers[0].get_authentication_loading_page()`，它已经在交换（或退出登录）进行时显示验证和重定向状态，失败时还会显示错误视图（参阅[认证失败 UX 和故障排除](#auth-failure-ux-and-troubleshooting)）：

```python
import reflex as rx


def custom_callback_page(providers, **context) -> rx.Component:
    return rx.center(
        rx.vstack(
            rx.text("Completing sign-in…"),
            providers[0].get_authentication_loading_page(),
        ),
        min_height="60vh",
    )


def custom_logout_page(providers, **context) -> rx.Component:
    return providers[0].get_authentication_loading_page()
```

当过渡页面需要品牌化时，将该视图包装在应用特定的布局中。

## 认证失败 UX 和故障排除

当令牌交换或验证失败时，`get_authentication_loading_page()` 会将加载动画替换为错误视图：一条面向用户的消息加上一个**错误 ID**（每个流程的 UUID），用户可以将它提供给技术支持。相同的失败会在后端以 `ERROR` 级别记录日志，前缀为 `<client_token> [txid=<id>]`。即使应用未配置日志，也会输出该日志条目。使用显示的 ID 来查找对应的服务器日志。

```md alert info
# 运维提示
当用户报告登录失败时，在后端日志中搜索他们看到的 ID 对应的 `[txid=...]`。
```

页面构建器**不**接受错误覆盖参数。`default_callback_page` 调用 `get_authentication_loading_page()`。有两种受支持的方式来自定义失败 UI：

**1. 覆盖状态类方法。** 继承你的提供商状态并覆盖 `get_error_component`、`get_authentication_error_component` 或 `get_logout_error_component`。加载页面会自动获取覆盖：

```python
import reflex as rx
from reflex_enterprise.auth import GenericOIDCAuthState


class MyProviderState(GenericOIDCAuthState):
    @classmethod
    def get_error_component(cls, operation, suggestion, error_id) -> rx.Component:
        return rx.vstack(
            rx.heading("Something went wrong"),
            rx.text(suggestion),
            rx.text("Error ID: ", rx.badge(error_id)),
        )
```

**2. 手写一个读取公开 Var 的页面。** `has_error`、`user_error_message` 和 `last_error_txid` 是提供商状态上的公开 Var，自定义回调或退出登录构建器可以直接根据它们进行分支：

```python
import reflex as rx


def custom_callback_page(providers, **context) -> rx.Component:
    provider = providers[0]
    return rx.center(
        rx.cond(
            provider.has_error,
            rx.vstack(
                rx.heading("Sign-in failed"),
                rx.text(provider.user_error_message),
                rx.text("Error ID: ", rx.badge(provider.last_error_txid)),
            ),
            provider.get_authentication_loading_page(),
        ),
        min_height="60vh",
    )
```

## 自定义禁止访问页面

当**已认证**用户尝试加载其无权查看的页面时，会显示 `/forbidden`。这发生在全局默认 `AuthPlugin(auth=...)` 是一个可调用检查且在页面加载时失败的情况下。禁止访问页面没有插件拥有的 `on_load`。

```python
import reflex as rx


def custom_forbidden_page(**context) -> rx.Component:
    return rx.center(
        rx.vstack(
            rx.heading("403", size="8"),
            rx.text("Access denied."),
            rx.link("Back to home", href="/"),
            spacing="3",
            align="center",
        ),
        min_height="60vh",
    )
```

```md alert info
# 禁止访问页面何时出现？
仅在已认证用户通过可调用全局默认检查失败的**页面**加载时出现。事件处理器检查失败会显示 `"Action not allowed"` 提示。字段和变量检查失败会隐藏值。两者都不会导航到 `/forbidden`。参阅[认证与授权](/docs/enterprise/auth/secure-by-default/#authentication-vs-authorization)。
```

## 接入配置

在 `rxconfig.py` 中以**导入路径字符串**（`"module.function"`）的形式将构建器传递给插件。构建器模块会导入 `reflex_enterprise`，而后者在导入时加载 `rxconfig`。在 `rxconfig.py` 中直接导入它们会导致重新进入配置。导入路径字符串在编译时延迟解析：

```python
import reflex_enterprise as rxe

config = rxe.Config(
    app_name="my_app",
    plugins=[
        rxe.AuthPlugin(
            login_page="my_app.auth_pages.custom_login_page",
            callback_page="my_app.auth_pages.custom_callback_page",
            logout_page="my_app.auth_pages.custom_logout_page",
            forbidden_page="my_app.auth_pages.custom_forbidden_page",
        ),
    ],
)
```

```md alert info
# rxconfig 中用字符串，其他地方用可调用对象
导入路径字符串仅在 `rxconfig.py` 中是必需的。在构建器已经可以导入的地方，直接传递可调用对象：`login_page=custom_login_page`。
```

## 默认值

省略构建器时，插件会回退到 `reflex_enterprise.auth.pages` 中的默认值：

| 构建器参数 | 默认值 | 渲染内容 |
| --- | --- | --- |
| `login_page` | `default_login_page` | 每个提供商一个 `provider.get_login_button()`。 |
| `callback_page` | `default_callback_page` | `providers[0].get_authentication_loading_page()`。 |
| `logout_page` | `default_logout_page` | `providers[0].get_authentication_loading_page()`。 |
| `forbidden_page` | `default_forbidden_page` | 403 拒绝访问视图。 |

默认值接受相同的关键字上下文。自定义构建器可以调用默认构建器并包装返回的内容：

```python
import reflex as rx
from reflex_enterprise.auth import default_login_page


def custom_login_page(providers, **context) -> rx.Component:
    return rx.center(default_login_page(providers=providers, **context))
```

## 相关内容

- [提供商](/docs/enterprise/auth/providers/)：身份提供商配置。
- [默认安全](/docs/enterprise/auth/secure-by-default/)：受保护的界面和 `/forbidden` 行为。
- [测试](/docs/enterprise/auth/testing/)：受保护界面的测试。
