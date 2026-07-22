```python exec
import reflex as rx
```

# 多列和行布局

一个简单的响应式多列和行布局。我们将列/行数指定为 `flex_direction` 属性的列表。布局将根据屏幕尺寸自动调整列/行数。

有关详细信息，请参阅[响应式文档页面](/docs/styling/responsive)。

## 列

```python demo
rx.flex(
    rx.box(bg=rx.color("accent", 3), width="100%", height="100%"),
    rx.box(bg=rx.color("accent", 5), width="100%", height="100%"),
    rx.box(bg=rx.color("accent", 7), width="100%", height="100%"),
    bg=rx.color("accent", 10),
    spacing="4",
    padding="1em",
    flex_direction=["column", "column", "row"],
    height="600px",
    width="100%",
)
```

```python demo
rx.flex(
    rx.box(bg=rx.color("accent", 3), width="100%", height="100%"),
    rx.box(
        bg=rx.color("accent", 5),
        width=["100%", "100%", "50%"],
        height=["50%", "50%", "100%"],
    ),
    rx.box(bg=rx.color("accent", 7), width="100%", height="100%"),
    rx.box(
        bg=rx.color("accent", 9),
        width=["100%", "100%", "50%"],
        height=["50%", "50%", "100%"],
    ),
    bg=rx.color("accent", 10),
    spacing="4",
    padding="1em",
    flex_direction=["column", "column", "row"],
    height="600px",
    width="100%",
)
```

## 行

```python demo
rx.flex(
    rx.flex(
        rx.box(bg=rx.color("accent", 3), width=["100%", "100%", "50%"], height="100%"),
        rx.box(bg=rx.color("accent", 5), width=["100%", "100%", "50%"], height="100%"),
        width="100%",
        height="100%",
        spacing="4",
        flex_direction=["column", "column", "row"],
    ),
    rx.box(bg=rx.color("accent", 7), width="100%", height="50%"),
    rx.box(bg=rx.color("accent", 9), width="100%", height="50%"),
    bg=rx.color("accent", 10),
    spacing="4",
    padding="1em",
    flex_direction="column",
    height="600px",
    width="100%",
)
```
