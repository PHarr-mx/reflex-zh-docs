---
components:
  - rx.radio_group
  - rx.radio_group.root
  - rx.radio_group.item

HighLevelRadioGroup: |
  lambda **props: rx.radio_group(["1", "2", "3", "4", "5"], **props)

RadioGroupRoot: |
  lambda **props: rx.radio_group.root(
      rx.radio_group.item(value="1"),
      rx.radio_group.item(value="2"),
      rx.radio_group.item(value="3"),
      rx.radio_group.item(value="4"),
      rx.radio_group.item(value="5"),
      **props
  )

RadioGroupItem: |
  lambda **props: rx.radio_group.root(
      rx.radio_group.item(value="1", **props),
      rx.radio_group.item(value="2", **props),
      rx.radio_group.item(value="3", **props),
      rx.radio_group.item(value="4", **props),
      rx.radio_group.item(value="5", **props),
      default_value="1",
  )
---

```python exec
import reflex as rx
```

# 单选组（Radio Group）

一组交互式的单选按钮（Radio Button），同一时间只能选中一个。

## 基本示例

```python demo exec
class RadioGroupState(rx.State):
    item: str = "No Selection"

    @rx.event
    def set_item(self, item: str):
        self.item = item


def radio_group_state_example():
    return rx.vstack(
        rx.badge(RadioGroupState.item, color_scheme="green"),
        rx.radio(["1", "2", "3"], on_change=RadioGroupState.set_item, direction="row"),
    )
```

## 使用单选组提交表单

`name` 属性用于命名该组。它作为名称/值对的一部分随所属表单一起提交。

当 `required` 属性为 `True` 时，表示用户必须在提交所属表单之前选择一个单选项。

```python demo exec
class FormRadioState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        self.form_data = form_data


def radio_form_example():
    return rx.card(
        rx.vstack(
            rx.heading("Example Form", as_="h2"),
            rx.form.root(
                rx.vstack(
                    rx.radio_group(
                        ["Option 1", "Option 2", "Option 3"],
                        name="radio_choice",
                        direction="row",
                    ),
                    rx.button("Submit", type="submit"),
                    width="100%",
                    spacing="4",
                ),
                on_submit=FormRadioState.handle_submit,
                reset_on_submit=True,
            ),
            rx.divider(),
            rx.hstack(
                rx.heading("Results:", as_="h2"),
                rx.badge(FormRadioState.form_data.to_string()),
            ),
            align_items="left",
            width="100%",
            spacing="4",
        ),
        width="50%",
    )
```
