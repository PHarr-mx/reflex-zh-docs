```python exec
import reflex as rx


def definition(title, *children):
    return rx.vstack(
        rx.heading(
            title,
            as_="h2",
            font_size="1em",
            font_weight="bold",
            color=rx.color("mauve", 12),
        ),
        *children,
        color=rx.color("mauve", 10),
        padding="1em",
        border=f"1px solid {rx.color('mauve', 4)}",
        background_color=rx.color("mauve", 2),
        border_radius="8px",
        _hover={
            "border": f"1px solid {rx.color('mauve', 5)}",
            "background_color": rx.color("mauve", 3),
        },
        align_items="start",
    )
```

# 状态（State）

状态允许我们创建可以响应用户输入的交互式应用。
它定义了可以随时间变化的变量，以及可以修改它们的函数。

```md video https://youtube.com/embed/ITOZkzjtjUA?start=1206&end=1869
# 视频：状态概述
```

## 状态基础

你可以通过创建一个继承自 `rx.State` 的类来定义状态：

```python
import reflex as rx


class State(rx.State):
    """在此定义你的应用状态。"""
```

状态类由两部分组成：变量（vars）和事件处理程序（event handlers）。

**变量（Vars）** 是应用中可以随时间变化的变量。

**事件处理程序（Event handlers）** 是响应事件修改这些变量的函数。

这些是理解 Reflex 中状态如何工作的主要概念：

```python eval
rx.grid(
    definition(
        "基础变量（Base Var）",
        rx.list.unordered(
            rx.list.item("应用中可以随时间变化的任何变量。"),
            rx.list.item("定义为 ", rx.code("State"), " 类中的字段"),
            rx.list.item("只能由事件处理程序修改。"),
        ),
    ),
    definition(
        "计算变量（Computed Var）",
        rx.list.unordered(
            rx.list.item("基于其他变量自动变化的变量。"),
            rx.list.item(
                "使用 ",
                rx.code("@rx.var"),
                " 装饰器定义为函数。",
            ),
            rx.list.item(
                "不能由事件处理程序设置，在状态变化时始终重新计算。"
            ),
        ),
    ),
    definition(
        "事件触发器（Event Trigger）",
        rx.list.unordered(
            rx.list.item(
                "触发事件的用户交互，例如按钮点击。"
            ),
            rx.list.item(
                "定义为特殊的组件属性，例如 ",
                rx.code("on_click"),
                "。",
            ),
            rx.list.item("可用于触发事件处理程序。"),
        ),
    ),
    definition(
        "事件处理程序（Event Handlers）",
        rx.list.unordered(
            rx.list.item("响应事件更新状态的函数。"),
            rx.list.item("定义为 ", rx.code("State"), " 类中的方法。"),
            rx.list.item(
                "可以由事件触发器或其他事件处理程序调用。"
            ),
        ),
    ),
    margin_bottom="1em",
    spacing="2",
    columns="2",
)
```

## 示例

以下是在 Reflex 应用中如何使用状态的示例。
点击文本以更改其颜色。

```python demo exec
class ExampleState(rx.State):
    # 用于循环颜色列表的基础变量。
    colors: list[str] = ["black", "red", "green", "blue", "purple"]

    # 当前颜色索引的基础变量。
    index: int = 0

    @rx.event
    def next_color(self):
        """转到下一个颜色的事件处理程序。"""
        # 事件处理程序可以修改基础变量。
        # 这里我们引用基础变量 `colors` 和 `index`。
        self.index = (self.index + 1) % len(self.colors)

    @rx.var
    def color(self) -> str:
        """返回当前颜色的计算变量。"""
        # 计算变量在状态变化时自动更新。
        return self.colors[self.index]


def index():
    return rx.heading(
        "Welcome to Reflex!",
        as_="h2",
        # 事件处理程序可以绑定到事件触发器。
        on_click=ExampleState.next_color,
        # 状态变量可以绑定到组件属性。
        color=ExampleState.color,
        _hover={"cursor": "pointer"},
    )
```

基础变量是 `colors` 和 `index`。它们是应用中唯一
可以在事件处理程序中直接修改的变量。

有一个计算变量 `color`，它是基础变量的函数。当基础变量变化时，它
将自动计算。

heading 组件将其 `on_click` 事件链接到
`ExampleState.next_color` 事件处理程序，该处理程序递增颜色索引。

```md alert success
# 使用 Reflex，你无需编写 API。

前端和后端之间的所有交互都通过事件处理。
```

```md alert info
# 状态 vs. 实例？

构建应用的 UI 时，通过状态类（`ExampleState`）引用变量和事件处理程序。

编写后端事件处理程序时，通过实例（`self`）访问和设置变量。
```

```md alert warning
# 无法打印状态变量。

代码 `print(ExampleState.index)` 将不起作用，因为状态变量的值仅在编译时才知道。
```

## 客户端状态

每个打开你的应用的用户都有一个唯一的 ID 和他们自己的状态副本。
这意味着每个用户可以独立于其他用户与应用交互并修改状态。

由于 Reflex 在内部为每个用户创建状态的新实例，你的代码应该
永远不要直接初始化状态类。

```md alert info
# 尝试在多个选项卡中打开应用，看看状态如何独立变化。
```

所有用户状态都存储在服务器上，所有事件处理程序都在
服务器上执行。Reflex 使用 websockets 将事件发送到服务器，并将
状态更新发送回客户端。

## 辅助方法

与后端变量类似，在 State 类中定义的任何以下划线 `_` 开头的方法都被视为辅助方法。这些方法不能用作
事件触发器，但可以从状态内的其他事件处理程序方法中调用。

只应在后端可用的功能，例如
经过认证的操作，应使用辅助方法以确保它不会被客户端意外
或恶意触发。
