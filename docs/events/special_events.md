```python exec
import reflex as rx
```

# 特殊事件

Reflex 也内置了特殊事件，可以在[参考文档](/docs/api-reference/special-events)中找到。

例如，事件处理器可以在浏览器中触发一个弹窗。

```python demo exec
class SpecialEventsState(rx.State):
    @rx.event
    def alert(self):
        return rx.window_alert("Hello World!")


def special_events_example():
    return rx.button("Alert", on_click=SpecialEventsState.alert)
```

特殊事件也可以直接通过将其附加到事件触发器来在 UI 中触发。

```python
def special_events_example():
    return rx.button("Alert", on_click=rx.window_alert("Hello World!"))
```
