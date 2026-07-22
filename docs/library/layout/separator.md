---
components:
  - rx.separator
Separator: |
  lambda **props: rx.separator(**props)
---

```python exec
import reflex as rx
```

# 分隔符（Separator）

在视觉或语义上分隔内容。

## 基本示例

```python demo
rx.flex(
    rx.card("Section 1"),
    rx.divider(),
    rx.card("Section 2"),
    spacing="4",
    direction="column",
    align="center",
)
```

## 尺寸

`size` 属性控制分隔符的长度。使用 `size="4"` 会使分隔符填满父容器。将 CSS 的 `width` 或 `height` 属性设置为 `"100%"` 也可以达到同样的效果，但 `size` 无论方向如何都能以相同方式工作。

```python demo
rx.flex(
    rx.card("Section 1"),
    rx.divider(size="4"),
    rx.card("Section 2"),
    spacing="4",
    direction="column",
)
```

## 方向

将 orientation 属性设置为 `vertical` 会使分隔符垂直显示。

```python demo
rx.flex(
    rx.card("Section 1"),
    rx.divider(orientation="vertical", size="4"),
    rx.card("Section 2"),
    spacing="4",
    width="100%",
    height="10vh",
)
```
