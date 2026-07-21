```python exec
import reflex as rx
```

# 布局组件（Layout Components）

布局组件如 `rx.flex`、`rx.container`、`rx.box` 等，用于组织和构建应用的视觉呈现。此页面详细说明了每个组件何时以及如何使用。

```md video https://youtube.com/embed/ITOZkzjtjUA?start=3311&end=3853
# 视频：页面主要内容布局示例
```

## Box

`rx.box` 是一个通用组件，可以对其子元素应用任何 CSS 样式。它是一个构建块，可用于应用特定的布局或样式属性。

**使用时机：** 当你需要对界面的一部分应用特定样式或约束时，使用 `rx.box`。

```python demo
rx.box(
    rx.box(
        "CSS color",
        background_color="red",
        border_radius="2px",
        width="50%",
        margin="4px",
        padding="4px",
    ),
    rx.box(
        "Radix Color",
        background_color=rx.color("tomato", 3),
        border_radius="5px",
        width="80%",
        margin="12px",
        padding="12px",
    ),
    text_align="center",
    width="100%",
)
```

## Stack

`rx.stack` 是一个布局组件，根据方向将其子元素排列为单列或单行。它对于在元素之间保持一致的间距非常有用。

**使用时机：** 当你需要将一系列组件以垂直或水平方向排列并保持均匀间距时，使用 `rx.stack`。

```python demo
rx.flex(
    rx.stack(
        rx.box(
            "Example",
            bg="orange",
            border_radius="3px",
            width="20%",
        ),
        rx.box(
            "Example",
            bg="lightblue",
            border_radius="3px",
            width="30%",
        ),
        flex_direction="row",
        width="100%",
    ),
    rx.stack(
        rx.box(
            "Example",
            bg="orange",
            border_radius="3px",
            width="20%",
        ),
        rx.box(
            "Example",
            bg="lightblue",
            border_radius="3px",
            width="30%",
        ),
        flex_direction="column",
        width="100%",
    ),
    width="100%",
)
```

## Flex

`rx.flex` 组件用于创建灵活的盒状布局，灵感来源于 [CSS Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox)。它非常适合设计项目大小可根据可用空间动态增长和收缩的布局。

**使用时机：** 当你需要一个能动态调整子组件大小和位置的响应式布局时，使用 `rx.flex`。

```python demo
rx.flex(
    rx.card("Card 1"),
    rx.card("Card 2"),
    rx.card("Card 3"),
    spacing="2",
    width="100%",
)
```

## Grid

`rx.grid` 组件用于创建基于网格系统的复杂响应式布局，类似于 [CSS Grid Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout)。

**使用时机：** 当处理需要行和列的复杂布局，特别是在多个轴上需要对齐和间距时，使用 `rx.grid`。

```python demo
rx.grid(
    rx.foreach(
        rx.Var.range(12),
        lambda i: rx.card(f"Card {i + 1}", height="10vh"),
    ),
    columns="3",
    spacing="4",
    width="100%",
)
```

## Container

`rx.container` 组件通常提供内边距并限制内部内容的最大宽度，常用于在大屏幕上居中内容。

**使用时机：** 使用 `rx.container` 将应用内容包裹在带有内边距的居中块中。

```python demo
rx.box(
    rx.container(
        rx.card(
            "This content is constrained to a max width of 448px.",
            width="100%",
        ),
        size="1",
    ),
    rx.container(
        rx.card(
            "This content is constrained to a max width of 688px.",
            width="100%",
        ),
        size="2",
    ),
    rx.container(
        rx.card(
            "This content is constrained to a max width of 880px.",
            width="100%",
        ),
        size="3",
    ),
    background_color="var(--gray-3)",
    width="100%",
)
```
