---
components:
  - rx.avatar
Avatar: |
  lambda **props: rx.hstack(rx.avatar(src="https://web.reflex-assets.dev/other/logo.jpg", **props), rx.avatar(fallback="RX", **props), spacing="3")
---

# 头像（Avatar）

```python exec
import reflex as rx
```

头像（Avatar）组件用于表示用户，并显示其个人资料图片或后备文本（如姓名首字母）。

## 基本示例

要创建带有图片的头像组件，请将图片 URL 作为 `src` 属性传入。

```python demo
rx.avatar(src="https://web.reflex-assets.dev/other/logo.jpg")
```

要显示文本（如姓名首字母），请设置 `fallback` 属性而不传入 `src` 属性。

```python demo
rx.avatar(fallback="RX")
```

## 样式


### 尺寸

`size` 属性控制头像的大小和间距。可接受的尺寸范围为 `"1"` 到 `"9"`，默认值为 `"3"`。

```python demo
rx.flex(
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", size="1"
    ),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", size="2"
    ),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", size="3"
    ),
    rx.avatar(src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX"),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", size="4"
    ),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", size="5"
    ),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", size="6"
    ),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", size="7"
    ),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", size="8"
    ),
    spacing="1",
)
```

### 变体

`variant` 属性控制头像后备文本的视觉样式。变体可以是 `"solid"` 或 `"soft"`。默认值为 `"soft"`。

```python demo
rx.flex(
    rx.avatar(fallback="RX", variant="solid"),
    rx.avatar(fallback="RX", variant="soft"),
    rx.avatar(fallback="RX"),
    spacing="2",
)
```

### 配色方案

`color_scheme` 属性为后备文本设置特定颜色，忽略全局主题。

```python demo
rx.flex(
    rx.avatar(fallback="RX", color_scheme="indigo"),
    rx.avatar(fallback="RX", color_scheme="cyan"),
    rx.avatar(fallback="RX", color_scheme="orange"),
    rx.avatar(fallback="RX", color_scheme="crimson"),
    spacing="2",
)
```

### 高对比度

`high_contrast` 属性增加后备文本与背景之间的颜色对比度。

```python demo
rx.grid(
    rx.avatar(fallback="RX", variant="solid"),
    rx.avatar(fallback="RX", variant="solid", high_contrast=True),
    rx.avatar(fallback="RX", variant="soft"),
    rx.avatar(fallback="RX", variant="soft", high_contrast=True),
    rows="2",
    spacing="2",
    flow="column",
)
```

### 圆角

`radius` 属性设置特定的圆角值，忽略全局主题。可取值为 `"none" | "small" | "medium" | "large" | "full"`。

```python demo
rx.grid(
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", radius="none"
    ),
    rx.avatar(fallback="RX", radius="none"),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg",
        fallback="RX",
        radius="small",
    ),
    rx.avatar(fallback="RX", radius="small"),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg",
        fallback="RX",
        radius="medium",
    ),
    rx.avatar(fallback="RX", radius="medium"),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg",
        fallback="RX",
        radius="large",
    ),
    rx.avatar(fallback="RX", radius="large"),
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RX", radius="full"
    ),
    rx.avatar(fallback="RX", radius="full"),
    rows="2",
    spacing="2",
    flow="column",
)
```

### 后备文本

`fallback` 属性指定当 `src` 无法加载时渲染的文本。

```python demo
rx.flex(
    rx.avatar(fallback="RX"),
    rx.avatar(fallback="PC"),
    spacing="2",
)
```

## 综合示例

作为用户个人资料页面的一部分，头像组件用于显示用户的个人资料图片，后备文本显示用户的姓名首字母。文本组件显示用户的全名和用户名，按钮组件显示编辑资料按钮。

```python demo
rx.flex(
    rx.avatar(
        src="https://web.reflex-assets.dev/other/logo.jpg", fallback="RU", size="9"
    ),
    rx.text("Reflex User", weight="bold", size="4"),
    rx.text("@reflexuser", color_scheme="gray"),
    rx.button("Edit Profile", color_scheme="indigo", variant="solid"),
    direction="column",
    spacing="1",
)
```
