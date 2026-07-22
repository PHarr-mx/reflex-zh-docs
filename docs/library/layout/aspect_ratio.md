---
components:
  - rx.aspect_ratio
---

```python exec
import reflex as rx
```

# 宽高比（Aspect Ratio）

以所需的宽高比显示内容。

## 基本示例

设置 `ratio` 属性会调整内容的宽度或高度，使 `width` 除以 `height` 等于 `ratio`。
要实现响应式缩放，请将内容的 `width` 或 `height` 设置为 `"100%"`。

```python demo
rx.grid(
    rx.aspect_ratio(
        rx.box(
            "Widescreen 16:9",
            background_color="papayawhip",
            width="100%",
            height="100%",
        ),
        ratio=16 / 9,
    ),
    rx.aspect_ratio(
        rx.box(
            "Letterbox 4:3",
            background_color="orange",
            width="100%",
            height="100%",
        ),
        ratio=4 / 3,
    ),
    rx.aspect_ratio(
        rx.box(
            "Square 1:1",
            background_color="green",
            width="100%",
            height="100%",
        ),
        ratio=1,
    ),
    rx.aspect_ratio(
        rx.box(
            "Portrait 5:7",
            background_color="lime",
            width="100%",
            height="100%",
        ),
        ratio=5 / 7,
    ),
    spacing="2",
    width="25%",
)
```

```md alert warning
# 不要直接在 `aspect_ratio` 组件或其内容上设置 `height` 或 `width`。

应将 `aspect_ratio` 包裹在一个约束了宽度或高度的 `box` 中，然后将内容的宽度和高度设置为 `"100%"`。
```

```python demo
rx.flex(
    *[
        rx.box(
            rx.aspect_ratio(
                rx.image(
                    src="https://web.reflex-assets.dev/other/logo.jpg",
                    alt="Reflex logo",
                    width="100%",
                    height="100%",
                ),
                ratio=ratio,
            ),
            width="20%",
        )
        for ratio in [16 / 9, 3 / 2, 2 / 3, 1]
    ],
    justify="between",
    width="100%",
)
```
