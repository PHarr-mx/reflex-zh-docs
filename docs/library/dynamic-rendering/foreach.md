```python exec
import reflex as rx
```

# 循环渲染（Foreach）

`rx.foreach` 组件接受一个可迭代对象（list、tuple 或 dict）和一个渲染列表中每个项目的函数。
这对于动态渲染状态中定义的项目列表非常有用。

```md alert warning
# 对状态变量使用 `rx.foreach`；对常量使用 Python 列表或字典推导式。
```

```python demo exec
class ForeachState(rx.State):
    colors: list[str] = [
        "#E5484D",
        "#12A594",
        "#3E63DD",
        "#AD5700",
        "#F76B15",
        "#8E4EC6",
    ]


def color_swatch(label: rx.Var[str | int], color: rx.Var[str]):
    return rx.box(
        rx.text(label, color="white", weight="medium"),
        bg=color,
        padding_y="0.5em",
        padding_x="0.75em",
        min_width="5.5em",
        text_align="center",
        border_radius="0.375rem",
        border="1px solid rgba(0, 0, 0, 0.12)",
        box_shadow="0 1px 2px rgba(0, 0, 0, 0.10)",
    )


def colored_box(color: rx.Var[str]):
    return color_swatch(color, color)


def foreach_example():
    return rx.grid(
        rx.foreach(ForeachState.colors, colored_box),
        columns="2",
    )
```

该函数还可以接受索引作为第二个参数。

```python demo exec
def colored_box_index(color: rx.Var[str], index: rx.Var[int]):
    return color_swatch(index, color)


def foreach_example_index():
    return rx.grid(
        rx.foreach(
            ForeachState.colors, lambda color, index: colored_box_index(color, index)
        ),
        columns="2",
    )
```

嵌套的 foreach 组件可用于渲染嵌套列表。

当索引嵌套列表时，声明列表的类型很重要，因为 Reflex 需要它来进行类型检查。
这确保了在用户遇到潜在的前端 JS 错误之前就能捕获它们。

```python demo exec
class NestedForeachState(rx.State):
    numbers: list[list[str]] = [["1", "2", "3"], ["4", "5", "6"], ["7", "8", "9"]]


def display_row(row: rx.Var[list[str]]):
    return rx.hstack(
        rx.foreach(
            row,
            lambda item: rx.box(
                item,
                border="1px solid black",
                padding="0.5em",
            ),
        ),
    )


def nested_foreach_example():
    return rx.vstack(rx.foreach(NestedForeachState.numbers, display_row))
```

下面是一个在待办事项列表中使用 foreach 的更复杂示例。

```python demo exec
from dataclasses import dataclass, field
from uuid import UUID, uuid4


@dataclass
class TodoItem:
    text: str
    id: UUID = field(default_factory=uuid4)


class ListState(rx.State):
    items: list[TodoItem] = [
        TodoItem(text="Write Code"),
        TodoItem(text="Sleep"),
        TodoItem(text="Have Fun"),
    ]
    new_item: str = ""

    @rx.event
    def set_new_item(self, new_item: str):
        self.new_item = new_item

    @rx.event
    def add_item(self):
        self.items += [TodoItem(text=self.new_item)]

    @rx.event
    def finish_item(self, item_id: UUID):
        self.items = [item for item in self.items if item.id != item_id]


def get_item(item: rx.Var[TodoItem]):
    return rx.list.item(
        rx.hstack(
            rx.button(
                "Done",
                on_click=lambda: ListState.finish_item(item.id),
                size="1",
                variant="soft",
            ),
            rx.text(item.text, font_size="1.25em"),
        ),
        key=item.id,
    )


def todo_example():
    return rx.vstack(
        rx.heading("Todos", as_="h2"),
        rx.input(
            on_blur=ListState.set_new_item, placeholder="Add a todo...", bg="white"
        ),
        rx.button("Add", on_click=ListState.add_item),
        rx.divider(),
        rx.list.ordered(
            rx.foreach(
                ListState.items,
                get_item,
            ),
        ),
        bg="#ededed",
        padding="1em",
        border_radius="0.5em",
        shadow="lg",
    )
```

## 字典

字典中的项目以键值对的形式传递给渲染函数。
当遍历字典时，即使 Python 字典使用其他键类型，键在 `foreach` 回调中也会被强制转换为字符串。
使用颜色示例，我们可以稍微修改代码以使用字典，如下所示。

```python demo exec
class SimpleDictForeachState(rx.State):
    color_chart: dict[int, str] = {1: "#3E63DD", 2: "#E5484D", 3: "#12A594"}


def display_color(color: rx.Var[tuple[str, str]]):
    # color 以键值对形式呈现，例如 ("1", "#3E63DD")。
    return color_swatch(color[0], color[1])


def foreach_dict_example():
    return rx.grid(
        rx.foreach(SimpleDictForeachState.color_chart, display_color), columns="2"
    )
```

现在让我们用颜色示例展示一个使用字典的更复杂示例。
此示例将相关的十六进制颜色分组到字典中，并将键和值都渲染为色块：

```python demo exec
class ComplexDictForeachState(rx.State):
    color_chart: dict[str, list[str]] = {
        "#8E4EC6": ["#E5484D", "#3E63DD"],
        "#F76B15": ["#AD5700", "#E5484D"],
        "#12A594": ["#3E63DD", "#AD5700"],
    }


def display_colors(color: rx.Var[tuple[str, list[str]]]):
    return rx.vstack(
        color_swatch(color[0], color[0]),
        rx.hstack(
            rx.foreach(
                color[1],
                lambda x: color_swatch(x, x),
            )
        ),
        align="center",
        spacing="2",
    )


def foreach_complex_dict_example():
    return rx.grid(
        rx.foreach(ComplexDictForeachState.color_chart, display_colors),
        columns="3",
        spacing="4",
    )
```

## API 参考

### `rx.foreach`

```python
rx.foreach(iterable, render_fn)
```

- `iterable`：要渲染的状态变量或可迭代对象。支持列表、元组、集合、字符串和字典；字典以字符串键的键值元组形式传递给 `render_fn`。
- `render_fn`：为每个项目返回组件的函数。它接收项目作为第一个 `rx.Var[...]` 参数，并可选地接收索引作为第二个 `rx.Var[int]` 参数。
