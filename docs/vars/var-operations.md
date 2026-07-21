```python exec
import random
import time

import numpy as np

import reflex as rx
```

# Var 操作

Var 操作转换变量在前端上的占位符表示，并提供了一种无需定义计算变量即可对 Var 执行基本操作的方式。

在前端组件中，你不能对状态变量使用任意的 Python 函数。例如，以下代码将**无法工作**。

```python
class State(rx.State):
    number: int


def index():
    # This will be compiled before runtime, before `State.number` has a known value.
    # Since `float` is not a valid var operation, this will throw an error.
    rx.text(float(State.number))
```

这是因为我们将前端编译为 JavaScript，而 `State.number` 的值只有在运行时才能确定。

在下面的示例中，我们使用 var 操作来拼接一个 `string` 和一个 `var`，这意味着我们不需要在状态中将其作为计算变量来处理。

```python demo exec
coins = ["BTC", "ETH", "LTC", "DOGE"]


class VarSelectState(rx.State):
    selected: str = "DOGE"

    def set_selected(self, value: str):
        self.selected = value


def var_operations_example():
    return rx.vstack(
        # Using a var operation to concatenate a string with a var.
        rx.heading("I just bought a bunch of " + VarSelectState.selected, as_="h2"),
        # Using an f-string to interpolate a var.
        rx.text(f"{VarSelectState.selected} is going to the moon!"),
        rx.select(
            coins,
            value=VarSelectState.selected,
            on_change=VarSelectState.set_selected,
        ),
    )
```

```md alert success
# Vars 支持许多常见操作。

它们可用于算术运算、字符串拼接、不等式比较、索引等。请参阅[支持操作的完整列表](/docs/api-reference/var/)。
```

## 支持的操作

Var 操作允许我们在前端更改变量，而无需在 State 中创建更多的计算变量。

一些简单的示例是 `==` var 运算符（用于检查两个变量是否相等）和 `to_string()` var 运算符（用于将变量转换为字符串）。

```python demo exec
fruits = ["Apple", "Banana", "Orange", "Mango"]


class EqualsState(rx.State):
    selected: str = "Apple"
    favorite: str = "Banana"

    def set_selected(self, value: str):
        self.selected = value


def var_equals_example():
    return rx.vstack(
        rx.text(EqualsState.favorite.to_string() + " is my favorite fruit!"),
        rx.select(
            fruits,
            value=EqualsState.selected,
            on_change=EqualsState.set_selected,
        ),
        rx.cond(
            EqualsState.selected == EqualsState.favorite,
            rx.text("The selected fruit is equal to the favorite fruit!"),
            rx.text("The selected fruit is not equal to the favorite fruit."),
        ),
    )
```

### 取反、绝对值和长度

`-` 运算符用于获取变量的负值。`abs()` 运算符用于获取变量的绝对值。`.length()` 运算符用于获取列表变量的长度。

```python demo exec
import random


class OperState(rx.State):
    number: int
    numbers_seen: list = []

    @rx.event
    def update(self):
        self.number = random.randint(-100, 100)
        self.numbers_seen.append(self.number)


def var_operation_example():
    return rx.vstack(
        rx.heading(f"The number: {OperState.number}", as_="h2", size="3"),
        rx.hstack(
            rx.text(
                "Negated:",
                rx.badge(-OperState.number, variant="soft", color_scheme="green"),
            ),
            rx.text(
                "Absolute:",
                rx.badge(abs(OperState.number), variant="soft", color_scheme="blue"),
            ),
            rx.text(
                "Numbers seen:",
                rx.badge(
                    OperState.numbers_seen.length(), variant="soft", color_scheme="red"
                ),
            ),
        ),
        rx.button("Update", on_click=OperState.update),
    )
```

### 比较和数学运算符

所有的比较运算符在 Python 中的使用方式都符合预期。这些包括 `==`、`!=`、`>`、`>=`、`<`、`<=`。

有加法 `+`、减法 `-`、乘法 `*` 以及幂运算 `pow()` 运算符。

```python demo exec
import random


class CompState(rx.State):
    number_1: int
    number_2: int

    @rx.event
    def update(self):
        self.number_1 = random.randint(-10, 10)
        self.number_2 = random.randint(-10, 10)


def var_comparison_example():

    return rx.vstack(
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Integer 1"),
                    rx.table.column_header_cell("Integer 2"),
                    rx.table.column_header_cell("Operation"),
                    rx.table.column_header_cell("Outcome"),
                ),
            ),
            rx.table.body(
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("Int 1 == Int 2"),
                    rx.table.cell(
                        (CompState.number_1 == CompState.number_2).to_string()
                    ),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("Int 1 != Int 2"),
                    rx.table.cell(
                        (CompState.number_1 != CompState.number_2).to_string()
                    ),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("Int 1 > Int 2"),
                    rx.table.cell(
                        (CompState.number_1 > CompState.number_2).to_string()
                    ),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("Int 1 >= Int 2"),
                    rx.table.cell(
                        (CompState.number_1 >= CompState.number_2).to_string()
                    ),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(
                        CompState.number_2,
                    ),
                    rx.table.cell("Int 1 < Int 2 "),
                    rx.table.cell(
                        (CompState.number_1 < CompState.number_2).to_string()
                    ),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("Int 1 <= Int 2"),
                    rx.table.cell(
                        (CompState.number_1 <= CompState.number_2).to_string()
                    ),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("Int 1 + Int 2"),
                    rx.table.cell(f"{(CompState.number_1 + CompState.number_2)}"),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("Int 1 - Int 2"),
                    rx.table.cell(f"{CompState.number_1 - CompState.number_2}"),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("Int 1 * Int 2"),
                    rx.table.cell(f"{CompState.number_1 * CompState.number_2}"),
                ),
                rx.table.row(
                    rx.table.row_header_cell(CompState.number_1),
                    rx.table.cell(CompState.number_2),
                    rx.table.cell("pow(Int 1, Int2)"),
                    rx.table.cell(f"{pow(CompState.number_1, CompState.number_2)}"),
                ),
            ),
            width="100%",
        ),
        rx.button("Update", on_click=CompState.update),
    )
```

### 真除法、地板除法和取余

`/` 运算符表示真除法。`//` 运算符表示地板除法。`%` 运算符表示除法的余数。

```python demo exec
import random


class DivState(rx.State):
    number_1: float = 3.5
    number_2: float = 1.4

    @rx.event
    def update(self):
        self.number_1 = round(random.uniform(5.1, 9.9), 2)
        self.number_2 = round(random.uniform(0.1, 4.9), 2)


def var_div_example():
    return rx.vstack(
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Integer 1"),
                    rx.table.column_header_cell("Integer 2"),
                    rx.table.column_header_cell("Operation"),
                    rx.table.column_header_cell("Outcome"),
                ),
            ),
            rx.table.body(
                rx.table.row(
                    rx.table.row_header_cell(DivState.number_1),
                    rx.table.cell(DivState.number_2),
                    rx.table.cell("Int 1 / Int 2"),
                    rx.table.cell(f"{DivState.number_1 / DivState.number_2}"),
                ),
                rx.table.row(
                    rx.table.row_header_cell(DivState.number_1),
                    rx.table.cell(DivState.number_2),
                    rx.table.cell("Int 1 // Int 2"),
                    rx.table.cell(f"{DivState.number_1 // DivState.number_2}"),
                ),
                rx.table.row(
                    rx.table.row_header_cell(DivState.number_1),
                    rx.table.cell(DivState.number_2),
                    rx.table.cell("Int 1 % Int 2"),
                    rx.table.cell(f"{DivState.number_1 % DivState.number_2}"),
                ),
            ),
            width="100%",
        ),
        rx.button("Update", on_click=DivState.update),
    )
```

### 与、或、非（And, Or, Not）

在 Reflex 中，`&` 运算符在前端使用时表示逻辑与（AND）。这意味着只有当两个条件同时为真时才返回真。
`|` 运算符在前端使用时表示逻辑或（OR）。这意味着当其中一个或两个条件为真时返回真。
`~` 运算符用于对变量取反。它用于 `bool` 类型的变量，相当于 `not` 运算符。

```python demo exec
import random


class LogicState(rx.State):
    var_1: bool = True
    var_2: bool = True

    @rx.event
    def update(self):
        self.var_1 = random.choice([True, False])
        self.var_2 = random.choice([True, False])


def var_logical_example():
    return rx.vstack(
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Var 1"),
                    rx.table.column_header_cell("Var 2"),
                    rx.table.column_header_cell("Operation"),
                    rx.table.column_header_cell("Outcome"),
                ),
            ),
            rx.table.body(
                rx.table.row(
                    rx.table.row_header_cell(LogicState.var_1.to_string()),
                    rx.table.cell(LogicState.var_2.to_string()),
                    rx.table.cell("Logical AND (&)"),
                    rx.table.cell((LogicState.var_1 & LogicState.var_2).to_string()),
                ),
                rx.table.row(
                    rx.table.row_header_cell(LogicState.var_1.to_string()),
                    rx.table.cell(LogicState.var_2.to_string()),
                    rx.table.cell("Logical OR (|)"),
                    rx.table.cell((LogicState.var_1 | LogicState.var_2).to_string()),
                ),
                rx.table.row(
                    rx.table.row_header_cell(LogicState.var_1.to_string()),
                    rx.table.cell(LogicState.var_2.to_string()),
                    rx.table.cell("The invert of Var 1 (~)"),
                    rx.table.cell((~LogicState.var_1).to_string()),
                ),
            ),
            width="100%",
        ),
        rx.button("Update", on_click=LogicState.update),
    )
```

### Contains、Reverse 和 Join

Var 类型不支持 `in` 运算符，我们必须使用 `Var.contains()` 代替。使用 `contains` 时，变量必须是 `dict`、`list`、`tuple` 或 `str` 类型。
`contains` 检查变量是否包含我们作为参数传递的对象。

我们使用 `reverse` 操作来反转列表变量。变量必须是 `list` 类型。

最后，我们使用 `join` 操作将列表变量连接为字符串。

```python demo exec
class ListsState(rx.State):
    list_1: list = [1, 2, 3, 4, 6]
    list_2: list = [7, 8, 9, 10]
    list_3: list = ["p", "y", "t", "h", "o", "n"]


def var_list_example():
    return rx.hstack(
        rx.vstack(
            rx.heading(f"List 1: {ListsState.list_1}", as_="h2", size="3"),
            rx.text(f"List 1 Contains 3: {ListsState.list_1.contains(3)}"),
        ),
        rx.vstack(
            rx.heading(f"List 2: {ListsState.list_2}", as_="h2", size="3"),
            rx.text(f"Reverse List 2: {ListsState.list_2.reverse()}"),
        ),
        rx.vstack(
            rx.heading(f"List 3: {ListsState.list_3}", as_="h2", size="3"),
            rx.text(f"List 3 Joins: {ListsState.list_3.join()}"),
        ),
    )
```

### Map、Filter、Reduce 和 Flat Map

`map` 操作将一个函数应用于列表变量的每个元素。该函数接收一个表示元素的变量，并且必须使用其他 var 操作构建其结果。（`foreach` 是 `map` 的已弃用别名。）

`filter` 操作保留函数返回真值的元素，遵循 Python 的真值语义：`0`、`""`、空列表和空字典都是假值。不带参数调用 `filter()` 会保留真值元素本身，类似于 Python 中的 `filter(None, xs)`。

`reduce` 操作将元素组合为单个值，类似于 `functools.reduce`，使用累加器和当前元素调用函数。传递第二个参数以提供初始累加器值；不传递时，第一个元素被用作初始值，对空列表进行 reduce 会在浏览器中引发 `TypeError`。

`flat_map` 操作将一个函数映射到列表上，并将结果展平一层，以 Python 的方式迭代每个结果：列表产生其元素，字符串产生其字符，字典产生其键。

```python demo exec
class MapFilterReduceState(rx.State):
    numbers: list[int] = [1, 2, 3, 4, 5]
    words: list[str] = ["hello", "", "world"]
    nested: list[list[int]] = [[1, 2], [3], [4, 5]]


def var_map_filter_reduce_example():
    return rx.vstack(
        rx.text(f"Doubled: {MapFilterReduceState.numbers.map(lambda x: x * 2)}"),
        rx.text(f"Evens: {MapFilterReduceState.numbers.filter(lambda x: x % 2 == 0)}"),
        rx.text(f"Non-empty words: {MapFilterReduceState.words.filter()}"),
        rx.text(
            f"Total: {MapFilterReduceState.numbers.reduce(lambda total, x: total + x, 0)}"
        ),
        rx.text(f"Flattened: {MapFilterReduceState.nested.flat_map(lambda x: x)}"),
    )
```

### Lower、Upper、Split

`lower` 运算符将字符串变量转换为小写。`upper` 运算符将字符串变量转换为大写。`split` 运算符将字符串变量拆分为列表。

```python demo exec
class StringState(rx.State):
    string_1: str = "PYTHON is FUN"
    string_2: str = "react is hard"


def var_string_example():
    return rx.hstack(
        rx.vstack(
            rx.heading(f"List 1: {StringState.string_1}", as_="h2", size="3"),
            rx.text(f"List 1 Lower Case: {StringState.string_1.lower()}"),
        ),
        rx.vstack(
            rx.heading(f"List 2: {StringState.string_2}", as_="h2", size="3"),
            rx.text(f"List 2 Upper Case: {StringState.string_2.upper()}"),
            rx.text(f"Split String 2: {StringState.string_2.split()}"),
        ),
    )
```

## 获取元素（索引）

索引仅支持字符串、列表、元组、字典和数据框。要索引到状态变量中，需要严格的类型注解。

```python
class GetItemState1(rx.State):
    list_1: list = [50, 10, 20]


def get_item_error_1():
    return rx.progress(value=GetItemState1.list_1[0])
```

上面的代码中，你可能期望索引到 list_1 状态变量的第一个元素。实际上，上述代码会抛出错误：`Invalid var passed for prop value, expected type <class 'int'>, got value of type typing.Any.` 这是因为列表中元素的类型没有在状态中明确定义。要修复这个问题，将 list_1 的定义改为 `list_1: list[int] = [50, 10, 20]`。

```python demo exec
class GetItemState1(rx.State):
    list_1: list[int] = [50, 10, 20]


def get_item_error_1():
    return rx.progress(value=GetItemState1.list_1[0])
```

### 与 Foreach 一起使用

在使用索引和 `foreach` 时，经常会出现错误。

```python
class ProjectsState(rx.State):
    projects: List[dict] = [
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


def get_badge(technology: str) -> rx.Component:
    return rx.badge(technology, variant="soft", color_scheme="green")


def project_item(project: dict):
    return rx.box(
        rx.hstack(rx.foreach(project["technologies"], get_badge)),
    )


def failing_projects_example() -> rx.Component:
    return rx.box(rx.foreach(ProjectsState.projects, project_item))
```

上面的代码抛出了错误 `TypeError: Could not foreach over var of type Any. (If you are trying to foreach over a state var, add a type annotation to the var.)`

我们必须将 `projects: list[dict]` 改为 `projects: list[dict[str, list]]`，因为虽然 projects 有标注，但 project["technologies"] 中的项没有标注。

```python demo exec
class ProjectsState(rx.State):
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


def projects_example() -> rx.Component:
    def get_badge(technology: str) -> rx.Component:
        return rx.badge(technology, variant="soft", color_scheme="green")

    def project_item(project: dict) -> rx.Component:

        return rx.box(
            rx.hstack(rx.foreach(project["technologies"], get_badge)),
        )

    return rx.box(rx.foreach(ProjectsState.projects, project_item))
```

之前的示例中，每个字典的 `keys` 和 `values` 都只有单一类型。对于复杂的多类型数据，你需要使用数据类，如下所示。

```python demo exec
import dataclasses


@dataclasses.dataclass
class ActressType:
    actress_name: str
    age: int
    pages: list[dict[str, str]]


class MultiDataTypeState(rx.State):
    """The app state."""

    actresses: list[ActressType] = [
        ActressType(
            actress_name="Ariana Grande",
            age=30,
            pages=[
                {"url": "arianagrande.com"},
                {"url": "https://es.wikipedia.org/wiki/Ariana_Grande"},
            ],
        ),
        ActressType(
            actress_name="Gal Gadot",
            age=38,
            pages=[
                {"url": "http://www.galgadot.com/"},
                {"url": "https://es.wikipedia.org/wiki/Gal_Gadot"},
            ],
        ),
    ]


def actresses_example() -> rx.Component:
    def showpage(page: dict[str, str]):
        return rx.vstack(
            rx.text(page["url"]),
        )

    def showlist(item: ActressType):
        return rx.vstack(
            rx.hstack(
                rx.text(item.actress_name),
                rx.text(item.age),
            ),
            rx.foreach(item.pages, showpage),
        )

    return rx.box(rx.foreach(MultiDataTypeState.actresses, showlist))
```

如果将 `actresses` 的类型设置为 `actresses: list[dict[str,str]]` 会失败，因为它无法理解 `pages` 键的 `value` 实际上是一个 `list`。

## 组合多个 Var 操作

你也可以将多个 var 操作组合在一起，如下一个示例所示。

```python demo exec
import random


class VarNumberState(rx.State):
    number: int

    @rx.event
    def update(self):
        self.number = random.randint(0, 100)


def var_number_example():
    return rx.vstack(
        rx.heading(f"The number is {VarNumberState.number}", as_="h2", size="5"),
        # Var operations can be composed for more complex expressions.
        rx.cond(
            VarNumberState.number % 2 == 0,
            rx.text("Even", color="green"),
            rx.text("Odd", color="red"),
        ),
        rx.button("Update", on_click=VarNumberState.update),
    )
```

我们本可以创建一个返回 `number` 奇偶性的计算变量，但使用 var 操作会更简单。

Var 操作通常可以链式调用以构成复合表达式，但某些 var 操作不支持的复杂转换必须使用计算变量在后端计算。
