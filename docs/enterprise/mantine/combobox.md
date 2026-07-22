---
title: Combobox
---

```python exec
import reflex as rx
import reflex_enterprise as rxe
```

# Combobox

`rxe.mantine.combobox` 是 Mantine 组件 [Combobox](https://mantine.dev/core/combobox/) 的封装。它是一个简单的组件，可用于显示选项列表，并允许用户从列表中选择一个或多个选项。它可以在各种场景中使用，例如在表单中或作为独立组件。

```python
import reflex as rx
import reflex_enterprise as rxe


def combobox_page():
    """Combobox 演示。"""
    return rxe.mantine.combobox(
        rxe.mantine.combobox.target(
            rx.input(type="button"),
        ),
        rxe.mantine.combobox.dropdown(
            rxe.mantine.combobox.options(
                rxe.mantine.combobox.option("Option 1"),
                rxe.mantine.combobox.option("Option 2"),
                rxe.mantine.combobox.option("Option 3"),
            ),
        ),
        label="Combobox",
        placeholder="Select a value",
    )
```
