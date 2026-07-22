---
components:
  - rx.match
---

```python exec
import reflex as rx
```

# 模式匹配（Match）

Reflex 中的 `rx.match` 功能是处理条件语句的 `rx.cond` 的强大替代方案。
虽然 `rx.cond` 擅长基于单个条件有条件地渲染两个组件，
但 `rx.match` 扩展了这一功能，允许开发者处理多个条件及其关联的组件。
此功能在处理复杂的条件逻辑或嵌套场景时特别有价值，
在这些情况下 `rx.cond` 的局限性可能导致代码可读性降低且更加复杂。

使用 `rx.match`，开发者不仅可以处理多个条件，还可以执行结构化模式匹配，
使其成为管理 Reflex 应用中各种场景的多功能工具。

## 基本用法

`rx.match` 函数提供了清晰且富有表现力的语法，用于处理多个条件及其对应的组件：

```python
rx.match(
    condition,
    (case_1, component_1),
    (case_2, component_2),
    ...
    default_component,
)

```

- `condition`：要匹配的值。
- `(case_i, component_i)`：匹配用例及其对应返回组件的元组。
- `default_component`：当条件不匹配任何匹配用例时的默认组件的特殊情况。

示例

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
            ("maine coon", rx.text("Maine Coon cat selected.")),
            ("ragdoll", rx.text("Ragdoll cat selected.")),
            rx.text("Unknown cat breed selected."),
        ),
        rx.select.root(
            rx.select.trigger(),
            rx.select.content(
                rx.select.group(
                    rx.foreach(
                        MatchState.animal_options, lambda x: rx.select.item(x, value=x)
                    )
                ),
            ),
            value=MatchState.cat_breed,
            on_change=MatchState.set_cat_breed,
        ),
        direction="column",
        gap="2",
    )
```

## 默认情况

`rx.match` 中的默认情况作为通用处理程序，用于处理没有任何指定匹配用例与给定匹配条件对齐的场景。以下是使用默认情况时的关键注意事项：

- **在 Match 函数中的位置**：默认情况必须是 `rx.match` 组件中最后一个非元组参数。
  所有匹配用例都应包含在元组中；任何非元组值都会自动被视为默认情况。例如：

```python
rx.match(
    MatchState.cat_breed,
    ("persian", rx.text("persian cat selected")),
    rx.text("Unknown cat breed selected."),
    ("siamese", rx.text("siamese cat selected")),
)
```

上面的代码片段将由于默认情况位置错误而导致错误。

- **单个默认情况**：`rx.match` 组件中只允许一个默认情况。
  尝试指定多个默认情况将导致错误。例如：

```python
rx.match(
    MatchState.cat_breed,
    ("persian", rx.text("persian cat selected")),
    ("siamese", rx.text("siamese cat selected")),
    rx.text("Unknown cat breed selected."),
    rx.text("Another unknown cat breed selected."),
)
```

- **组件返回值的可选默认情况**：如果 `rx.match` 组件中的匹配用例
  返回组件，则默认情况可以是可选的。在这种情况下，如果未提供默认情况，
  `rx.fragment` 将被隐式分配为默认值。例如：

```python
rx.match(
    MatchState.cat_breed,
    ("persian", rx.text("persian cat selected")),
    ("siamese", rx.text("siamese cat selected")),
)
```

在这种情况下，`rx.fragment` 是默认情况。但是，对于非组件
返回值不提供默认情况将导致错误：

```python
rx.match(
    MatchState.cat_breed,
    ("persian", "persian cat selected"),
    ("siamese", "siamese cat selected"),
)
```

上面的代码片段将导致错误，因为在此场景中必须显式提供默认情况。

## 处理多条件

`rx.match` 擅长高效管理多个条件及其对应的组件，
与嵌套的 `rx.cond` 结构相比，提供了更简洁、更易读的替代方案。

考虑以下示例：

```python demo exec
from typing import List

import reflex as rx


class MultiMatchState(rx.State):
    animal_breed: str = ""
    animal_options: List[str] = [
        "persian",
        "siamese",
        "maine coon",
        "pug",
        "corgi",
        "mustang",
        "rahvan",
        "football",
        "golf",
    ]

    @rx.event
    def set_animal_breed(self, breed: str):
        self.animal_breed = breed


def multi_match_demo():
    return rx.flex(
        rx.match(
            MultiMatchState.animal_breed,
            ("persian", "siamese", "maine coon", rx.text("Breeds of cats.")),
            ("pug", "corgi", rx.text("Breeds of dogs.")),
            ("mustang", "rahvan", rx.text("Breeds of horses.")),
            rx.text("Unknown animal breed"),
        ),
        rx.select.root(
            rx.select.trigger(),
            rx.select.content(
                rx.select.group(
                    rx.foreach(
                        MultiMatchState.animal_options,
                        lambda x: rx.select.item(x, value=x),
                    )
                ),
            ),
            value=MultiMatchState.animal_breed,
            on_change=MultiMatchState.set_animal_breed,
        ),
        direction="column",
        gap="2",
    )
```

在匹配用例元组中，你可以指定多个条件。匹配用例元组的最后一个值
自动被视为返回值。需要注意的是，匹配用例
元组应至少包含两个元素：一个匹配用例和一个返回值。
以下代码片段将导致错误：

```python
rx.match(
    MatchState.cat_breed,
    ("persian",),
    ("maine coon", rx.text("Maine Coon cat selected")),
)
```

## 作为属性使用

与 `rx.cond` 类似，`rx.match` 可以用作属性值，允许 UI 组件具有动态行为：

```python demo exec
import reflex as rx


class MatchPropState(rx.State):
    value: int = 0

    @rx.event
    def incr(self):
        self.value += 1

    @rx.event
    def decr(self):
        self.value -= 1


def match_prop_demo_():
    return rx.flex(
        rx.button("decrement", on_click=MatchPropState.decr, background_color="red"),
        rx.badge(
            MatchPropState.value,
            color_scheme=rx.match(
                MatchPropState.value,
                (1, "red"),
                (2, "blue"),
                (6, "purple"),
                (10, "orange"),
                "green",
            ),
            size="2",
        ),
        rx.button("increment", on_click=MatchPropState.incr),
        align_items="center",
        direction="row",
        gap="3",
    )
```

在上面的示例中，包含 `State.value` 的 box 组件的背景颜色属性在
`state.value` 为 1 时变为红色，为 5 时变为蓝色，为 5 时变为绿色，为 15 时变为橙色，其他任何值时为黑色。

下面的示例还展示了使用 match 组件作为属性来处理多个条件。

```python demo exec
import reflex as rx


class MatchMultiPropState(rx.State):
    value: int = 0

    @rx.event
    def incr(self):
        self.value += 1

    @rx.event
    def decr(self):
        self.value -= 1


def match_multi_prop_demo_():
    return rx.flex(
        rx.button(
            "decrement", on_click=MatchMultiPropState.decr, background_color="red"
        ),
        rx.badge(
            MatchMultiPropState.value,
            color_scheme=rx.match(
                MatchMultiPropState.value,
                (1, 3, 9, "red"),
                (2, 4, 5, "blue"),
                (6, 8, 12, "purple"),
                (10, 15, 20, 25, "orange"),
                "green",
            ),
            size="2",
        ),
        rx.button("increment", on_click=MatchMultiPropState.incr),
        align_items="center",
        direction="row",
        gap="3",
    )
```

```md alert warning
# 与结构化模式匹配配合使用

`rx.match` 组件专为结构化模式匹配而设计。如果匹配条件的值计算结果为布尔值（True 或 False），建议使用 `rx.cond`。

考虑以下示例，它更适合使用 `rx.cond`：\*
```

```python
rx.cond(MatchPropState.value == 10, "true value", "false value")
```
