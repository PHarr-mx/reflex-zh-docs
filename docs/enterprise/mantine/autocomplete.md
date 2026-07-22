---
title: Autocomplete
---

# Autocomplete 组件

`rxe.mantine.autocomplete` 是一个在用户输入时提供建议的组件。它通过根据输入提供相关选项来增强用户体验。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def autocomplete_example():
    return rx.vstack(
        rxe.mantine.autocomplete(
            data=["Apple", "Banana", "Cherry", "Date", "Elderberry"],
            placeholder="Type a fruit",
            label="Fruit Autocomplete",
            description="Select a fruit from the list",
        ),
    )
```
