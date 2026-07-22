---
components:
  - rx.button

Button: |
  lambda **props: rx.button("Basic Button", **props)
---

```python exec
import reflex as rx
```

# 按钮（Button）

按钮（Button）是应用界面中的基本元素，用户可以点击按钮来触发事件。

## 基本示例

当按钮被点击时，会调用 `on_click` 触发器。

```python demo exec
class CountState(rx.State):
    count: int = 0

    @rx.event
    def increment(self):
        self.count += 1

    @rx.event
    def decrement(self):
        self.count -= 1


def counter():
    return rx.flex(
        rx.button(
            "Decrement",
            color_scheme="red",
            on_click=CountState.decrement,
        ),
        rx.heading(CountState.count, as_="h2"),
        rx.button(
            "Increment",
            color_scheme="grass",
            on_click=CountState.increment,
        ),
        spacing="3",
    )
```

### 加载和禁用

`loading` 属性用于指示按钮触发的操作正在进行中。当设置为 `True` 时，按钮会显示一个加载动画，为用户提供操作正在处理的视觉反馈。这也能防止按钮在加载状态下被多次点击。默认情况下，`loading` 设置为 `False`。

`disabled` 属性同样会阻止按钮被点击，但不会显示加载动画。

```python demo
rx.flex(
    rx.button("Regular"),
    rx.button("Loading", loading=True),
    rx.button("Disabled", disabled=True),
    spacing="2",
)
```
