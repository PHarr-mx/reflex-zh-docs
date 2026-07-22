---
components:
  - rx.card

Card: |
  lambda **props: rx.card("Basic Card ", **props)
---

```python exec
import reflex as rx
```

# 卡片（Card）

Card 组件用于将相关组件组合在一起。它与 Box 类似，但带有边框，使用主题颜色和边框圆角，并提供 `size` 属性来根据 Radix `"1"` - `"5"` 比例控制间距和外边距。

在配合主题使用时，Card 比 Box 需要更少的样式设置即可实现一致的视觉效果。

## 基本示例

```python demo
rx.flex(
    rx.card("Card 1", size="1"),
    rx.card("Card 2", size="2"),
    rx.card("Card 3", size="3"),
    rx.card("Card 4", size="4"),
    rx.card("Card 5", size="5"),
    spacing="2",
    align_items="flex-start",
    flex_wrap="wrap",
)
```

## 渲染为其他元素

可以使用 `as_child` 属性将 Card 渲染为其他元素。Link 和 Button 常用于使 Card 可点击。

```python demo
rx.card(
    rx.link(
        rx.flex(
            rx.avatar(src="https://web.reflex-assets.dev/other/reflex_banner.png"),
            rx.box(
                rx.heading("Quick Start", as_="h2"),
                rx.text("Get started with Reflex in 5 minutes."),
            ),
            spacing="2",
        ),
    ),
    as_child=True,
)
```

## 使用内嵌内容（Inset）
