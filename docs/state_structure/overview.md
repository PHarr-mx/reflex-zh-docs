```python exec
import reflex as rx
from typing import Any
```

# 子状态（Substates）

子状态允许你将状态分解为多个类，使其更易于管理。随着应用的增长，这非常有用，
因为它允许你将每个页面视为一个独立的实体。子状态还允许你共享公共状态
资源，例如变量或事件处理程序。

当特定的状态类变得太大时，将其分解为多个子状态可以带来性能
优势，因为它只加载用于处理特定事件的状态部分。

## 多状态

一种常见的模式是为应用中的每个页面创建一个子状态。
这允许你将每个页面视为一个独立的实体，并使你的代码随着应用的增长更易于管理。

要创建子状态，只需多次继承 `rx.State`：

```python
# index.py
import reflex as rx


class IndexState(rx.State):
    """在此定义你的主状态。"""

    data: str = "Hello World"


@rx.page()
def index():
    return rx.box(rx.text(IndexState.data))


# signup.py
import reflex as rx


class SignupState(rx.State):
    """在此定义你的注册状态。"""

    username: str = ""
    password: str = ""

    def signup(self): ...


@rx.page()
def signup_page():
    return rx.box(
        rx.input(value=SignupState.username),
        rx.input(value=SignupState.password),
    )


# login.py
import reflex as rx


class LoginState(rx.State):
    """在此定义你的登录状态。"""

    username: str = ""
    password: str = ""

    def login(self): ...


@rx.page()
def login_page():
    return rx.box(
        rx.input(value=LoginState.username),
        rx.input(value=LoginState.password),
    )
```

分离状态纯粹是组织问题。你仍然可以通过导入状态类从其他页面访问状态。

```python
# index.py

import reflex as rx

from signup import SignupState

...


def index():
    return rx.box(
        rx.text(IndexState.data),
        rx.input(value=SignupState.username),
        rx.input(value=SignupState.password),
    )
```

## 访问任意状态

特定状态中的事件处理程序可以通过调用
`get_state` 异步方法并传递所需的状态类来访问和修改另一个状态实例中的变量。如果请求的状态尚未加载，
它将按需加载和反序列化。

在以下示例中，`GreeterState` 访问 `SettingsState` 以获取 `salutation` 并使用它
来更新 `message` 变量。

值得注意的是，设置 salutation 的小部件在处理
输入 `on_change` 事件时不必加载 `GreeterState`，这提高了性能。

```python demo exec
class SettingsState(rx.State):
    salutation: str = "Hello"

    def set_salutation(self, value: str):
        self.salutation = value


def set_salutation_popover():
    return rx.popover.root(
        rx.popover.trigger(
            rx.icon_button(rx.icon("settings")),
        ),
        rx.popover.content(
            rx.input(
                value=SettingsState.salutation, on_change=SettingsState.set_salutation
            ),
        ),
    )


class GreeterState(rx.State):
    message: str = ""

    @rx.event
    async def handle_submit(self, form_data: dict[str, Any]):
        settings = await self.get_state(SettingsState)
        self.message = f"{settings.salutation} {form_data['name']}"


def index():
    return rx.vstack(
        rx.form(
            rx.vstack(
                rx.hstack(
                    rx.input(placeholder="Name", id="name"),
                    set_salutation_popover(),
                ),
                rx.button("Submit"),
            ),
            reset_on_submit=True,
            on_submit=GreeterState.handle_submit,
        ),
        rx.text(GreeterState.message),
    )
```

### 访问单个变量值

除了使用 `get_state` 访问整个状态实例外，你还可以使用 `get_var_value` 方法检索单个变量值：

```python
# 从另一个状态访问变量值
value = await self.get_var_value(OtherState.some_var)
```

当你只需要特定值而不是加载整个状态时，此异步方法特别有用。在以下情况下，使用 `get_var_value` 可能比 `get_state` 更高效：

1. 你只需要从另一个状态访问单个变量
2. 另一个状态包含大量数据
3. 你想避免将不必要的数据加载到内存中

以下示例演示如何使用 `get_var_value` 在状态之间访问数据：

```python demo exec
# 定义一个保存计数器值的状态
class CounterState(rx.State):
    # 此变量将从另一个状态访问
    count: int = 0

    @rx.event
    async def increment(self):
        # 点击按钮时递增计数器
        self.count += 1


# 定义一个将显示信息的单独状态
class DisplayState(rx.State):
    # 这将显示当前计数值
    message: str = ""

    @rx.event
    async def show_count(self):
        # 使用 get_var_value 仅从 CounterState 访问 count 变量
        # 这比使用 get_state 加载整个状态更高效
        current = await self.get_var_value(CounterState.count)
        self.message = f"Current count: {current}"


def var_value_example():
    return rx.vstack(
        rx.heading("Get Var Value Example", as_="h2"),
        rx.hstack(
            # 此按钮调用 DisplayState.show_count 以显示当前计数
            rx.button("Get Count Value", on_click=DisplayState.show_count),
            # 此按钮调用 CounterState.increment 以增加计数器
            rx.button("Increment", on_click=CounterState.increment),
        ),
        # 显示来自 DisplayState 的消息
        rx.text(DisplayState.message),
        width="100%",
        align="center",
        spacing="4",
    )
```

在此示例中：

1. 我们有两个独立的状态：管理计数器的 `CounterState` 和显示信息的 `DisplayState`
2. 当你点击 "Increment" 时，它调用 `CounterState.increment()` 以增加计数器值
3. 当你点击 "Show Count" 时，它调用 `DisplayState.show_count()`，使用 `get_var_value` 仅从 `CounterState` 检索计数值，而不加载整个状态
4. 然后当前计数显示在消息中

当你有多个需要相互交互但不需要访问彼此所有数据的状态时，此模式非常有用。

如果变量不可检索，`get_var_value` 将引发 `UnretrievableVarValueError`。

## 性能影响

当事件处理程序被调用时，Reflex 不仅会加载包含
事件处理程序的子状态的数据，还会加载其所有子状态和父状态的数据。
如果状态有大量子状态或包含大量数据，它可能会减慢
与该状态相关的事件处理。

为了获得最佳性能，保持扁平结构，大多数子状态类直接继承自 `rx.State`。
只有当父状态包含子状态常用的数据时，才从另一个状态继承。
使用独立的、不连接的状态实现应用的不同部分，确保只有必要的
数据被加载以处理特定页面或组件的事件。

避免在包含大量数据的状态中定义计算变量，因为
带有计算变量的状态总是被加载以确保值被重新计算。
使用计算变量时，最好在直接继承自 `rx.State` 且
没有其他状态继承它的状态中定义它们，以避免加载不必要的数据。
