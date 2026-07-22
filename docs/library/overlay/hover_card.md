---
components:
  - rx.hover_card.root
  - rx.hover_card.content
  - rx.hover_card.trigger

only_low_level:
  - True

HoverCardRoot: |
  lambda **props: rx.hover_card.root(
      rx.hover_card.trigger(
          rx.link("Hover over me"),
      ),
      rx.hover_card.content(
          rx.text("This is the tooltip content."),
      ),
      **props
  )

HoverCardContent: |
  lambda **props: rx.hover_card.root(
      rx.hover_card.trigger(
          rx.link("Hover over me"),
      ),
      rx.hover_card.content(
          rx.text("This is the tooltip content."),
          **props
      ),
  )
---

```python exec
import reflex as rx
```

# Hovercard（悬停卡片）

`hover_card.root` 包含悬停卡片的所有部分。

`hover_card.trigger` 包裹用于打开悬停卡片的链接。

`hover_card.content` 包含悬停卡片打开时的内容。

```python demo
rx.text(
    "Hover over the text to see the tooltip. ",
    rx.hover_card.root(
        rx.hover_card.trigger(
            rx.link("Hover over me", color_scheme="blue", underline="always"),
        ),
        rx.hover_card.content(
            rx.text("This is the hovercard content."),
        ),
    ),
)
```

```python demo
rx.text(
    "Hover over the text to see the tooltip. ",
    rx.hover_card.root(
        rx.hover_card.trigger(
            rx.link("Hover over me", color_scheme="blue", underline="always"),
        ),
        rx.hover_card.content(
            rx.grid(
                rx.inset(
                    side="left",
                    pr="current",
                    background="url('https://images.unsplash.com/5/unsplash-kitsune-4.jpg') center/cover",
                    height="full",
                ),
                rx.box(
                    rx.text_area(placeholder="Write a comment…", style={"height": 80}),
                    rx.flex(
                        rx.checkbox("Send to group"),
                        spacing="3",
                        margin_top="12px",
                        justify="between",
                    ),
                    padding_left="12px",
                ),
                columns="120px 1fr",
            ),
            style={"width": 360},
        ),
    ),
)
```

## 悬停卡片打开或关闭时的事件

当悬停卡片的 `open` 状态发生变化时，会调用 `on_open_change` 事件。它与 `open` prop 配合使用，`open` 的值会传递给事件处理器。

```python demo exec
class HovercardState(rx.State):
    num_opens: int = 0
    opened: bool = False

    @rx.event
    def count_opens(self, value: bool):
        self.opened = value
        self.num_opens += 1


def hovercard_example():
    return rx.flex(
        rx.heading(
            f"Number of times hovercard opened or closed: {HovercardState.num_opens}",
            as_="h2",
        ),
        rx.heading(f"Hovercard open: {HovercardState.opened}", as_="h2"),
        rx.text(
            "Hover over the text to see the hover card. ",
            rx.hover_card.root(
                rx.hover_card.trigger(
                    rx.link("Hover over me", color_scheme="blue", underline="always"),
                ),
                rx.hover_card.content(
                    rx.text("This is the tooltip content."),
                ),
                on_open_change=HovercardState.count_opens,
            ),
        ),
        direction="column",
        spacing="3",
    )
```
