---
components:
  - rx.container
---

```python exec
import reflex as rx
```

# 容器（Container）

限制页面内容的最大宽度，同时保持灵活的外边距以实现响应式布局。

Container 通常用于包裹页面的主要内容。

## 基本示例

```python demo
rx.box(
    rx.container(
        rx.card("This content is constrained to a max width of 448px.", width="100%"),
        size="1",
    ),
    rx.container(
        rx.card("This content is constrained to a max width of 688px.", width="100%"),
        size="2",
    ),
    rx.container(
        rx.card("This content is constrained to a max width of 880px.", width="100%"),
        size="3",
    ),
    rx.container(
        rx.card("This content is constrained to a max width of 1136px.", width="100%"),
        size="4",
    ),
    background_color="var(--gray-3)",
    width="100%",
)
```
