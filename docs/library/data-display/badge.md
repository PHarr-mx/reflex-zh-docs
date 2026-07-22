---
components:
  - rx.badge

Badge: |
  lambda **props: rx.badge("Basic Badge", **props)
---

# 徽章（Badge）

```python exec
import reflex as rx
```

徽章（Badge）用于突出显示项目的状态，以便快速识别。

## 基本示例

要创建仅包含文本的徽章组件，请将文本作为参数传入。

```python demo
rx.badge("New")
```

## 样式


### 尺寸

`size` 属性控制徽章的大小和内边距。可取值为 `"1" | "2"`，默认值为 `"1"`。

```python demo
rx.flex(
    rx.badge("New"),
    rx.badge("New", size="1"),
    rx.badge("New", size="2"),
    align="center",
    spacing="2",
)
```

### 变体

`variant` 属性控制徽章的视觉样式。支持的变体类型为 `"solid" | "soft" | "surface" | "outline"`。默认变体为 `"soft"`。

```python demo
rx.flex(
    rx.badge("New", variant="solid"),
    rx.badge("New", variant="soft"),
    rx.badge("New"),
    rx.badge("New", variant="surface"),
    rx.badge("New", variant="outline"),
    spacing="2",
)
```

### 配色方案

`color_scheme` 属性设置特定颜色，忽略全局主题。

```python demo
rx.flex(
    rx.badge("New", color_scheme="indigo"),
    rx.badge("New", color_scheme="cyan"),
    rx.badge("New", color_scheme="orange"),
    rx.badge("New", color_scheme="crimson"),
    spacing="2",
)
```

### 高对比度

`high_contrast` 属性增加后备文本与背景之间的颜色对比度。

```python demo
rx.flex(
    rx.flex(
        rx.badge("New", variant="solid"),
        rx.badge("New", variant="soft"),
        rx.badge("New", variant="surface"),
        rx.badge("New", variant="outline"),
        spacing="2",
    ),
    rx.flex(
        rx.badge("New", variant="solid", high_contrast=True),
        rx.badge("New", variant="soft", high_contrast=True),
        rx.badge("New", variant="surface", high_contrast=True),
        rx.badge("New", variant="outline", high_contrast=True),
        spacing="2",
    ),
    direction="column",
    spacing="2",
)
```

### 圆角

`radius` 属性设置特定的圆角值，忽略全局主题。可取值为 `"none" | "small" | "medium" | "large" | "full"`。

```python demo
rx.flex(
    rx.badge("New", radius="none"),
    rx.badge("New", radius="small"),
    rx.badge("New", radius="medium"),
    rx.badge("New", radius="large"),
    rx.badge("New", radius="full"),
    spacing="3",
)
```

## 综合示例

徽章内部可以包含更复杂的元素。此示例使用 `flex` 组件来正确对齐图标和文本，并使用 `gap` 属性确保两者之间有舒适的间距。

```python demo
rx.badge(
    rx.flex(
        rx.icon(tag="arrow_up"),
        rx.text("8.8%"),
        spacing="1",
    ),
    color_scheme="grass",
)
```
