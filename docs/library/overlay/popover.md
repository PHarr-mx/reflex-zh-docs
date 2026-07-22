---
components:
  - rx.popover.root
  - rx.popover.content
  - rx.popover.trigger
  - rx.popover.close

only_low_level:
  - True

PopoverRoot: |
  lambda **props: rx.popover.root(
      rx.popover.trigger(
          rx.button("Popover"),
      ),
      rx.popover.content(
          rx.flex(
              rx.text("Simple Example"),
              rx.popover.close(
                  rx.button("Close"),
              ),
              direction="column",
              spacing="3",
          ),
      ),
      **props
  )

PopoverContent: |
  lambda **props: rx.popover.root(
      rx.popover.trigger(
          rx.button("Popover"),
      ),
      rx.popover.content(
          rx.flex(
              rx.text("Simple Example"),
              rx.popover.close(
                  rx.button("Close"),
              ),
              direction="column",
              spacing="3",
          ),
          **props
      ),
  )
---

```python exec
import reflex as rx
```

# Popover（弹出框）

弹出框（Popover）由按钮触发，用于显示内容。

`popover.root` 包含弹出框的所有部分。

`popover.trigger` 包含用于切换弹出框的按钮。

`popover.content` 是弹出框打开时弹出的组件。

`popover.close` 是用于关闭已打开弹出框的按钮。

## 基本示例

```python demo
rx.popover.root(
    rx.popover.trigger(
        rx.button("Popover"),
    ),
    rx.popover.content(
        rx.flex(
            rx.text("Simple Example"),
            rx.popover.close(
                rx.button("Close"),
            ),
            direction="column",
            spacing="3",
        ),
    ),
)
```

## 实际场景示例

```python demo
rx.popover.root(
    rx.popover.trigger(
        rx.button("Comment", variant="soft"),
    ),
    rx.popover.content(
        rx.flex(
            rx.avatar("2", fallback="RX", radius="full"),
            rx.box(
                rx.text_area(placeholder="Write a comment…", style={"height": 80}),
                rx.flex(
                    rx.checkbox("Send to group"),
                    rx.popover.close(rx.button("Comment", size="1")),
                    spacing="3",
                    margin_top="12px",
                    justify="between",
                ),
                flex_grow="1",
            ),
            spacing="3",
        ),
        style={"width": 360},
    ),
)
```

```python demo
rx.popover.root(
    rx.popover.trigger(
        rx.button("Feedback", variant="classic"),
    ),
    rx.popover.content(
        rx.inset(
            side="top",
            background="url('https://images.unsplash.com/5/unsplash-kitsune-4.jpg') center/cover",
            height="100px",
        ),
        rx.box(
            rx.text_area(placeholder="Write a comment…", style={"height": 80}),
            rx.flex(
                rx.checkbox("Send to group"),
                rx.popover.close(rx.button("Comment", size="1")),
                spacing="3",
                margin_top="12px",
                justify="between",
            ),
            padding_top="12px",
        ),
        style={"width": 360},
    ),
)
```

## 带动态标题的 Popover

如下代码不会按预期工作，需要将动态标题（`Index2State.language`）放在 `rx.text` 组件内部。

```python
class Index2State(rx.State):
    language: str = "EN"


def index() -> rx.Component:
    return rx.popover.root(
        rx.popover.trigger(
            rx.button(Index2State.language),
        ),
        rx.popover.content(rx.text("Success")),
    )
```

以下代码可以正常工作：

```python demo exec
class Index2State(rx.State):
    language: str = "EN"


def index() -> rx.Component:
    return rx.popover.root(
        rx.popover.trigger(
            rx.button(rx.text(Index2State.language)),
        ),
        rx.popover.content(rx.text("Success")),
    )
```

## Popover 打开或关闭时的事件

当弹出框的 `open` 状态发生变化时，会调用 `on_open_change` 事件。它与 `open` prop 配合使用，`open` 的值会传递给事件处理器。

```python demo exec
class PopoverState(rx.State):
    num_opens: int = 0
    opened: bool = False

    @rx.event
    def count_opens(self, value: bool):
        self.opened = value
        self.num_opens += 1


def popover_example():
    return rx.flex(
        rx.heading(
            f"Number of times popover opened or closed: {PopoverState.num_opens}",
            as_="h2",
        ),
        rx.heading(f"Popover open: {PopoverState.opened}", as_="h2"),
        rx.popover.root(
            rx.popover.trigger(
                rx.button("Popover"),
            ),
            rx.popover.content(
                rx.flex(
                    rx.text("Simple Example"),
                    rx.popover.close(
                        rx.button("Close"),
                    ),
                    direction="column",
                    spacing="3",
                ),
            ),
            on_open_change=PopoverState.count_opens,
        ),
        direction="column",
        spacing="3",
    )
```
