---
components:
  - rx.callout.root
  - rx.callout.icon
  - rx.callout.text
---

```python exec
import reflex as rx
```

# 标注（Callout）

`callout` 是一条用于吸引用户注意力的简短消息。

```python demo
rx.callout.root(
    rx.callout.icon(rx.icon(tag="info")),
    rx.callout.text(
        "You will need admin privileges to install and access this application."
    ),
)
```

`callout` 组件由 `callout.root` 组成，它将 `callout.icon` 和 `callout.text` 部分组合在一起。该组件基于 `div` 元素，支持通用的 margin 属性。

`callout.icon` 为与 `callout` 关联的 `icon` 提供宽度和高度。该组件基于 `div` 元素。请参阅[**图标**组件以查看所有可用图标。](/docs/library/data-display/icon/)

`callout.text` 渲染标注文本。该组件基于 `p` 元素。

## 作为警告

```python demo
rx.callout.root(
    rx.callout.icon(rx.icon(tag="triangle_alert")),
    rx.callout.text(
        "Access denied. Please contact the network administrator to view this page."
    ),
    color_scheme="red",
    role="alert",
)
```

## 样式

### 尺寸

使用 `size` 属性控制大小。

```python demo
rx.flex(
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        size="3",
    ),
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        size="2",
    ),
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        size="1",
    ),
    direction="column",
    spacing="3",
    align="start",
)
```

### 变体

使用 `variant` 属性控制视觉样式。默认设置为 `soft`。

```python demo
rx.flex(
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        variant="soft",
    ),
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        variant="surface",
    ),
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        variant="outline",
    ),
    direction="column",
    spacing="3",
)
```

### 颜色

使用 `color_scheme` 属性指定特定颜色，忽略全局主题。

```python demo
rx.flex(
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        color_scheme="blue",
    ),
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        color_scheme="green",
    ),
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        color_scheme="red",
    ),
    direction="column",
    spacing="3",
)
```

### 高对比度

使用 `high_contrast` 属性增加额外的对比度。

```python demo
rx.flex(
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
    ),
    rx.callout.root(
        rx.callout.icon(rx.icon(tag="info")),
        rx.callout.text(
            "You will need admin privileges to install and access this application."
        ),
        high_contrast=True,
    ),
    direction="column",
    spacing="3",
)
```
