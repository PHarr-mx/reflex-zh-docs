---
components:
  - rx.select.root
  - rx.select.trigger
  - rx.select.content
  - rx.select.group
  - rx.select.item
  - rx.select.label
  - rx.select.separator
---

```python exec
import random
import reflex as rx
import reflex.components.radix.primitives as rdxp
```

# 选择器（Select）

显示一个供用户选择的选项列表，由按钮触发。

## 基本示例

```python demo
rx.select.root(
    rx.select.trigger(),
    rx.select.content(
        rx.select.group(
            rx.select.label("Fruits"),
            rx.select.item("Orange", value="orange"),
            rx.select.item("Apple", value="apple"),
            rx.select.item("Grape", value="grape", disabled=True),
        ),
        rx.select.separator(),
        rx.select.group(
            rx.select.label("Vegetables"),
            rx.select.item("Carrot", value="carrot"),
            rx.select.item("Potato", value="potato"),
        ),
    ),
    default_value="apple",
)
```

## 用法

## 禁用

可以使用与 `rx.select.item` 关联的 `disabled` 属性来禁用 `select` 中的单个选项。

```python demo
rx.select.root(
    rx.select.trigger(placeholder="Select a Fruit"),
    rx.select.content(
        rx.select.group(
            rx.select.item("Apple", value="apple"),
            rx.select.item("Grape", value="grape", disabled=True),
            rx.select.item("Pineapple", value="pineapple"),
        ),
    ),
)
```

要完全阻止用户与选择器交互，在 `rx.select.root` 组件上将 `disabled` 属性设置为 `True`。

```python demo
rx.select.root(
    rx.select.trigger(placeholder="This is Disabled"),
    rx.select.content(
        rx.select.group(
            rx.select.item("Apple", value="apple"),
            rx.select.item("Grape", value="grape"),
        ),
    ),
    disabled=True,
)
```

## 设置默认值

在构建 `select` 时可以设置多个默认值。

`rx.select.trigger` 中的 `placeholder` 属性指定当 `value` 或 `default_value` 为空或未设置时渲染的内容。

```python demo
rx.select.root(
    rx.select.trigger(placeholder="pick a fruit"),
    rx.select.content(
        rx.select.group(
            rx.select.item("Apple", value="apple"),
            rx.select.item("Grape", value="grape"),
        ),
    ),
)
```

`rx.select.root` 中的 `default_value` 指定 `select` 初始渲染时的值。`default_value` 应该对应子元素 `rx.select.item` 的 `value`。

```python demo
rx.select.root(
    rx.select.trigger(),
    rx.select.content(
        rx.select.group(
            rx.select.item("Apple", value="apple"),
            rx.select.item("Grape", value="grape"),
        ),
    ),
    default_value="apple",
)
```

## 完全受控

当选择器的值改变时，会触发 `on_change` 事件。在此示例中，`rx.select.root` 的 `value` 属性指定哪个选项被选中，这也可以通过状态和按钮来控制，而无需直接与选择器组件交互。

```python demo exec
class SelectState2(rx.State):
    values: list[str] = ["apple", "grape", "pear"]

    value: str = ""

    def set_value(self, value: str):
        self.value = value

    @rx.event
    def choose_randomly(self):
        """改变选择器的值变量。"""
        original_value = self.value
        while self.value == original_value:
            self.value = random.choice(self.values)


def select_example2():
    return rx.vstack(
        rx.select.root(
            rx.select.trigger(placeholder="No Selection"),
            rx.select.content(
                rx.select.group(
                    rx.foreach(
                        SelectState2.values, lambda x: rx.select.item(x, value=x)
                    )
                ),
            ),
            value=SelectState2.value,
            on_change=SelectState2.set_value,
        ),
        rx.button("Choose Randomly", on_click=SelectState2.choose_randomly),
        rx.button("Reset", on_click=SelectState2.set_value("")),
    )
```

`open` 属性和 `on_open_change` 事件触发器的工作方式类似于 `value` 和 `on_change`，用于控制选择器的打开状态。如果 `on_open_change` 处理函数没有改变 `open` 属性，则无法通过点击 `select_trigger` 来打开或关闭选择器。

```python demo exec
class SelectState8(rx.State):
    is_open: bool = False

    @rx.event
    def set_is_open(self, value: bool):
        self.is_open = value


def select_example8():
    return rx.flex(
        rx.select.root(
            rx.select.trigger(placeholder="No Selection"),
            rx.select.content(
                rx.select.group(
                    rx.select.item("Apple", value="apple"),
                    rx.select.item("Grape", value="grape"),
                ),
            ),
            open=SelectState8.is_open,
            on_open_change=SelectState8.set_is_open,
        ),
        rx.button("Toggle", on_click=SelectState8.set_is_open(~SelectState8.is_open)),
        spacing="2",
    )
```

### 使用选择器提交表单

当选择器是表单的一部分时，`rx.select.root` 的 `name` 属性设置随表单数据一起提交的键。

`rx.select.item` 的 `value` 属性提供当表单提交且选中该项时与 `name` 键关联的值。

当 `rx.select.root` 的 `required` 属性为 `True` 时，表示用户必须在提交表单之前选择一个值。

```python demo exec
class FormSelectState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        self.form_data = form_data


def form_select():
    return rx.flex(
        rx.form.root(
            rx.flex(
                rx.select.root(
                    rx.select.trigger(),
                    rx.select.content(
                        rx.select.group(
                            rx.select.label("Fruits"),
                            rx.select.item("Orange", value="orange"),
                            rx.select.item("Apple", value="apple"),
                            rx.select.item("Grape", value="grape"),
                        ),
                        rx.select.separator(),
                        rx.select.group(
                            rx.select.label("Vegetables"),
                            rx.select.item("Carrot", value="carrot"),
                            rx.select.item("Potato", value="potato"),
                        ),
                    ),
                    default_value="apple",
                    name="select",
                ),
                rx.button("Submit"),
                width="100%",
                direction="column",
                spacing="2",
            ),
            on_submit=FormSelectState.handle_submit,
            reset_on_submit=True,
        ),
        rx.divider(size="4"),
        rx.heading("Results", as_="h2"),
        rx.text(FormSelectState.form_data.to_string()),
        width="100%",
        direction="column",
        spacing="2",
    )
```

## 实际示例

```python demo
rx.card(
    rx.flex(
        rx.image(
            src="https://web.reflex-assets.dev/other/reflex_banner.png",
            alt="Reflex banner image",
            width="100%",
            height="auto",
        ),
        rx.flex(
            rx.heading("Reflex Swag", as_="h2", size="4", margin_bottom="4px"),
            rx.heading("$99", as_="h2", size="6", margin_bottom="4px"),
            direction="row",
            justify="between",
            width="100%",
        ),
        rx.text(
            "Reflex swag with a sense of nostalgia, as if they carry whispered tales of past adventures",
            size="2",
            margin_bottom="4px",
        ),
        rx.divider(size="4"),
        rx.flex(
            rx.flex(
                rx.text("Color", size="2", margin_bottom="4px", color_scheme="gray"),
                rx.select.root(
                    rx.select.trigger(),
                    rx.select.content(
                        rx.select.group(
                            rx.select.item("Light", value="light"),
                            rx.select.item("Dark", value="dark"),
                        ),
                    ),
                    default_value="light",
                ),
                direction="column",
            ),
            rx.flex(
                rx.text("Size", size="2", margin_bottom="4px", color_scheme="gray"),
                rx.select.root(
                    rx.select.trigger(),
                    rx.select.content(
                        rx.select.group(
                            rx.select.item("24", value="24"),
                            rx.select.item("26", value="26"),
                            rx.select.item("28", value="28", disabled=True),
                            rx.select.item("30", value="30"),
                            rx.select.item("32", value="32"),
                            rx.select.item("34", value="34"),
                            rx.select.item("36", value="36"),
                        ),
                    ),
                    default_value="30",
                ),
                direction="column",
            ),
            rx.button(rx.icon(tag="plus"), "Add"),
            align="end",
            justify="between",
            spacing="2",
            width="100%",
        ),
        width="15em",
        direction="column",
        spacing="2",
    ),
)
```
