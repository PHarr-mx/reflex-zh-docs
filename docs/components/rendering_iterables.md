```python exec
import reflex as rx
```

# 渲染可迭代对象（Rendering Iterables）

再次回顾[基础](/docs/getting-started/basics)部分，在 Reflex 中引用状态变量时不能使用 Python `for` 循环。相反，使用 `rx.foreach` 组件从数据集合中渲染组件。

对于应自动滚动以显示最新项目的动态内容，请考虑将 [auto scroll](/docs/library/dynamic-rendering/auto-scroll) 组件与 `rx.foreach` 一起使用。

```python demo exec
class IterState(rx.State):
    color: list[str] = [
        "red",
        "green",
        "blue",
    ]


def colored_box(color: str):
    return rx.button(color, background_color=color)


def dynamic_buttons():
    return rx.vstack(
        rx.foreach(IterState.color, colored_box),
    )
```

以下是使用 lambda 函数的相同示例。

```python
def dynamic_buttons():
    return rx.vstack(
        rx.foreach(IterState.color, lambda color: colored_box(color)),
    )
```

你也可以直接将 lambda 函数与组件一起使用，而无需定义单独的函数。

```python
def dynamic_buttons():
    return rx.vstack(
        rx.foreach(
            IterState.color, lambda color: rx.button(color, background_color=color)
        ),
    )
```

在这个第一个简单示例中，我们遍历一个颜色 `list` 并渲染动态数量的按钮。

`rx.foreach` 函数的第一个参数是你想要遍历的状态变量。第二个参数是一个函数，它接收数据中的一个项目并返回一个组件。在本例中，`colored_box` 函数接收一个颜色并返回一个具有该颜色的按钮。

## For vs Foreach

```md definition
# 常规 For 循环

- 在遍历常量时使用。

# Foreach

- 在遍历状态变量时使用。
```

上面的示例本可以使用常规 Python `for` 循环编写，因为数据是常量。

```python demo exec
colors = ["red", "green", "blue"]


def dynamic_buttons_for():
    return rx.vstack(
        [colored_box(color) for color in colors],
    )
```

然而，一旦你需要数据是动态的，就必须使用 `rx.foreach`。

```python demo exec
class DynamicIterState(rx.State):
    color: list[str] = [
        "red",
        "green",
        "blue",
    ]

    def add_color(self, form_data):
        self.color.append(form_data["color"])


def dynamic_buttons_foreach():
    return rx.vstack(
        rx.foreach(DynamicIterState.color, colored_box),
        rx.form(
            rx.input(name="color", placeholder="Add a color"),
            rx.button("Add"),
            on_submit=DynamicIterState.add_color,
        ),
    )
```

## 渲染函数

渲染每个项目的函数可以定义为单独的函数或 lambda 函数。在下面的示例中，我们单独定义函数 `colored_box` 并将其传递给 `rx.foreach` 函数。

```python demo exec
class IterState2(rx.State):
    color: list[str] = [
        "red",
        "green",
        "blue",
    ]


def colored_box(color: rx.Var[str]):
    return rx.button(color, background_color=color)


def dynamic_buttons2():
    return rx.vstack(
        rx.foreach(IterState2.color, colored_box),
    )
```

注意 `colored_box` 函数中 `color` 参数的类型注解是 `rx.Var[str]`（而不仅仅是 `str`）。这是因为 `rx.foreach` 函数将项目作为 `Var` 对象传递，它是实际值的包装器。这使我们能够在不知道状态变量实际值（仅在运行时才知道）的情况下编译前端。

## 枚举可迭代对象

该函数还可以将索引作为第二个参数，这意味着我们可以像下面的示例一样枚举数据。

```python demo exec
class IterIndexState(rx.State):
    color: list[str] = [
        "red",
        "green",
        "blue",
    ]


def create_button(color: rx.Var[str], index: int):
    return rx.box(
        rx.button(f"{index + 1}. {color}"),
        padding_y="0.5em",
    )


def enumerate_foreach():
    return rx.vstack(
        rx.foreach(IterIndexState.color, create_button),
    )
```

以下是使用 lambda 函数的相同示例。

```python
def enumerate_foreach():
    return rx.vstack(
        rx.foreach(
            IterIndexState.color, lambda color, index: create_button(color, index)
        ),
    )
```

## 遍历字典

我们可以使用 `foreach` 遍历 `dict`。当字典传递给渲染每个项目的函数时，它以键值对列表的形式呈现 `[("sky", "blue"), ("balloon", "red"), ("grass", "green")]`。

```python demo exec
class SimpleDictIterState(rx.State):
    color_chart: dict[str, str] = {
        "sky": "blue",
        "balloon": "red",
        "grass": "green",
    }


def display_color(color: list):
    # color 以键值对列表的形式呈现 [("sky", "blue"), ("balloon", "red"), ("grass", "green")]
    return rx.box(rx.text(color[0]), bg=color[1], padding_x="1.5em")


def dict_foreach():
    return rx.grid(
        rx.foreach(
            SimpleDictIterState.color_chart,
            display_color,
        ),
        columns="3",
    )
```

```md alert warning
# 字典类型注解。

在状态定义中为字典提供正确的完整类型注解（例如 `dict[str, str]` 而不是 `dict`）至关重要，以确保 `rx.foreach` 按预期工作。正确的类型允许 Reflex 在渲染期间推断和验证数据的结构。
```

## 嵌套示例

`rx.foreach` 可以与嵌套状态变量一起使用。这里我们使用嵌套的 `foreach` 组件来渲染嵌套状态变量。`project_item` 函数内部的 `rx.foreach(project["technologies"], get_badge)` 渲染类型为 `list` 的 `dict` 值。`projects_example` 函数内部的 `rx.box(rx.foreach(NestedStateFE.projects, project_item))` 渲染总体状态变量 `projects` 内的每个 `dict`。

```python demo exec
class NestedStateFE(rx.State):
    projects: list[dict[str, list]] = [
        {
            "technologies": [
                "Next.js",
                "Prisma",
                "Tailwind",
                "Google Cloud",
                "Docker",
                "MySQL",
            ]
        },
        {"technologies": ["Python", "Flask", "Google Cloud", "Docker"]},
    ]


def get_badge(technology: rx.Var[str]) -> rx.Component:
    return rx.badge(technology, variant="soft", color_scheme="green")


def project_item(project: rx.Var[dict[str, list]]) -> rx.Component:
    return rx.box(
        rx.hstack(rx.foreach(project["technologies"], get_badge)),
    )


def projects_example() -> rx.Component:
    return rx.box(rx.foreach(NestedStateFE.projects, project_item))
```

如果你想要一个字典中并非所有值都是相同类型的示例，请查看[使用 foreach 的变量操作](/docs/vars/var-operations)上的示例。

以下是如何使用 `foreach` 处理嵌套数据结构的进一步示例。

```python demo exec
class NestedDictIterState(rx.State):
    color_chart: dict[str, list[str]] = {
        "purple": ["red", "blue"],
        "orange": ["yellow", "red"],
        "green": ["blue", "yellow"],
    }


def display_colors(color: rx.Var[tuple[str, list[str]]]):
    return rx.vstack(
        rx.text(color[0], color=color[0]),
        rx.hstack(
            rx.foreach(
                color[1],
                lambda x: rx.box(rx.text(x, color="black"), bg=x),
            )
        ),
    )


def nested_dict_foreach():
    return rx.grid(
        rx.foreach(
            NestedDictIterState.color_chart,
            display_colors,
        ),
        columns="3",
    )
```

## Foreach 与 Cond

我们也可以将 `foreach` 与 `cond` 组件一起使用。

在此示例中，我们定义函数 `render_item`。此函数接收一个 `item`，使用 `cond` 检查项目是否 `is_packed`。如果已打包，则返回带有 `✔` 的 `item_name`，否则只返回 `item_name`。我们使用 `foreach` 通过 `render_item` 函数遍历 `to_do_list` 中的所有项目。

```python demo exec
import dataclasses


@dataclasses.dataclass
class ToDoListItem:
    item_name: str
    is_packed: bool


class ForeachCondState(rx.State):
    to_do_list: list[ToDoListItem] = [
        ToDoListItem(item_name="Space suit", is_packed=True),
        ToDoListItem(item_name="Helmet", is_packed=True),
        ToDoListItem(item_name="Back Pack", is_packed=False),
    ]


def render_item(item: rx.Var[ToDoListItem]):
    return rx.cond(
        item.is_packed,
        rx.list.item(item.item_name + " ✔"),
        rx.list.item(item.item_name),
    )


def packing_list():
    return rx.vstack(
        rx.text("Sammy's Packing List"),
        rx.list(rx.foreach(ForeachCondState.to_do_list, render_item)),
    )
```
