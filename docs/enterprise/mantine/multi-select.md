---
title: MultiSelect
---

# MultiSelect 组件

`rxe.mantine.multi_select` 是一个用于从列表中选择多个选项的组件。它允许用户选择一个或多个项目，适用于需要多选的场景。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class MultiSelectState(rx.State):
    selected_fruits: list = []

    def set_selected_fruits(self, value: list):
        self.selected_fruits = value


def multi_select_example():
    return rx.vstack(
        rxe.mantine.multi_select(
            label="Select fruits",
            placeholder="Pick all that you like",
            data=["Apple", "Banana", "Cherry", "Date", "Elderberry"],
            value=MultiSelectState.selected_fruits,
            on_change=MultiSelectState.set_selected_fruits,
        )
    )
```
