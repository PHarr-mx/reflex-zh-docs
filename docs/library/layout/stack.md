---
components:
  - rx.stack
  - rx.hstack
  - rx.vstack
Stack: |
  lambda **props: rx.stack(
      rx.card("Card 1", size="2"), rx.card("Card 2", size="2"), rx.card("Card 3", size="2"),
      width="100%",
      height="20vh",
      **props,
  )

VStack: |
  lambda **props: rx.vstack(
      rx.card("Card 1", size="2"),
      rx.card("Card 2", size="2"),
      rx.card("Card 3", size="2"),
      width="100%",
      border_radius="0.5rem",
      **props,
  )

HStack: |
  lambda **props: rx.hstack(
      rx.card("Card 1", size="2"),
      rx.card("Card 2", size="2"),
      rx.card("Card 3", size="2"),
      width="100%",
      border_radius="0.5rem",
      **props,
  )
---

```python exec
import reflex as rx
```

# 堆叠（Stack）

`Stack` 是一个布局组件，用于将元素组合在一起并在它们之间应用间距。

`vstack` 用于在垂直方向堆叠元素。

`hstack` 用于在水平方向堆叠元素。

`stack` 用于在垂直或水平方向堆叠元素。

这些组件都基于 `flex` 组件，因此继承了其所有属性。

`stack` 组件可以配合 `flex_direction` 属性使用，将其设置为 `row` 或 `column` 来指定方向。

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
        rx.box(
            "Example",
            bg="lightgreen",
            border_radius="3px",
            width="50%",
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
        rx.box(
            "Example",
            bg="lightgreen",
            border_radius="3px",
            width="50%",
        ),
        flex_direction="column",
        width="100%",
    ),
    width="100%",
)
```

## 水平堆叠（Hstack）

```python demo
rx.hstack(
    rx.box("Example", bg="red", border_radius="3px", width="10%"),
    rx.box(
        "Example",
        bg="orange",
        border_radius="3px",
        width="10%",
    ),
    rx.box(
        "Example",
        bg="yellow",
        border_radius="3px",
        width="10%",
    ),
    rx.box(
        "Example",
        bg="lightblue",
        border_radius="3px",
        width="10%",
    ),
    rx.box(
        "Example",
        bg="lightgreen",
        border_radius="3px",
        width="60%",
    ),
    width="100%",
)
```

## 垂直堆叠（Vstack）

```python demo
rx.vstack(
    rx.box("Example", bg="red", border_radius="3px", width="20%"),
    rx.box(
        "Example",
        bg="orange",
        border_radius="3px",
        width="40%",
    ),
    rx.box(
        "Example",
        bg="yellow",
        border_radius="3px",
        width="60%",
    ),
    rx.box(
        "Example",
        bg="lightblue",
        border_radius="3px",
        width="80%",
    ),
    rx.box(
        "Example",
        bg="lightgreen",
        border_radius="3px",
        width="100%",
    ),
    width="100%",
)
```

## 实际示例

```python demo
rx.hstack(
    rx.box(
        rx.heading("Saving Money", as_="h2"),
        rx.text(
            "Saving money is an art that combines discipline, strategic planning, and the wisdom to foresee future needs and emergencies. It begins with the simple act of setting aside a portion of one's income, creating a buffer that can grow over time through interest or investments.",
            margin_top="0.5em",
        ),
        padding="1em",
        border_width="1px",
    ),
    rx.box(
        rx.heading("Spending Money", as_="h2"),
        rx.text(
            "Spending money is a balancing act between fulfilling immediate desires and maintaining long-term financial health. It's about making choices, sometimes indulging in the pleasures of the moment, and at other times, prioritizing essential expenses.",
            margin_top="0.5em",
        ),
        padding="1em",
        border_width="1px",
    ),
    gap="2em",
)
```
