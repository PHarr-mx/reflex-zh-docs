```python exec
import reflex as rx
```

# 条件渲染（Conditional Rendering）

回顾[基础](/docs/getting-started/basics)部分，在 Reflex 中引用状态变量时不能使用 Python `if/else` 语句。相反，使用 `rx.cond` 组件根据状态变量的值有条件地渲染组件或设置属性。

```md video https://youtube.com/embed/ITOZkzjtjUA?start=6040&end=6463
# 视频：条件渲染
```

```md alert
# 查看 [cond 文档](/docs/library/dynamic-rendering/cond)的 API 参考。
```

```python eval
rx.box(height="2em")
```

下面是一个简单的示例，展示如何通过检查状态变量 `show` 的值在两个文本组件之间切换。

```python demo exec
class CondSimpleState(rx.State):
    show: bool = True

    @rx.event
    def change(self):
        self.show = not (self.show)


def cond_simple_example():
    return rx.vstack(
        rx.button("Toggle", on_click=CondSimpleState.change),
        rx.cond(
            CondSimpleState.show,
            rx.text("Text 1", color="blue"),
            rx.text("Text 2", color="red"),
        ),
    )
```

如果 `show` 为 `True`，则渲染第一个组件（在本例中为蓝色文本）。否则渲染第二个组件（在本例中为红色文本）。

## 条件属性

你也可以使用 `rx.cond` 有条件地设置属性。在此示例中，我们根据状态变量 `show` 的值设置文本组件的 `color` 属性。

```python demo exec
class PropCondState(rx.State):
    value: int

    @rx.event
    def set_end(self, value: list[int | float]):
        self.value = value[0]


def cond_prop():
    return rx.slider(
        default_value=[50],
        on_value_commit=PropCondState.set_end,
        color_scheme=rx.cond(PropCondState.value > 50, "green", "pink"),
        width="100%",
    )
```

## 变量操作

你可以将[变量操作](/docs/vars/var-operations)与 `cond` 组件一起使用，以实现更复杂的条件。有关更多详细信息，请参阅完整的 [cond 参考](/docs/library/dynamic-rendering/cond)。

## 多条件语句

Reflex 中的 [`rx.match`](/docs/library/dynamic-rendering/match) 组件为 `rx.cond` 提供了一个强大的替代方案，用于处理多条件语句和结构化模式匹配。与嵌套的 `rx.cond` 结构相比，此组件允许你以更简洁、更易读的方式处理多个条件及其关联的组件。

```python demo exec
from typing import List

import reflex as rx


class MatchState(rx.State):
    cat_breed: str = ""
    animal_options: List[str] = [
        "persian",
        "siamese",
        "maine coon",
        "ragdoll",
        "pug",
        "corgi",
    ]

    @rx.event
    def set_cat_breed(self, breed: str):
        self.cat_breed = breed


def match_demo():
    return rx.flex(
        rx.match(
            MatchState.cat_breed,
            ("persian", rx.text("Persian cat selected.")),
            ("siamese", rx.text("Siamese cat selected.")),
            (
                "maine coon",
                rx.text("Maine Coon cat selected."),
            ),
            ("ragdoll", rx.text("Ragdoll cat selected.")),
            rx.text("Unknown cat breed selected."),
        ),
        rx.select(
            [
                "persian",
                "siamese",
                "maine coon",
                "ragdoll",
                "pug",
                "corgi",
            ],
            value=MatchState.cat_breed,
            on_change=MatchState.set_cat_breed,
        ),
        direction="column",
        gap="2",
    )
```
