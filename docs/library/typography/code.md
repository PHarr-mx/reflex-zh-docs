---
components:
  - rx.code
---

```python exec
import reflex as rx
```

# Code（代码）

```python demo
rx.code("console.log()")
```

## 尺寸

使用 `size` prop 来控制文本大小。该 prop 还提供正确的行高和修正的字间距——随着文本尺寸增大，相对行高和字间距会减小。

```python demo
rx.flex(
    rx.code("console.log()", size="1"),
    rx.code("console.log()", size="2"),
    rx.code("console.log()", size="3"),
    rx.code("console.log()", size="4"),
    rx.code("console.log()", size="5"),
    rx.code("console.log()", size="6"),
    rx.code("console.log()", size="7"),
    rx.code("console.log()", size="8"),
    rx.code("console.log()", size="9"),
    direction="column",
    spacing="3",
    align="start",
)
```

## 字重

使用 `weight` prop 来设置文本字重。

```python demo
rx.flex(
    rx.code("console.log()", weight="light"),
    rx.code("console.log()", weight="regular"),
    rx.code("console.log()", weight="medium"),
    rx.code("console.log()", weight="bold"),
    direction="column",
    spacing="3",
)
```

## 变体

使用 `variant` prop 来控制视觉样式。

```python demo
rx.flex(
    rx.code("console.log()", variant="solid"),
    rx.code("console.log()", variant="soft"),
    rx.code("console.log()", variant="outline"),
    rx.code("console.log()", variant="ghost"),
    direction="column",
    spacing="2",
    align="start",
)
```

## 颜色

使用 `color_scheme` prop 来指定特定颜色，忽略全局主题。

```python demo
rx.flex(
    rx.code("console.log()", color_scheme="indigo"),
    rx.code("console.log()", color_scheme="crimson"),
    rx.code("console.log()", color_scheme="orange"),
    rx.code("console.log()", color_scheme="cyan"),
    direction="column",
    spacing="2",
    align="start",
)
```

## 高对比度

使用 `high_contrast` prop 来增加与背景的颜色对比度。

```python demo
rx.flex(
    rx.flex(
        rx.code("console.log()", variant="solid"),
        rx.code("console.log()", variant="soft"),
        rx.code("console.log()", variant="outline"),
        rx.code("console.log()", variant="ghost"),
        direction="column",
        align="start",
        spacing="2",
    ),
    rx.flex(
        rx.code("console.log()", variant="solid", high_contrast=True),
        rx.code("console.log()", variant="soft", high_contrast=True),
        rx.code("console.log()", variant="outline", high_contrast=True),
        rx.code("console.log()", variant="ghost", high_contrast=True),
        direction="column",
        align="start",
        spacing="2",
    ),
    spacing="3",
)
```
