---
components:
  - rx.section

Section: |
  lambda **props: rx.section(
      rx.text("Section content", color="black"),
      background="white",
      border_radius="0.5rem",
      width="100%",
      justify="center",
      border="1px solid var(--gray-3)",
      align="center",
      display="flex",
      padding="4rem",
      **props,
  )
---

```python exec
import reflex as rx
```

# 区块（Section）

表示页面内容的一个区块，默认提供垂直内边距。

这主要是一个语义化组件，用于将相关的文本内容组合在一起。

## 基本示例

```python demo
rx.box(
    rx.section(
        rx.heading("First", as_="h2"),
        rx.text("This is the first content section"),
        padding_left="12px",
        padding_right="12px",
        background_color="var(--gray-2)",
    ),
    rx.section(
        rx.heading("Second", as_="h2"),
        rx.text("This is the second content section"),
        padding_left="12px",
        padding_right="12px",
        background_color="var(--gray-2)",
    ),
    width="100%",
)
```
