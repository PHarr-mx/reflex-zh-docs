---
components:
  - rx.inset

Inset: |
  lambda **props: rx.card(
      rx.inset(
          rx.image(src="https://web.reflex-assets.dev/other/reflex_banner.png", alt='Reflex banner image', height="auto"),
          **props,
      ),
      width="500px",
  )
---

```python exec
import reflex as rx
```

# 内嵌（Inset）

应用负外边距，使内容延伸到周围容器的边缘。

## 基本示例

将 Inset 组件嵌套在 Card 内部，会使内容从卡片的一边渲染到另一边。

```python demo
rx.card(
    rx.inset(
        rx.image(
            src="https://web.reflex-assets.dev/other/reflex_banner.png",
            alt="Reflex banner image",
            width="100%",
            height="auto",
        ),
        side="top",
        pb="current",
    ),
    rx.text(
        "Reflex is a web framework that allows developers to build their app in pure Python."
    ),
    width="25vw",
)
```

## 其他方向

`side` 属性控制负外边距应用于哪一侧。使用特定侧时，将相对侧的内边距设置为 `current` 很有帮助，这样可以保持内容延伸到父组件边缘时本应具有的相同内边距。

```python demo
rx.card(
    rx.text("The inset below uses a bottom side."),
    rx.inset(
        rx.image(
            src="https://web.reflex-assets.dev/other/reflex_banner.png",
            alt="Reflex banner image",
            width="100%",
            height="auto",
        ),
        side="bottom",
        pt="current",
    ),
    width="25vw",
)
```

```python demo
rx.card(
    rx.flex(
        rx.text(
            "This inset uses a right side, which requires a flex with direction row."
        ),
        rx.inset(
            rx.box(
                background="center/cover url('https://web.reflex-assets.dev/other/reflex_banner.png')",
                height="100%",
            ),
            width="100%",
            side="right",
            pl="current",
        ),
        direction="row",
        width="100%",
    ),
    width="25vw",
)
```

```python demo
rx.card(
    rx.flex(
        rx.inset(
            rx.box(
                background="center/cover url('https://web.reflex-assets.dev/other/reflex_banner.png')",
                height="100%",
            ),
            width="100%",
            side="left",
            pr="current",
        ),
        rx.text(
            "This inset uses a left side, which also requires a flex with direction row."
        ),
        direction="row",
        width="100%",
    ),
    width="25vw",
)
```
