---
components:
  - rx.tooltip

Tooltip: |
  lambda **props: rx.tooltip(
      rx.button("Hover over me"),
      content="This is the tooltip content.",
      **props,
  )
---

```python exec
import reflex as rx
```

# Tooltip（工具提示）

`tooltip` 在用户悬停或聚焦于某个元素时显示提示信息。

它接受一个 `content` prop，即与工具提示关联的内容。

```python demo
rx.tooltip(
    rx.button("Hover over me"),
    content="This is the tooltip content.",
)
```

## Tooltip 打开或关闭时的事件

当工具提示的 `open` 状态发生变化时，会调用 `on_open_change` 事件。它与 `open` prop 配合使用，`open` 的值会传递给事件处理器。

```python demo exec
class TooltipState(rx.State):
    num_opens: int = 0
    opened: bool = False

    @rx.event
    def count_opens(self, value: bool):
        self.opened = value
        self.num_opens += 1


def index():
    return rx.flex(
        rx.heading(
            f"Number of times tooltip opened or closed: {TooltipState.num_opens}",
            as_="h2",
        ),
        rx.heading(f"Tooltip open: {TooltipState.opened}", as_="h2"),
        rx.text(
            "Hover over the button to see the tooltip.",
            rx.tooltip(
                rx.button("Hover over me"),
                content="This is the tooltip content.",
                on_open_change=TooltipState.count_opens,
            ),
        ),
        direction="column",
        spacing="3",
    )
```
