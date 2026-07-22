---
title: Collapse
---

# Collapse 组件

`rxe.mantine.collapse` 是一个允许你在应用程序中创建可折叠区域的组件。它适用于根据用户交互（如点击按钮或链接）来隐藏或显示内容。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class CollapseState(rx.State):
    is_open: bool = False

    @rx.event
    def toggle_collapse(self):
        self.is_open = not self.is_open


def collapse_example():
    return rx.vstack(
        rxe.mantine.collapse(
            rx.text(
                "This is a collapsible section. Click the button to toggle the collapse.",
                font_size="lg",
            ),
            in_=CollapseState.is_open,
            label="Collapsible Section",
            description="Click the button to toggle the collapse.",
        ),
        rx.button(
            "Toggle Collapse",
            on_click=lambda: CollapseState.toggle_collapse,
        ),
    )
```
