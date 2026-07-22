---
components:
  - rx.blockquote
---

```python exec
import reflex as rx
```

# Blockquote（块引用）

```python demo
rx.blockquote("Perfect typography is certainly the most elusive of all arts.")
```

## 尺寸

使用 `size` prop 来控制块引用的大小。该 prop 还提供正确的行高和修正的字间距——随着文本尺寸增大，相对行高和字间距会减小。

```python demo
rx.flex(
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="1"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="2"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="3"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="4"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="5"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="6"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="7"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="8"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", size="9"
    ),
    direction="column",
    spacing="3",
)
```

## 字重

使用 `weight` prop 来设置块引用的字重。

```python demo
rx.flex(
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", weight="light"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.",
        weight="regular",
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", weight="medium"
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.", weight="bold"
    ),
    direction="column",
    spacing="3",
)
```

## 颜色

使用 `color_scheme` prop 来指定特定颜色，忽略全局主题。

```python demo
rx.flex(
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.",
        color_scheme="indigo",
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.",
        color_scheme="cyan",
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.",
        color_scheme="crimson",
    ),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.",
        color_scheme="orange",
    ),
    direction="column",
    spacing="3",
)
```

## 高对比度

使用 `high_contrast` prop 来增加与背景的颜色对比度。

```python demo
rx.flex(
    rx.blockquote("Perfect typography is certainly the most elusive of all arts."),
    rx.blockquote(
        "Perfect typography is certainly the most elusive of all arts.",
        high_contrast=True,
    ),
    direction="column",
    spacing="3",
)
```
