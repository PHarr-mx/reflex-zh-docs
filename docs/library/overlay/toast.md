---
components:
  - rx.toast.provider
---

```python exec
import reflex as rx
```

# Toast（轻提示）

`rx.toast` 是一种非阻塞通知，会在一定时间后自动消失。它通常用于在不中断用户工作流的情况下向用户显示消息。

## 用法

你可以将 `rx.toast` 用作任何触发操作的组件的事件处理器。

```python demo
rx.button("Show Toast", on_click=rx.toast("Hello, World!"))
```

### 在 State 中使用

你也可以在状态中使用 `rx.toast`，通过 `yield` 在特定操作触发时显示轻提示。

```python demo exec
import asyncio


class ToastState(rx.State):
    @rx.event
    async def fetch_data(self):
        # 模拟 2 秒延迟获取数据
        await asyncio.sleep(2)
        # 数据获取完成后显示轻提示
        yield rx.toast("Data fetched!")


def render():
    return rx.button("Get Data", on_click=ToastState.fetch_data)
```

## 交互

如果你想与轻提示进行交互，可以使用一些 prop 来自定义行为。

通过向 `action` 或 `cancel` prop 传递 `ToastAction`，你可以在点击轻提示或关闭轻提示时触发操作。

```python demo
rx.button(
    "Show Toast", on_click=rx.toast("Hello, World!", duration=5000, close_button=True)
)
```

### 预设

`rx.toast` 提供了一些预设，用于显示不同类型的轻提示。

```python demo
rx.hstack(
    rx.button("Success", on_click=rx.toast.success("Success!"), color_scheme="green"),
    rx.button("Error", on_click=rx.toast.error("Error!"), color_scheme="red"),
    rx.button("Warning", on_click=rx.toast.warning("Warning!"), color_scheme="orange"),
    rx.button("Info", on_click=rx.toast.info("Info!"), color_scheme="blue"),
)
```

### 自定义

如果预设不能满足你的需求，你可以通过向 `rx.toast` 或 `rx.toast.options` 传递 kwargs 来自定义轻提示。

```python demo
rx.button(
    "Custom",
    on_click=rx.toast(
        "Custom Toast!",
        position="top-right",
        style={
            "background-color": "green",
            "color": "white",
            "border": "1px solid green",
            "border-radius": "0.53m",
        },
    ),
)
```

以下 prop 可用于自定义：

- `description`：`str | Var`：轻提示的描述，渲染在标题下方。
- `close_button`：`bool`：是否显示关闭按钮。
- `invert`：`bool`：在浅色模式下使用深色轻提示，反之亦然。
- `important`：`bool`：控制轻提示对屏幕阅读器的敏感度。
- `duration`：`int`：自动关闭轻提示前应经过的时间（毫秒）。
- `position`：`LiteralPosition`：轻提示的位置。
- `dismissible`：`bool`：如果为 false，将阻止用户关闭轻提示。
- `action`：`ToastAction`：渲染一个主要按钮，点击它会关闭轻提示。
- `cancel`：`ToastAction`：渲染一个次要按钮，点击它会关闭轻提示。
- `id`：`str | Var`：轻提示的自定义 id。
- `unstyled`：`bool`：移除默认样式，便于自定义。
- `style`：`Style`：轻提示的自定义样式。
- `on_dismiss`：`Any`：当点击关闭按钮或滑动关闭轻提示时调用的函数。
- `on_auto_close`：`Any`：轻提示在超时（`duration` prop）后自动消失时调用的函数。

## Toast Provider（轻提示提供者）

使用 `rx.toast` 函数需要在应用中有一个 toast provider。

`rx.toast.provider` 是一个提供轻提示显示上下文的组件。它应放置在应用的根级别。

```md alert warning
# 在大多数情况下，你不需要直接包含此组件，因为它已经作为 `overlay_component` 包含在 `rx.app` 中，用于显示连接错误。
```
