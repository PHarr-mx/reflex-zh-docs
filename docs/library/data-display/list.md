---
components:
  - rx.list.item
  - rx.list.ordered
  - rx.list.unordered
---

```python exec
import reflex as rx
```

# 列表（List）

`list` 是一个用于显示项目列表的组件，默认垂直堆叠。`list` 可以是 `ordered`（有序）或 `unordered`（无序）的。它基于 `flex` 组件，因此继承了其所有属性。

`list.unordered` 使用项目符号来显示列表项。

```python demo
rx.list.unordered(
    rx.list.item("Example 1"),
    rx.list.item("Example 2"),
    rx.list.item("Example 3"),
)
```

`list.ordered` 使用数字来显示列表项。

```python demo
rx.list.ordered(
    rx.list.item("Example 1"),
    rx.list.item("Example 2"),
    rx.list.item("Example 3"),
)
```

通过将 `list_style_type` 属性设置为 `none`，`list.unordered()` 和 `list.ordered()` 可以不显示项目符号或数字。
这实际上与使用 `list()` 组件效果相同。

```python demo
rx.hstack(
    rx.list(
        rx.list.item("Example 1"),
        rx.list.item("Example 2"),
        rx.list.item("Example 3"),
    ),
    rx.list.unordered(
        rx.list.item("Example 1"),
        rx.list.item("Example 2"),
        rx.list.item("Example 3"),
        list_style_type="none",
    ),
)
```

列表也可以与图标一起使用。

```python demo
rx.list(
    rx.list.item(
        rx.icon("circle_check_big", color="green"),
        " Allowed",
    ),
    rx.list.item(
        rx.icon("octagon_x", color="red"),
        " Not",
    ),
    rx.list.item(rx.icon("settings", color="grey"), " Settings"),
    list_style_type="none",
)
```
