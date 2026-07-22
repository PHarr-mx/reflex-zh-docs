---
components:
  - rx.spinner
---

# 加载指示器（Spinner）

加载指示器（Spinner）用于在任务进行中显示动画加载指示。

```python exec
import reflex as rx
```

```python demo
rx.spinner()
```

## 基本示例

加载指示器可以有不同的尺寸。

```python demo
rx.vstack(
    rx.hstack(
        rx.spinner(size="1"),
        rx.spinner(size="2"),
        rx.spinner(size="3"),
        align="center",
        gap="1em",
    )
)
```

## 与按钮配合使用

按钮有自己的 loading 属性，会自动组合一个加载指示器。

```python demo
rx.button("Bookmark", loading=True)
```

## 按钮内的加载指示器

如果按钮内有图标，你可以使用按钮的禁用状态，并将图标包裹在独立的 rx.spinner 中，以实现更精致的设计。

```python demo
rx.button(rx.spinner(loading=True), "Bookmark", disabled=True)
```
