---
components:
  - rx.heading
---

```python exec
import reflex as rx
```

# Heading（标题）

```python demo
rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2")
```

## 作为其他元素

使用 `as_` prop 来更改标题级别。此 prop 纯粹是语义性的，不会改变视觉外观。

```python demo
rx.flex(
    rx.heading("Level 2", as_="h2"),
    rx.heading("Level 3", as_="h3"),
    rx.heading("Level 4", as_="h4"),
    direction="column",
    spacing="3",
)
```

## 尺寸

使用 `size` prop 来控制标题的大小。该 prop 还提供正确的行高和修正的字间距——随着文本尺寸增大，相对行高和字间距会减小。

```python demo
rx.flex(
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="1"),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="2"),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="3"),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="4"),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="5"),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="6"),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="7"),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="8"),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", size="9"),
    direction="column",
    spacing="3",
)
```

## 字重

使用 `weight` prop 来设置文本字重。

```python demo
rx.flex(
    rx.heading(
        "The quick brown fox jumps over the lazy dog.", as_="h2", weight="light"
    ),
    rx.heading(
        "The quick brown fox jumps over the lazy dog.", as_="h2", weight="regular"
    ),
    rx.heading(
        "The quick brown fox jumps over the lazy dog.", as_="h2", weight="medium"
    ),
    rx.heading("The quick brown fox jumps over the lazy dog.", as_="h2", weight="bold"),
    direction="column",
    spacing="3",
)
```

## 对齐

使用 `align` prop 来设置文本对齐方式。

```python demo
rx.flex(
    rx.heading("Left-aligned", as_="h2", align="left"),
    rx.heading("Center-aligned", as_="h2", align="center"),
    rx.heading("Right-aligned", as_="h2", align="right"),
    direction="column",
    spacing="3",
    width="100%",
)
```

## 修剪

使用 `trim` prop 来修剪文本开头、结尾或两侧的 leading 空间。

```python demo
rx.flex(
    rx.heading(
        "Without Trim",
        as_="h2",
        trim="normal",
        style={
            "background": "var(--gray-a2)",
            "border_top": "1px dashed var(--gray-a7)",
            "border_bottom": "1px dashed var(--gray-a7)",
        },
    ),
    rx.heading(
        "With Trim",
        as_="h2",
        trim="both",
        style={
            "background": "var(--gray-a2)",
            "border_top": "1px dashed var(--gray-a7)",
            "border_bottom": "1px dashed var(--gray-a7)",
        },
    ),
    direction="column",
    spacing="3",
)
```

修剪 leading 在调整卡片或其他"盒状"组件的垂直间距时很有用。否则，上下方向的 padding 看起来会比两侧更大。

```python demo
rx.flex(
    rx.box(
        rx.heading(
            "Without trim",
            as_="h2",
            margin_bottom="4px",
            size="3",
        ),
        rx.text(
            "The goal of typography is to relate font size, line height, and line width in a proportional way that maximizes beauty and makes reading easier and more pleasant."
        ),
        style={
            "background": "var(--gray-a2)",
            "border": "1px dashed var(--gray-a7)",
        },
        padding="16px",
    ),
    rx.box(
        rx.heading("With trim", as_="h2", margin_bottom="4px", size="3", trim="start"),
        rx.text(
            "The goal of typography is to relate font size, line height, and line width in a proportional way that maximizes beauty and makes reading easier and more pleasant."
        ),
        style={
            "background": "var(--gray-a2)",
            "border": "1px dashed var(--gray-a7)",
        },
        padding="16px",
    ),
    direction="column",
    spacing="3",
)
```

## 颜色

使用 `color_scheme` prop 来指定特定颜色，忽略全局主题。

```python demo
rx.flex(
    rx.heading(
        "The quick brown fox jumps over the lazy dog.", as_="h2", color_scheme="indigo"
    ),
    rx.heading(
        "The quick brown fox jumps over the lazy dog.", as_="h2", color_scheme="cyan"
    ),
    rx.heading(
        "The quick brown fox jumps over the lazy dog.", as_="h2", color_scheme="crimson"
    ),
    rx.heading(
        "The quick brown fox jumps over the lazy dog.", as_="h2", color_scheme="orange"
    ),
    direction="column",
)
```

## 高对比度

使用 `high_contrast` prop 来增加与背景的颜色对比度。

```python demo
rx.flex(
    rx.heading(
        "The quick brown fox jumps over the lazy dog.",
        as_="h2",
        color_scheme="indigo",
        high_contrast=True,
    ),
    rx.heading(
        "The quick brown fox jumps over the lazy dog.",
        as_="h2",
        color_scheme="cyan",
        high_contrast=True,
    ),
    rx.heading(
        "The quick brown fox jumps over the lazy dog.",
        as_="h2",
        color_scheme="crimson",
        high_contrast=True,
    ),
    rx.heading(
        "The quick brown fox jumps over the lazy dog.",
        as_="h2",
        color_scheme="orange",
        high_contrast=True,
    ),
    direction="column",
)
```
