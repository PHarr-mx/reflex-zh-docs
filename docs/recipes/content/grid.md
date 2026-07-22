```python exec
import reflex as rx
```

# 网格（Grid）

一个简单的响应式网格布局。我们将列数指定为 `grid_template_columns` 属性的列表。网格将根据屏幕尺寸自动调整列数。

有关详细信息，请参阅[响应式文档页面](/docs/styling/responsive)。

## 卡片

```python demo
rx.grid(
    rx.foreach(
        rx.Var.range(12),
        lambda i: rx.card(f"Card {i + 1}", height="10vh"),
    ),
    gap="1rem",
    grid_template_columns=[
        "1fr",
        "repeat(2, 1fr)",
        "repeat(2, 1fr)",
        "repeat(3, 1fr)",
        "repeat(4, 1fr)",
    ],
    width="100%",
)
```

## 内嵌卡片

```python demo
rx.grid(
    rx.foreach(
        rx.Var.range(12),
        lambda i: rx.card(
            rx.inset(
                rx.image(
                    src="https://web.reflex-assets.dev/other/reflex_banner.png",
                    alt="Reflex banner image",
                    width="100%",
                    height="auto",
                ),
                side="top",
                pb="current",
            ),
            rx.text(
                f"Card {i + 1}",
            ),
        ),
    ),
    gap="1rem",
    grid_template_columns=[
        "1fr",
        "repeat(2, 1fr)",
        "repeat(2, 1fr)",
        "repeat(3, 1fr)",
        "repeat(4, 1fr)",
    ],
    width="100%",
)
```
