---
components:
  - rx.input.slot
---

```python exec
import reflex as rx
```

# 输入框（Input）

文本字段（Text Field）是用户可以输入内容的输入框。此组件使用 Radix 的 [text field](https://www.radix-ui.com/themes/docs/components/text-field) 组件。

## 概述

TextField 组件用于捕获用户输入，可以包含一个可选的插槽（Slot）用于放置按钮和图标。它基于 <div> 元素，支持通用的 margin 属性。

## 基本示例

```python demo
rx.input(
    rx.input.slot(
        rx.icon(tag="search"),
    ),
    placeholder="Search here...",
)
```

## 带 Blur 事件的有状态示例

```python demo exec
class TextfieldBlur1(rx.State):
    text: str = "Hello World!"

    @rx.event
    def set_text(self, text: str):
        self.text = text


def blur_example1():
    return rx.vstack(
        rx.heading(TextfieldBlur1.text, as_="h2"),
        rx.input(
            rx.input.slot(
                rx.icon(tag="search"),
            ),
            placeholder="Search here...",
            on_blur=TextfieldBlur1.set_text,
        ),
    )
```

## 受控示例

```python demo exec
class TextfieldControlled1(rx.State):
    text: str = "Hello World!"

    @rx.event
    def set_text(self, text: str):
        self.text = text


def controlled_example1():
    return rx.vstack(
        rx.heading(TextfieldControlled1.text, as_="h2"),
        rx.input(
            rx.input.slot(
                rx.icon(tag="search"),
            ),
            placeholder="Search here...",
            value=TextfieldControlled1.text,
            on_change=TextfieldControlled1.set_text,
        ),
    )
```

## 实际示例

```python demo exec
def song(title, initials: str, genre: str):
    return rx.card(
        rx.flex(
            rx.flex(
                rx.avatar(fallback=initials),
                rx.flex(
                    rx.text(title, size="2", weight="bold"),
                    rx.text(genre, size="1", color_scheme="gray"),
                    direction="column",
                    spacing="1",
                ),
                direction="row",
                align_items="left",
                spacing="1",
            ),
            rx.flex(
                rx.icon(tag="chevron_right"),
                align_items="center",
            ),
            justify="between",
        )
    )


def search():
    return rx.card(
        rx.flex(
            rx.input(
                rx.input.slot(
                    rx.icon(tag="search"),
                ),
                placeholder="Search songs...",
            ),
            rx.flex(
                song("The Less I Know", "T", "Rock"),
                song("Breathe Deeper", "ZB", "Rock"),
                song("Let It Happen", "TF", "Rock"),
                song("Borderline", "ZB", "Pop"),
                song("Lost In Yesterday", "TO", "Rock"),
                song("Is It True", "TO", "Rock"),
                direction="column",
                spacing="1",
            ),
            direction="column",
            spacing="3",
        ),
        style={"maxWidth": 500},
    )
```
