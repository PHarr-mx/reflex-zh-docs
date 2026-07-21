```python exec
import reflex as rx
import datetime
```

# 事件动作

在 Reflex 中，事件动作（event action）是在前端处理事件期间或之后发生的特殊行为。

事件动作可以修改浏览器处理 DOM 事件的方式，或者在事件被后端处理之前对其进行节流（throttle）和防抖（debounce）。

事件动作通过访问所有 EventHandler 和 EventSpec 上存在的属性和方法来指定。

## DOM 事件传播

_在 v0.3.2 中添加_

### prevent_default

`.prevent_default` 动作可阻止浏览器对该动作的默认行为。该动作可以添加到任何现有事件上，也可以通过指定 `rx.prevent_default` 作为事件处理器来单独使用。

一个常见的用例是阻止点击链接时的导航。

```python demo
rx.link(
    "This Link Does Nothing", href="https://reflex.dev/", on_click=rx.prevent_default
)
```

```python demo exec
class LinkPreventDefaultState(rx.State):
    status: bool = False

    @rx.event
    def toggle_status(self):
        self.status = not self.status


def prevent_default_example():
    return rx.vstack(
        rx.heading(f"The value is {LinkPreventDefaultState.status}", as_="h2"),
        rx.link(
            "Toggle Value",
            href="https://reflex.dev/",
            on_click=LinkPreventDefaultState.toggle_status.prevent_default,
        ),
    )
```

### stop_propagation

`.stop_propagation` 动作阻止事件传播到父级元素。

该动作常用于可点击元素包含嵌套按钮时，这些按钮不应触发父元素的点击事件。

在以下示例中，第一个按钮使用了 `.stop_propagation` 来阻止点击事件传播到外层的 vstack。第二个按钮没有使用 `.stop_propagation`，因此点击事件也会被外层 vstack 上的 `on_click` 处理。

```python demo exec
class StopPropagationState(rx.State):
    where_clicked: list[str] = []

    @rx.event
    def handle_click(self, where: str):
        self.where_clicked.append(where)

    @rx.event
    def handle_reset(self):
        self.where_clicked = []


def stop_propagation_example():
    return rx.vstack(
        rx.button(
            "btn1 - Stop Propagation",
            on_click=StopPropagationState.handle_click("btn1").stop_propagation,
        ),
        rx.button(
            "btn2 - Normal Propagation",
            on_click=StopPropagationState.handle_click("btn2"),
        ),
        rx.foreach(StopPropagationState.where_clicked, rx.text),
        rx.button(
            "Reset",
            on_click=StopPropagationState.handle_reset.stop_propagation,
        ),
        padding="2em",
        border=f"1px dashed {rx.color('accent', 5)}",
        on_click=StopPropagationState.handle_click("outer"),
    )
```

## 节流与防抖

_在 v0.5.0 中添加_

对于触发频繁的事件，对其进行节流（throttle）或防抖（debounce）有助于避免网络延迟并提高性能。这两个动作都接受一个参数，即延迟时间（毫秒）。

### throttle

`.throttle` 动作限制在给定时间范围内事件被处理的次数。它对于 `on_scroll` 和 `on_mouse_move` 等触发非常频繁、在后端处理时会导致迟滞的事件很有用。

```md alert warning
# 节流事件会被丢弃。

在节流周期处于活跃状态时触发的任何事件都不会被最终投递。节流不适用于最终负载包含必须处理的数据的事件，如 `on_change`。
```

在以下示例中，`on_scroll` 事件被节流为每半秒只触发一次。

```python demo exec
class ThrottleState(rx.State):
    last_scroll: datetime.datetime | None

    @rx.event
    def handle_scroll(self):
        self.last_scroll = datetime.datetime.now(datetime.timezone.utc)


def scroll_box():
    return rx.scroll_area(
        rx.heading("Scroll Me", as_="h2"),
        *[rx.text(f"Item {i}") for i in range(100)],
        height="75px",
        width="50%",
        border=f"1px solid {rx.color('accent', 5)}",
        on_scroll=ThrottleState.handle_scroll.throttle(500),
    )


def throttle_example():
    return (
        scroll_box(),
        rx.text(
            "Last Scroll Event: ",
            rx.moment(ThrottleState.last_scroll, format="HH:mm:ss.SSS"),
        ),
    )
```

```md alert info
# 事件动作可链式调用

事件动作可以链式组合以创建更复杂的事件处理行为。例如，你可以同时对一个事件进行节流并阻止其默认行为：`on_click=MyState.handle_click.throttle(500).prevent_default`。
```

### debounce

`.debounce` 动作延迟事件的处理，直到指定的超时时间到期。如果在超时期间触发了另一个事件，则计时器重置，原始事件被丢弃。

防抖适用于处理一系列事件的最终结果，例如移动滑块。

```md alert warning
# 防抖事件会被丢弃。

当在防抖期间触发了新事件时，原始事件会被丢弃。防抖不适用于每个负载包含必须处理的唯一数据的事件，如 `on_key_down`。
```

在以下示例中，滑块的 `on_change` 处理器 `update_value` 仅当滑块值在半秒内没有变化时才会在后端触发。

```python demo exec
class DebounceState(rx.State):
    settled_value: int = 50

    @rx.event
    def update_value(self, value: list[int | float]):
        self.settled_value = value[0]


def debounced_slider():
    return rx.slider(
        key=rx.State.router.session.session_id,
        default_value=[DebounceState.settled_value],
        on_change=DebounceState.update_value.debounce(500),
        width="100%",
    )


def debounce_example():
    return rx.vstack(
        debounced_slider(),
        rx.text(f"Settled Value: {DebounceState.settled_value}"),
    )
```

```md alert info
# 为什么要在滑块上设置 key？

将 `key` 设置为 `session_id`，并结合动态的 `default_value`，可以确保页面刷新时组件会重新渲染以反映状态中更新后的 default_value。

如果没有设置 `key`，滑块在页面重新加载后始终显示原始的 `settled_value`，而不是其当前值。
```

## 临时事件（Temporal Events）

_在 [v0.6.6](https://github.com/reflex-dev/reflex/releases/tag/v0.6.6) 中添加_

### temporal

`.temporal` 动作阻止事件在后端不可用时排队。这对于非关键事件很有用，你不希望它们在暂时的连接问题期间堆积。

```md alert warning
# 临时事件在后端不可用时会被丢弃。

当后端不可用时，带有 `.temporal` 动作的事件将被丢弃，而不是排队等待后续处理。仅将此用于在连接问题期间丢失一些交互是可以接受的事件。
```

在以下示例中，`rx.moment` 组件通过 `interval` 和 `on_change` 使用 `.temporal` 来阻止定期更新在后端不可用时排队：

```python demo exec
class TemporalState(rx.State):
    current_time: str = ""

    @rx.event
    def update_time(self):
        self.current_time = datetime.datetime.now().strftime("%H:%M:%S")


def temporal_example():
    return rx.vstack(
        rx.heading("Current Time:", as_="h2"),
        rx.heading(TemporalState.current_time, as_="h2"),
        rx.moment(
            interval=1000,
            on_change=TemporalState.update_time.temporal,
        ),
        rx.text("Time updates will not be queued if the backend is down."),
    )
```
