---
components:
  - rx.checkbox

HighLevelCheckbox: |
  lambda **props: rx.checkbox("Basic Checkbox", **props)
---

```python exec
import reflex as rx
```

# 复选框（Checkbox）

## 基本示例

当 `checkbox` 被点击时，会调用 `on_change` 触发器。

```python demo exec
class CheckboxState(rx.State):
    checked: bool = False

    @rx.event
    def set_checked(self, value: bool):
        self.checked = value


def checkbox_example():
    return rx.vstack(
        rx.heading(CheckboxState.checked, as_="h2"),
        rx.checkbox(on_change=CheckboxState.set_checked),
    )
```

`input` 属性用于将 `checkbox` 设置为受控组件。

```python demo exec
class FormCheckboxState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        print(form_data)
        self.form_data = form_data


def form_checkbox_example():
    return rx.card(
        rx.vstack(
            rx.heading("Example Form", as_="h2"),
            rx.form.root(
                rx.hstack(
                    rx.checkbox(
                        name="checkbox",
                        label="Accept terms and conditions",
                    ),
                    rx.button("Submit", type="submit"),
                    width="100%",
                ),
                on_submit=FormCheckboxState.handle_submit,
                reset_on_submit=True,
            ),
            rx.divider(),
            rx.hstack(
                rx.heading("Results:", as_="h2"),
                rx.badge(FormCheckboxState.form_data.to_string()),
            ),
            align_items="left",
            width="100%",
        ),
        width="50%",
    )
```
