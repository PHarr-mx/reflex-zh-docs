---
components:
  - rx.text
  - rx.text.em
---

```python exec
import reflex as rx
```

# Text（文本）

```python demo
rx.text("The quick brown fox jumps over the lazy dog.")
```

## 作为其他元素

使用 `as_` prop 将文本渲染为 `p`、`label`、`div` 或 `span`。此 prop 纯粹是语义性的，不会改变视觉外观。

```python demo
rx.flex(
    rx.text("This is a ", rx.text.strong("paragraph"), " element.", as_="p"),
    rx.text("This is a ", rx.text.strong("label"), " element.", as_="label"),
    rx.text("This is a ", rx.text.strong("div"), " element.", as_="div"),
    rx.text("This is a ", rx.text.strong("span"), " element.", as_="span"),
    direction="column",
    spacing="3",
)
```

## 尺寸

使用 `size` prop 来控制文本大小。该 prop 还提供正确的行高和修正的字间距——随着文本尺寸增大，相对行高和字间距会减小。

```python demo
rx.flex(
    rx.text("The quick brown fox jumps over the lazy dog.", size="1"),
    rx.text("The quick brown fox jumps over the lazy dog.", size="2"),
    rx.text("The quick brown fox jumps over the lazy dog.", size="3"),
    rx.text("The quick brown fox jumps over the lazy dog.", size="4"),
    rx.text("The quick brown fox jumps over the lazy dog.", size="5"),
    rx.text("The quick brown fox jumps over the lazy dog.", size="6"),
    rx.text("The quick brown fox jumps over the lazy dog.", size="7"),
    rx.text("The quick brown fox jumps over the lazy dog.", size="8"),
    rx.text("The quick brown fox jumps over the lazy dog.", size="9"),
    direction="column",
    spacing="3",
)
```

尺寸 2–4 专为长文本内容设计。尺寸 1–3 专为 UI 标签设计。

## 字重

使用 `weight` prop 来设置文本字重。

```python demo
rx.flex(
    rx.text("The quick brown fox jumps over the lazy dog.", weight="light", as_="div"),
    rx.text(
        "The quick brown fox jumps over the lazy dog.", weight="regular", as_="div"
    ),
    rx.text("The quick brown fox jumps over the lazy dog.", weight="medium", as_="div"),
    rx.text("The quick brown fox jumps over the lazy dog.", weight="bold", as_="div"),
    direction="column",
    spacing="3",
)
```

## 对齐

使用 `align` prop 来设置文本对齐方式。

```python demo
rx.flex(
    rx.text("Left-aligned", align="left", as_="div"),
    rx.text("Center-aligned", align="center", as_="div"),
    rx.text("Right-aligned", align="right", as_="div"),
    direction="column",
    spacing="3",
    width="100%",
)
```

## 修剪

使用 `trim` prop 来修剪文本框开头、结尾或两侧的 leading 空间。

```python demo
rx.flex(
    rx.text(
        "Without Trim",
        trim="normal",
        style={
            "background": "var(--gray-a2)",
            "border_top": "1px dashed var(--gray-a7)",
            "border_bottom": "1px dashed var(--gray-a7)",
        },
    ),
    rx.text(
        "With Trim",
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
    rx.text("The quick brown fox jumps over the lazy dog.", color_scheme="indigo"),
    rx.text("The quick brown fox jumps over the lazy dog.", color_scheme="cyan"),
    rx.text("The quick brown fox jumps over the lazy dog.", color_scheme="crimson"),
    rx.text("The quick brown fox jumps over the lazy dog.", color_scheme="orange"),
    direction="column",
)
```

## 高对比度

使用 `high_contrast` prop 来增加与背景的颜色对比度。

```python demo
rx.flex(
    rx.text(
        "The quick brown fox jumps over the lazy dog.",
        color_scheme="indigo",
        high_contrast=True,
    ),
    rx.text(
        "The quick brown fox jumps over the lazy dog.",
        color_scheme="cyan",
        high_contrast=True,
    ),
    rx.text(
        "The quick brown fox jumps over the lazy dog.",
        color_scheme="crimson",
        high_contrast=True,
    ),
    rx.text(
        "The quick brown fox jumps over the lazy dog.",
        color_scheme="orange",
        high_contrast=True,
    ),
    direction="column",
)
```

## 带格式化

将 `Text` 与格式化组件组合，为内容添加强调和结构。

```python demo
rx.text(
    "Look, such a helpful ",
    rx.link("link", href="#"),
    ", an ",
    rx.text.em("italic emphasis"),
    " a piece of computer ",
    rx.code("code"),
    ", and even a hotkey combination ",
    rx.text.kbd("⇧⌘A"),
    " within the text.",
    size="5",
)
```

## 预格式化

默认情况下，浏览器会将多个空格渲染为一个。要保留空白字符，请使用 `white_space = "pre"` CSS prop。

```python demo
rx.hstack(
    rx.text("This is not pre     formatted"),
    rx.text("This is pre     formatted", white_space="pre"),
)
```

## 配合表单控件

将 `text` 与 `checkbox`、`radiogroup` 或 `switch` 等表单控件组合时，控件会自动与第一行文本居中对齐，即使文本是多行的。

```python demo
rx.box(
    rx.text(
        rx.flex(
            rx.checkbox(default_checked=True),
            "I understand that these documents are confidential and cannot be shared with a third party.",
        ),
        as_="label",
        size="3",
    ),
    style={"max_width": 300},
)
```
