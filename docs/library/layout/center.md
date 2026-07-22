---
components:
  - rx.center

Center: |
  lambda **props: rx.center(
      rx.card("Card 1", size="2"),
      rx.card("Card 2", size="2"),
      rx.card("Card 3", size="2"),
      gap="0.75rem",
      width="100%",
      height="10rem",
      border_radius="0.5rem",
      **props,
  )
---

```python exec
import reflex as rx
```

# 居中（Center）

`Center` 是一个将其子元素在自身内部居中的组件。它基于 `flex` 组件，因此继承了其所有属性。

```python demo
rx.center(
    rx.text("Hello World!"),
    border_radius="15px",
    border_width="thick",
    width="50%",
)
```
