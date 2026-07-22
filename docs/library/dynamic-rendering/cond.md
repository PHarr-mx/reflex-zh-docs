---
components:
  - rx.cond
---

```python exec
import reflex as rx
```

# 条件渲染（Cond）

此组件用于有条件地渲染组件。

cond 组件接受一个条件和两个组件。
如果条件为 `True`，则渲染第一个组件，否则渲染第二个组件。

```python demo exec
class CondState(rx.State):
    show: bool = True

    @rx.event
    def change(self):
        self.show = not (self.show)


def cond_example():
    return rx.vstack(
        rx.button("Toggle", on_click=CondState.change),
        rx.cond(
            CondState.show,
            rx.text("Text 1", color="blue"),
            rx.text("Text 2", color="red"),
        ),
    )
```

第二个组件是可选的，可以省略。
如果省略了，当条件为 `False` 时不会渲染任何内容。

```python demo exec
class CondOptionalState(rx.State):
    show_optional: bool = True

    @rx.event
    def toggle_optional(self):
        self.show_optional = not (self.show_optional)


def cond_optional_example():
    return rx.vstack(
        rx.button("Toggle", on_click=CondOptionalState.toggle_optional),
        rx.cond(
            CondOptionalState.show_optional,
            rx.text("This text appears when condition is True", color="green"),
        ),
        rx.text("This text is always visible", color="gray"),
    )
```

```md video https://youtube.com/embed/ITOZKzjtjUA?start=6040&end=6463
# 视频：条件渲染
```

## 取反

你可以使用逻辑运算符 `~` 来取反一个条件。

```python
rx.vstack(
    rx.button("Toggle", on_click=CondState.change),
    rx.cond(
        CondState.show, rx.text("Text 1", color="blue"), rx.text("Text 2", color="red")
    ),
    rx.cond(
        ~CondState.show, rx.text("Text 1", color="blue"), rx.text("Text 2", color="red")
    ),
)
```

## 多条件

也可以使用`逻辑或`（|）和`逻辑与`（&）运算符来构建复杂条件。

这里有一个使用变量运算符 `>=`、`<=`、`&` 的示例。我们定义了一个条件：如果一个人的年龄在 18 到 65 岁之间（包括这两个年龄），他们可以工作，否则不能。

我们同样可以在条件中使用运算符 `|` 来表示`逻辑或`。

```python demo exec
import random


class CondComplexState(rx.State):
    age: int = 19

    @rx.event
    def change(self):
        self.age = random.randint(0, 100)


def cond_complex_example():
    return rx.vstack(
        rx.button("Toggle", on_click=CondComplexState.change),
        rx.text(f"Age: {CondComplexState.age}"),
        rx.cond(
            (CondComplexState.age >= 18) & (CondComplexState.age <= 65),
            rx.text("You can work!", color="green"),
            rx.text("You cannot work!", color="red"),
        ),
    )
```

## 嵌套条件

我们还可以将 `cond` 组件相互嵌套以创建更复杂的逻辑。在 Python 中，我们可以有一个 `if` 语句，后面跟着多个 `elif` 语句，最后以 `else` 结尾。在 Reflex 中使用嵌套的 `cond` 组件也可以实现同样的效果。在此示例中，我们检查一个数是正数、负数还是零。

以下是使用 `if` 语句的 Python 逻辑：

```python
number = 0

if number > 0:
    print("Positive number")

elif number == 0:
    print("Zero")
else:
    print("Negative number")
```

以下是在逻辑上完全相同的 Reflex 代码：

```python demo exec
import random


class NestedState(rx.State):
    num: int = 0

    def change(self):
        self.num = random.randint(-10, 10)


def cond_nested_example():
    return rx.vstack(
        rx.button("Toggle", on_click=NestedState.change),
        rx.cond(
            NestedState.num > 0,
            rx.text(f"{NestedState.num} is Positive!", color="orange"),
            rx.cond(
                NestedState.num == 0,
                rx.text(f"{NestedState.num} is Zero!", color="blue"),
                rx.text(f"{NestedState.num} is Negative!", color="red"),
            ),
        ),
    )
```

下面是一个更高级的示例，我们有三个数字，正在检查哪个是最大的。如果其中任意两个相等，则返回 `Some of the numbers are equal!`。

以下 Reflex 代码在逻辑上等同于在 Python 中执行以下操作：

```python
a = 8
b = 10
c = 2

if (a > b and a > c) and (a != b and a != c):
    print(a, " is the largest!")
elif (b > a and b > c) and (b != a and b != c):
    print(b, " is the largest!")
elif (c > a and c > b) and (c != a and c != b):
    print(c, " is the largest!")
else:
    print("Some of the numbers are equal!")
```
