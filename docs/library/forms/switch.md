---
components:
  - rx.switch

Switch: |
  lambda **props: rx.switch(**props)
---

```python exec
import reflex as rx
```

# 开关（Switch）

开关（Switch）是复选框的替代切换控件。

## 基本示例

这是一个开关的基本示例。我们使用 `on_change` 触发器来切换状态中的值。

```python demo exec
class SwitchState(rx.State):
    value: bool = False

    @rx.event
    def set_end(self, value: bool):
        self.value = value


def switch_intro():
    return rx.center(
        rx.switch(on_change=SwitchState.set_end),
        rx.badge(SwitchState.value),
    )
```

## 控制值

`checked` 属性用于控制开关的状态。当开关状态改变时，会调用 `on_change` 事件，即调用 `change_checked` 事件处理函数。

当 `disabled` 属性为 `True` 时，会阻止用户与开关交互。在下面的示例中，即使第二个开关是 `disabled` 的，我们仍然可以通过 `checked` 属性来改变它是否被选中。

```python demo exec
class ControlSwitchState(rx.State):
    checked = True

    @rx.event
    def change_checked(self, checked: bool):
        """改变开关的选中状态变量。"""
        self.checked = checked


def control_switch_example():
    return rx.hstack(
        rx.switch(
            checked=ControlSwitchState.checked,
            on_change=ControlSwitchState.change_checked,
        ),
        rx.switch(
            checked=ControlSwitchState.checked,
            on_change=ControlSwitchState.change_checked,
            disabled=True,
        ),
    )
```

## 在表单中使用开关

开关的 `name` 是提交表单时作为名称/值对的一部分所必需的。当 `required` 属性为 `True` 时，表示用户必须在提交表单之前选中该开关。

`value` 属性仅用于表单提交，使用 `checked` 属性来控制 `switch` 的状态。

```python demo exec
class FormSwitchState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        self.form_data = form_data


def switch_form_example():
    return rx.card(
        rx.vstack(
            rx.heading("Example Form", as_="h2"),
            rx.form.root(
                rx.hstack(
                    rx.switch(name="switch"),
                    rx.button("Submit", type="submit"),
                    width="100%",
                ),
                on_submit=FormSwitchState.handle_submit,
                reset_on_submit=True,
            ),
            rx.divider(),
            rx.hstack(
                rx.heading("Results:", as_="h2"),
                rx.badge(FormSwitchState.form_data.to_string()),
            ),
            align_items="left",
            width="100%",
        ),
        width="50%",
    )
```
