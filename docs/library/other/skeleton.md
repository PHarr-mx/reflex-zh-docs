---
description: Skeleton, a loading placeholder component for content that is not yet available.
components:
  - rx.skeleton
---

```python exec
import reflex as rx
```

# Skeleton（加载占位符）

`Skeleton` 是一个加载占位符组件，在内容加载期间充当视觉占位符。
它有助于保持布局结构，并在等待最终内容时为用户提供进度感。

```python demo
(
    rx.vstack(
        rx.skeleton(rx.button("button-small"), height="10px"),
        rx.skeleton(rx.button("button-big"), height="20px"),
        rx.skeleton(
            rx.text("Text is loaded."),
            loading=True,
        ),
        rx.skeleton(
            rx.text("Text is already loaded."),
            loading=False,
        ),
    ),
)
```

在文本中使用 `Skeleton` 时，应包裹文本本身而非父元素，以获得相同大小的占位符。

使用 loading prop 来控制显示骨架屏还是其子元素。Skeleton 在子元素隐藏时会保留其尺寸，并禁用交互元素。
