---
components:
  - rx.callout

Callout: |
  lambda **props: rx.callout("Basic Callout", icon="search", **props)

CalloutRoot: |
  lambda **props: rx.callout.root(
      rx.callout.icon(rx.icon(tag="info")),
      rx.callout.text("You will need admin privileges to install and access this application."),
      **props
  )
---

```python exec
import reflex as rx
```

# 标注（Callout）

`callout` 是一条用于吸引用户注意力的简短消息。

```python demo
rx.callout(
    "You will need admin privileges to install and access this application.",
    icon="info",
)
```

`icon` 属性允许向 `callout` 组件传入一个图标。请参阅[**图标**组件以查看所有可用图标。](/docs/library/data-display/icon)

## 作为警告

```python demo
rx.callout(
    "Access denied. Please contact the network administrator to view this page.",
    icon="triangle_alert",
    color_scheme="red",
    role="alert",
)
```

## 样式

### 尺寸

使用 `size` 属性控制大小。

```python demo
rx.flex(
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
        size="3",
    ),
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
        size="2",
    ),
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
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
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
        variant="soft",
    ),
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
        variant="surface",
    ),
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
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
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
        color_scheme="blue",
    ),
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
        color_scheme="green",
    ),
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
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
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
    ),
    rx.callout(
        "You will need admin privileges to install and access this application.",
        icon="info",
        high_contrast=True,
    ),
    direction="column",
    spacing="3",
)
```
