```python exec
import reflex as rx
```

# 基础

**约 10 分钟** · 介绍你构建 Reflex 应用时最常用的概念。

```md section
# 你将学会如何：

- 创建和嵌套组件
- 自定义组件并设置样式
- 区分编译时（compile-time）与运行时（runtime）
- 显示随时间变化的数据
- 响应事件并更新界面
- 渲染条件和列表
- 创建页面并在它们之间导航
```

如果你还没有安装，请先[安装 Reflex](/docs/getting-started/installation) 再继续。下面的每个示例都将库导入为 `rx`：

```python
import reflex as rx
```

## 创建和嵌套组件

[组件](/docs/ui/overview)是应用用户界面（UI）的构建单元。它们是构成应用的可视化元素，例如按钮、文本和图片。Reflex 提供了丰富的[内置组件](/docs/library)，帮助你快速上手。

组件通过返回组件对象的函数来创建。

```python demo exec
def my_button():
    return rx.button("Click Me")
```

组件可以相互嵌套，以构建复杂的 UI。

要将组件作为子组件嵌套，请将它们作为位置参数传递给父组件。在下面的示例中，`rx.text` 和 `my_button` 组件是 `rx.box` 组件的子组件。

```python demo exec
def my_container():
    return rx.box(
        rx.text("This is a page"),
        # Reference components defined in other functions.
        my_button(),
    )
```

你也可以通过 [rx.el](/docs/library/html/) 命名空间使用任何基础 HTML 元素。这让你可以在需要更多控制权、或 Reflex 组件库中没有特定组件时，直接在 Reflex 应用中使用标准 HTML 元素。

```python demo exec
def my_div():
    return rx.el.div(
        rx.el.p("Use base html!"),
    )
```

如果你需要 Reflex 未提供的组件，可以查看[第三方生态系统](/docs/custom-components)，或[包装你自己的 React 组件](/docs/wrapping-react/library-and-tags)。

## 自定义组件并设置样式

组件可以使用 [props](/docs/components/props) 进行自定义，props 以关键字参数的形式传递给组件函数。

每个组件都有该组件特有的 props。请查阅你所用组件的文档，了解有哪些可用的 props。

```python demo exec
def half_filled_progress():
    return rx.progress(value=50)
```

除了组件特有的 props 之外，组件还可以使用作为 props 传递的 CSS 属性来设置样式。

```python demo exec
def round_button():
    return rx.button("Click Me", border_radius="15px", font_size="18px")
```

```md alert
请使用 CSS 属性名的 `snake_case` 版本作为 prop 名称。
```

有关如何为组件设置样式的更多信息，请参见[样式指南](/docs/styling/overview)

总而言之，组件由子组件（children）和 props 构成。

```md definition
# Children（子组件）

- 嵌套在组件内部的文本或其他 Reflex 组件。
- 作为**位置参数**传递。

# Props

- 影响组件行为和外观的属性。
- 作为**关键字参数**传递。
```

## 显示随时间变化的数据

应用需要存储并显示随时间变化的数据。Reflex 通过 [State](/docs/state/overview) 来处理这一点，State 是一个 Python 类，用于存储在应用运行时可能发生变化的变量，以及能够修改这些变量的函数。

要定义一个 state 类，请继承 `rx.State` 并定义用于存储应用状态的字段。状态变量（[vars](/docs/vars/base-vars)）应当带有类型注解，并可以用默认值进行初始化。

```python
class MyState(rx.State):
    count: int = 0
```

### 在组件中引用状态 vars

要在组件中引用状态 var，你可以将它作为子组件或 prop 传递。当状态发生变化时，组件会自动更新。

Vars 通过 state 类上的类属性来引用。例如，要在组件中引用 `count` var，请使用 `MyState.count`。

```python demo exec
class MyState(rx.State):
    count: int = 0
    color: str = "red"


def counter():
    return rx.hstack(
        # The heading `color` prop is set to the `color` var in MyState.
        rx.heading("Count: ", as_="h2", color=MyState.color),
        # The `count` var in `MyState` is passed as a child to the heading component.
        rx.heading(MyState.count, as_="h2"),
    )
```

Vars 可以在多个组件中被引用，并且会在状态变化时自动更新。

## 响应事件并更新界面

到目前为止，我们已经定义了状态 vars，但还没有展示如何修改它们。所有的状态更改都通过 state 类中的函数来处理，这些函数称为[事件处理器（event handlers）](/docs/events/events-overview)。

```md alert
事件处理器是在 Reflex 中修改状态的**唯一**方式。
```

组件有一些特殊的 props，例如 `on_click`，称为事件触发器（event triggers），可用于让组件具有交互性。事件触发器将组件与事件处理器连接起来，由事件处理器更新状态。

```python demo exec
class CounterState(rx.State):
    count: int = 0

    @rx.event
    def increment(self):
        self.count += 1


def counter_increment():
    return rx.hstack(
        rx.heading(CounterState.count, as_="h2"),
        rx.button("Increment", on_click=CounterState.increment),
    )
```

当事件触发器被激活时，对应的事件处理器会被调用，从而更新状态。UI 会自动重新渲染以反映新的状态。

```md alert info
# `@rx.event` 装饰器是什么？

强烈建议在事件处理器上方添加 `@rx.event` 装饰器。该装饰器能够启用正确的静态类型检查，确保事件处理器接收到正确数量和类型的参数。这一特性是在 Reflex 0.6.5 版本中引入的。
```

### 带参数的事件处理器

事件处理器也可以接收参数。例如，`increment` 事件处理器可以接收一个参数，用于按指定的数量增加计数。

```python demo exec
class CounterState2(rx.State):
    count: int = 0

    @rx.event
    def increment(self, amount: int):
        self.count += amount


def counter_variable():
    return rx.hstack(
        rx.heading(CounterState2.count, as_="h2"),
        rx.button("Increment by 1", on_click=lambda: CounterState2.increment(1)),
        rx.button("Increment by 5", on_click=lambda: CounterState2.increment(5)),
    )
```

这里的 `on_click` 事件触发器不传递任何参数，但有些事件触发器会传递参数。例如，`on_blur` 事件触发器会将输入框的文本作为参数传递给事件处理器。

```python demo exec
class TextState(rx.State):
    text: str = ""

    @rx.event
    def update_text(self, new_text: str):
        self.text = new_text


def text_input():
    return rx.vstack(
        rx.heading(TextState.text, as_="h2"),
        rx.input(default_value=TextState.text, on_blur=TextState.update_text),
    )
```

```md alert
请确保事件处理器的参数数量与事件触发器一致，否则会抛出错误。
```

## 编译时与运行时

在深入探讨状态之前，理解 Reflex 中编译时（compile-time）与运行时（runtime）的区别非常重要。

当你运行应用时，前端会被编译为在浏览器中运行的 Javascript 代码（编译时）。后端则保持为 Python，并在应用的整个生命周期内在服务器上运行（运行时）。

### 什么时候不能使用纯 Python？

我们无法编译任意 Python 代码，只能编译你所定义的组件。这意味着，重要的是，你不能在组件中对状态 vars 使用任意的 Python 操作和函数。

不过，由于 state 中的任何事件处理器都位于后端，因此你**可以在 state 中使用任何 Python 代码或库**。

### 可行的示例

在事件处理器中，可以使用任何 Python 代码或库。

```python demo exec
def check_even(num: int):
    return num % 2 == 0


class MyState3(rx.State):
    count: int = 0
    text: str = "even"

    @rx.event
    def increment(self):
        # Use any Python code within state.
        # Even reference functions defined outside the state.
        if check_even(self.count):
            self.text = "even"
        else:
            self.text = "odd"
        self.count += 1


def count_and_check():
    return rx.box(
        rx.heading(MyState3.text, as_="h2"),
        rx.button("Increment", on_click=MyState3.increment),
    )
```

只要函数是在编译时定义的（即不引用任何状态 var），就可以在组件中使用任何 Python 函数。

```python demo exec
def show_numbers():
    return rx.vstack(*[rx.hstack(i, check_even(i)) for i in range(10)])
```

### 不可行的示例

你不能在组件中对 vars 使用 `if` 语句，因为其值在编译时是未知的。

```python
class BadState(rx.State):
    count: int = 0


def count_if_even():
    return rx.box(
        rx.heading("Count: ", as_="h2"),
        # This will raise a compile error, as BadState.count is a var and not known at compile time.
        rx.text(BadState.count if BadState.count % 2 == 0 else "Odd"),
        # Using an if statement with a var as a prop will NOT work either.
        rx.text("hello", color="red" if BadState.count % 2 == 0 else "blue"),
    )
```

你不能对 vars 列表使用 `for` 循环。

```python
class BadState(rx.State):
    items: list[str] = ["Apple", "Banana", "Cherry"]


def loop_over_list():
    return rx.box(
        # This will raise a compile error, as BadState.items is a list and not known at compile time.
        *[rx.text(item) for item in BadState.items]
    )
```

你不能在组件中对状态 vars 使用任意的 Python 操作。

```python
class BadTextState(rx.State):
    text: str = "Hello world"


def format_text():
    return rx.box(
        # Python operations such as `len` will not work on state vars.
        rx.text(len(BadTextState.text)),
    )
```

在接下来的几节中，我们将展示如何处理这些情况。

## 条件渲染

如前所述，你不能在组件中对状态 vars 使用 Python 的 `if/else` 语句。请改用 [rx.cond](/docs/components/conditional-rendering) 函数来有条件地渲染组件。

```python demo exec
class LoginState(rx.State):
    logged_in: bool = False

    @rx.event
    def toggle_login(self):
        self.logged_in = not self.logged_in


def show_login():
    return rx.box(
        rx.cond(
            LoginState.logged_in,
            rx.heading("Logged In", as_="h2"),
            rx.heading("Not Logged In", as_="h2"),
        ),
        rx.button("Toggle Login", on_click=LoginState.toggle_login),
    )
```

## 渲染列表

要遍历一个作为列表的 var，请使用 [rx.foreach](/docs/components/rendering-iterables) 函数来渲染组件列表。

将列表 var 和一个返回组件的函数作为参数传递给 `rx.foreach`。

```python demo exec
class ListState(rx.State):
    items: list[str] = ["Apple", "Banana", "Cherry"]


def render_item(item: rx.Var[str]):
    """Render a single item."""
    # Note that item here is a Var, not a str!
    return rx.list.item(item)


def show_fruits():
    return rx.box(
        rx.foreach(ListState.items, render_item),
    )
```

渲染每个元素的函数接收的是一个 `Var`，因为它会在编译阶段被预先编译。

## Var 操作

你不能在组件中对状态 vars 使用任意的 Python 操作，但 Reflex 提供了 [var 操作](/docs/vars/var-operations)，可用于操作状态 vars。

例如，要检查一个 var 是否为偶数，你可以使用 `%` 和 `==` var 操作。

```python demo exec
class CountEvenState(rx.State):
    count: int = 0

    @rx.event
    def increment(self):
        self.count += 1


def count_if_even():
    return rx.box(
        rx.heading("Count: ", as_="h2"),
        rx.cond(
            # Here we use the `%` and `==` var operations to check if the count is even.
            CountEvenState.count % 2 == 0,
            rx.text("Even"),
            rx.text("Odd"),
        ),
        rx.button("Increment", on_click=CountEvenState.increment),
    )
```

## 应用与页面

Reflex 应用通过实例化 `rx.App` 类来创建。页面与特定的 URL 路由相关联，并通过定义一个返回组件的函数来创建。

```python
def index():
    return rx.text("Root Page")


app = rx.App()
app.add_page(index, route="/")
```

## 下一步

你已经掌握了核心要素——组件、状态、事件、编译时与运行时。是时候动手构建了。

```md alert info
# 构建一个真实的应用

- [仪表盘教程](/docs/getting-started/dashboard-tutorial)——一个包含表格、表单和状态的数据应用。
- [聊天应用教程](/docs/getting-started/chatapp-tutorial)——端到端的流式 AI 响应。
```

```md alert info
# 深入理解

- [Vars](/docs/vars/base-vars) 和 [var 操作](/docs/vars/var-operations)——完整的 API。
- [事件](/docs/events/events-overview) 和 [页面](/docs/pages/overview)——路由、触发器、处理器。
- [Reflex 工作原理](/docs/advanced-onboarding/how-reflex-works)——什么在哪里运行，以及为什么。
```
