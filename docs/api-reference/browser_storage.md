# 浏览器存储（Browser Storage）

## rx.Cookie

表示作为 cookie 存储在浏览器中的状态变量（Var）。目前仅支持字符串值。

参数

- `name`：客户端 cookie 的名称。
- `path`：cookie 路径。使用 `/` 使 cookie 在所有页面上可访问。
- `max_age`：从客户端接收到 cookie 起的相对最大存活时间（以秒为单位）。
- `domain`：cookie 的域（例如 `sub.domain.com` 或 `.allsubdomains.com`）。
- `secure`：cookie 是否只能通过 HTTPS 访问。
- `same_site`：cookie 是否随第三方请求发送。可以是（`True`、`False`、`None`、`lax`、`strict`）之一。

```python
class CookieState(rx.State):
    c1: str = rx.Cookie()
    c2: str = rx.Cookie("c2 default")

    # 带有自定义设置的 cookie
    c3: str = rx.Cookie(max_age=2)  # 2 秒后过期
    c4: str = rx.Cookie(same_site="strict")
    c5: str = rx.Cookie(path="/foo/")  # 仅在 `/foo/` 上可访问
    c6: str = rx.Cookie(name="c6-custom-name")
```

```md alert warning
# **Cookie 的默认值永远不会在浏览器中设置！**

Cookie 值仅在变量被赋值时设置。如果你需要设置
默认值，可以在 `on_load` 事件处理程序中为 cookie 赋值。
```

## 访问 Cookie

Cookie 像状态中的任何其他变量一样被访问。如果另一个状态需要访问
cookie 的值，该状态应该是定义 cookie 的状态的子状态。或者可以使用 `get_state` API 来访问另一个状态。

对于在前端渲染 cookie，导入定义 cookie 的状态并
直接引用它。

```md alert warning
# **两个不同的状态应该_避免_定义同名的 `rx.Cookie`。**

虽然技术上可行，但 cookie 选项可能不同，导致
意外的结果。

此外，在一个状态中更新 cookie 值不会自动
更新另一个状态中的值，除非进行页面刷新或导航事件。
```

## rx.remove_cookie

从客户端浏览器中删除 cookie。

参数：

- `key`：要删除的 cookie 名称。

```python
rx.button("Remove cookie", on_click=rx.remove_cookie("key"))
```

此事件也可以从事件处理程序返回：

```python
class CookieState(rx.State):
    ...

    def logout(self):
        return rx.remove_cookie("auth_token")
```

## rx.LocalStorage

表示存储在浏览器 localStorage 中的状态变量。目前仅支持字符串值。

参数

- `name`：客户端存储键的名称。
- `sync`：布尔值，指示状态是否应在同一浏览器的选项卡之间保持同步。

```python
class LocalStorageState(rx.State):
    # 使用默认设置的本地存储
    l1: str = rx.LocalStorage()

    # 使用自定义设置的本地存储
    l2: str = rx.LocalStorage("l2 default")
    l3: str = rx.LocalStorage(name="l3")

    # 在选项卡之间自动更新其他状态的本地存储
    l4: str = rx.LocalStorage(sync=True)
```

### 同步变量

由于 LocalStorage 适用于整个浏览器，所有 LocalStorage 变量
自动在选项卡之间共享。

`sync` 参数控制一个选项卡中的更新是否应主动
传播到其他选项卡，而无需导航或页面刷新事件。

## rx.remove_local_storage

从客户端浏览器中删除本地存储项。

参数

- `key`：要从本地存储中删除的键。

```python
rx.button(
    "Remove Local Storage",
    on_click=rx.remove_local_storage("key"),
)
```

此事件也可以从事件处理程序返回：

```python
class LocalStorageState(rx.State):
    ...

    def logout(self):
        return rx.remove_local_storage("local_storage_state.l1")
```

## rx.clear_local_storage()

清除客户端浏览器中的所有本地存储项。这可能会影响
在同一域中运行的其他应用或应用内使用本地存储的库。

```python
rx.button(
    "Clear all Local Storage",
    on_click=rx.clear_local_storage(),
)
```

## rx.SessionStorage

表示存储在浏览器 sessionStorage 中的状态变量。类似于 localStorage，但数据在页面会话结束时（浏览器/选项卡关闭时）被清除。目前仅支持字符串值。

参数

- `name`：客户端存储键的名称。

```python
class SessionStorageState(rx.State):
    # 使用默认设置的会话存储
    s1: str = rx.SessionStorage()

    # 使用自定义设置的会话存储
    s2: str = rx.SessionStorage("s2 default")
    s3: str = rx.SessionStorage(name="s3")
```

### 会话持久性

SessionStorage 数据在页面会话结束时被清除。页面会话在浏览器打开期间持续存在，并在页面刷新和恢复后继续存在，但在选项卡或浏览器关闭时被清除。

与 LocalStorage 不同，SessionStorage 隔离到创建它的选项卡/窗口，因此不与同一来源的其他选项卡/窗口共享。

## rx.remove_session_storage

从客户端浏览器中删除会话存储项。

参数

- `key`：要从会话存储中删除的键。

```python
rx.button(
    "Remove Session Storage",
    on_click=rx.remove_session_storage("key"),
)
```

此事件也可以从事件处理程序返回：

```python
class SessionStorageState(rx.State):
    ...

    def logout(self):
        return rx.remove_session_storage("session_storage_state.s1")
```

## rx.clear_session_storage()

清除客户端浏览器中的所有会话存储项。这可能会影响
在同一域中运行的其他应用或应用内使用会话存储的库。

```python
rx.button(
    "Clear all Session Storage",
    on_click=rx.clear_session_storage(),
)
```

## 序列化策略

如果应将非平凡的数据结构存储在 `Cookie`、`LocalStorage` 或 `SessionStorage` 变量中，则需要在存储之前和之后对其进行序列化。建议使用 pydantic 类来处理数据，它提供简单的序列化辅助函数，并在复杂的对象结构中递归工作。

```python demo exec
import reflex as rx
import pydantic


class AppSettings(pydantic.BaseModel):
    theme: str = "light"
    sidebar_visible: bool = True
    update_frequency: int = 60
    error_messages: list[str] = pydantic.Field(default_factory=list)


class ComplexLocalStorageState(rx.State):
    data_raw: str = rx.LocalStorage("{}")
    data: AppSettings = AppSettings()
    settings_open: bool = False

    @rx.event
    def save_settings(self):
        self.data_raw = self.data.model_dump_json()
        self.settings_open = False

    @rx.event
    def open_settings(self):
        self.data = AppSettings.model_validate_json(self.data_raw)
        self.settings_open = True

    @rx.event
    def set_field(self, field, value):
        setattr(self.data, field, value)


def app_settings():
    return rx.form.root(
        rx.foreach(
            ComplexLocalStorageState.data.error_messages,
            rx.text,
        ),
        rx.form.field(
            rx.flex(
                rx.form.label(
                    "Theme",
                    rx.input(
                        value=ComplexLocalStorageState.data.theme,
                        on_change=lambda v: ComplexLocalStorageState.set_field(
                            "theme", v
                        ),
                    ),
                ),
                rx.form.label(
                    "Sidebar Visible",
                    rx.switch(
                        checked=ComplexLocalStorageState.data.sidebar_visible,
                        on_change=lambda v: ComplexLocalStorageState.set_field(
                            "sidebar_visible",
                            v,
                        ),
                    ),
                ),
                rx.form.label(
                    "Update Frequency (seconds)",
                    rx.input(
                        value=ComplexLocalStorageState.data.update_frequency,
                        on_change=lambda v: ComplexLocalStorageState.set_field(
                            "update_frequency",
                            v,
                        ),
                    ),
                ),
                rx.dialog.close(rx.button("Save", type="submit")),
                gap=2,
                direction="column",
            )
        ),
        on_submit=lambda _: ComplexLocalStorageState.save_settings(),
    )


def app_settings_example():
    return rx.dialog.root(
        rx.dialog.trigger(
            rx.button("App Settings", on_click=ComplexLocalStorageState.open_settings),
        ),
        rx.dialog.content(
            rx.dialog.title("App Settings"),
            app_settings(),
        ),
    )
```

## 存储类型比较

以下是 Reflex 中不同客户端存储选项的比较：

| 功能             | rx.Cookie                         | rx.LocalStorage             | rx.SessionStorage           |
| ------------------- | --------------------------------- | --------------------------- | --------------------------- |
| 持久性         | 直到 cookie 过期              | 直到显式删除    | 直到浏览器/选项卡关闭 |
| 存储限制       | ~4KB                              | ~5MB                        | ~5MB                        |
| 随请求发送  | 是                               | 否                          | 否                          |
| 可访问性       | 服务器和客户端                   | 仅客户端                 | 仅客户端                 |
| 过期          | 可配置                      | 永不                       | 会话结束              |
| 作用域               | 可配置（域、路径）       | 来源（域）             | 选项卡/窗口                  |
| 跨选项卡同步 | 否                                | 是（使用 sync=True）        | 否                          |
| 用例            | 认证、服务器端状态 | 用户偏好、应用状态 | 临时会话数据      |

## 何时使用每种存储类型

## 使用 rx.Cookie 当：

- 你需要数据在服务器端可访问（cookie 随 HTTP 请求发送）
- 你正在处理用户认证
- 你需要对过期和作用域进行细粒度控制
- 你需要将数据限制在应用中的特定路径

## 使用 rx.LocalStorage 当：

- 你需要存储大量数据（最多约 5MB）
- 你希望数据无限期持久（直到显式删除）
- 你需要在应用的不同选项卡/窗口之间共享数据
- 你想存储应在浏览器会话之间记住的用户偏好

## 使用 rx.SessionStorage 当：

- 你需要在浏览器/选项卡关闭时应清除的临时数据
- 你想将数据隔离到特定选项卡/窗口
- 你正在存储不应在会话结束后持久存在的敏感信息
- 你正在实现按会话的功能，如表单数据、购物车或多步骤流程
- 你想在 Redis 过期后持久化状态的数据（对于需要比 Redis TTL 存活更长时间的服务器端状态）
