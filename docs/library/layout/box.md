---
components:
  - rx.box
---

```python exec
import reflex as rx
```

# Box

Box 是一个通用容器组件，可用于将其他组件组合在一起。

默认情况下，Box 组件基于 `div`，渲染为块级元素。它的主要用途是应用样式。

## 基本示例

```python demo
rx.box(
    rx.box(
        "CSS color",
        background_color="yellow",
        border_radius="2px",
        width="20%",
        margin="4px",
        padding="4px",
    ),
    rx.box(
        "CSS color",
        background_color="orange",
        border_radius="5px",
        width="40%",
        margin="8px",
        padding="8px",
    ),
    rx.box(
        "Radix Color",
        background_color="var(--tomato-3)",
        border_radius="5px",
        width="60%",
        margin="12px",
        padding="12px",
    ),
    rx.box(
        "Radix Color",
        background_color="var(--plum-3)",
        border_radius="10px",
        width="80%",
        margin="16px",
        padding="16px",
    ),
    rx.box(
        "Radix Theme Color",
        background_color="var(--accent-2)",
        radius="full",
        width="100%",
        margin="24px",
        padding="25px",
    ),
    flex_grow="1",
    text_align="center",
)
```

## 背景

要设置背景[图片](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_images)或[渐变](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_images/Using_CSS_gradients)，请使用 [`background` CSS 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/background)。

```python demo
rx.flex(
    rx.box(
        background="linear-gradient(45deg, var(--tomato-9), var(--plum-9))",
        width="20%",
        height="100%",
    ),
    rx.box(
        background="linear-gradient(red, yellow, blue, orange)",
        width="20%",
        height="100%",
    ),
    rx.box(
        background="radial-gradient(at 0% 30%, red 10px, yellow 30%, #1e90ff 50%)",
        width="20%",
        height="100%",
    ),
    rx.box(
        background="center/cover url('https://web.reflex-assets.dev/other/reflex_banner.png')",
        width="20%",
        height="100%",
    ),
    spacing="2",
    width="100%",
    height="10vh",
)
```
