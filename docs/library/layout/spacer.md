---
components:
  - rx.spacer
---

```python exec
import reflex as rx
```

# 间隔（Spacer）

创建一个可调节的空白区域，可用于调整 `flex` 中子元素之间的间距。

```python demo
rx.flex(
    rx.center(rx.text("Example"), bg="lightblue"),
    rx.spacer(),
    rx.center(rx.text("Example"), bg="lightgreen"),
    rx.spacer(),
    rx.center(rx.text("Example"), bg="salmon"),
    width="100%",
)
```

由于 `stack`、`vstack` 和 `hstack` 都是基于 `flex` 构建的，因此也可以在这些组件内部使用 `spacer`。
