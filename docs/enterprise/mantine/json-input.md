---
title: JSON Input
---

# JSON Input

`rxe.mantine.json_input` 是一个允许你以用户友好的方式输入 JSON 数据的组件。它提供验证和格式化功能，以确保 JSON 数据结构正确。

## 示例

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class JsonInputState(rx.State):
    json_data: str = ""

    def set_json_data(self, value: str):
        self.json_data = value


def json_input_example():
    return rxe.mantine.json_input(
        id="json-input",
        value=JsonInputState.json_data,
        placeholder="Enter JSON data",
        label="JSON Input",
        description="Please enter valid JSON data.",
        required=True,
        size="md",
        format_on_blur=True,
        on_change=JsonInputState.set_json_data,
        width="300px",
    )
```
