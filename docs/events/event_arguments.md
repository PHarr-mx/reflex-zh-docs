```python exec
import reflex as rx
```

# 事件参数

事件处理器的签名需要与事件触发器定义的参数数量匹配。如果事件处理器接受两个参数，那么事件触发器必须能够提供两个参数。

以下是一个简单的示例：

```python demo exec
class EventArgStateSlider(rx.State):
    value: int = 50

    @rx.event
    def set_end(self, value: list[int | float]):
        self.value = value[0]


def slider_max_min_step():
    return rx.vstack(
        rx.heading(EventArgStateSlider.value, as_="h2"),
        rx.slider(
            default_value=40,
            on_value_commit=EventArgStateSlider.set_end,
        ),
        width="100%",
    )
```

这里的事件触发器是 `on_value_commit`，它在交互结束时值发生变化时被调用。该事件触发器传递一个参数，即滑块的值。因此，由事件触发器调用的事件处理器必须接受一个参数，即这里的 `value`。

以下是一个表单示例：

```python demo exec
class EventArgState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        """Handle the form submit."""
        self.form_data = form_data


def event_arg_example():
    return rx.vstack(
        rx.form(
            rx.vstack(
                rx.input(
                    placeholder="Name",
                    name="name",
                ),
                rx.checkbox("Checked", name="check"),
                rx.button("Submit", type="submit"),
            ),
            on_submit=EventArgState.handle_submit,
            reset_on_submit=True,
        ),
        rx.divider(),
        rx.heading("Results", as_="h2"),
        rx.text(EventArgState.form_data.to_string()),
    )
```

在这个示例中，事件触发器是表单的 `on_submit` 事件。事件处理器是 `handle_submit`。`on_submit` 事件触发器传递一个参数（表单数据的字典）给 `handle_submit` 事件处理器。由于 `on_submit` 事件触发器传递一个参数，`handle_submit` 事件处理器必须接受一个参数。

当 EventHandler 接受的参数数量与事件触发器提供的参数数量不同时，将引发 `EventHandlerArgMismatch` 错误。

## 向事件处理器传递额外参数

在某些使用场景中，你可能希望向事件处理器传递额外参数。为此，你可以将事件触发器绑定到 lambda，由 lambda 使用你想要的参数调用事件处理器。

尝试在下面的输入框中输入一种颜色，然后点击输入框外部以改变输入框的颜色。

```python demo exec
class ArgState(rx.State):
    colors: list[str] = ["rgba(245,168,152)", "MediumSeaGreen", "#DEADE3"]

    @rx.event
    def change_color(self, color: str, index: int):
        self.colors[index] = color


def event_arguments_example():
    return rx.hstack(
        rx.input(
            default_value=ArgState.colors[0],
            on_blur=lambda c: ArgState.change_color(c, 0),
            bg=ArgState.colors[0],
        ),
        rx.input(
            default_value=ArgState.colors[1],
            on_blur=lambda c: ArgState.change_color(c, 1),
            bg=ArgState.colors[1],
        ),
        rx.input(
            default_value=ArgState.colors[2],
            on_blur=lambda c: ArgState.change_color(c, 2),
            bg=ArgState.colors[2],
        ),
    )
```

在此场景中，我们希望向事件处理器 `change_color` 传递两个参数：颜色值和要更改的颜色索引。

`on_blur` 事件触发器将输入框的文本作为参数传递给 lambda，而 lambda 则使用该文本和输入框的索引调用 `change_color` 事件处理器。

当 lambda 接受的参数数量与事件触发器提供的参数数量不同时，将引发 `EventFnArgMismatch` 错误。

```md alert warning
# 事件处理器参数应提供类型注解。

与 state vars 一样，请确保为事件处理器中的参数提供正确的类型注解。
```

## 部分参数事件（进阶）

_在 v0.5.0 中添加_

Reflex 中的事件参数是按位置传递的。任何未传递给 EventHandler 的额外参数将在事件触发时由事件触发器填充。

以下两个代码示例是等效的：

```python
# Use a lambda to pass event trigger args to the EventHandler.
rx.text(on_blur=lambda v: MyState.handle_update("field1", v))

# Create a partial that passes event trigger args for any args not provided to the EventHandler.
rx.text(on_blur=MyState.handle_update("field1"))
```
