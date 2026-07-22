```python exec
import reflex as rx
```

# 组件状态（Component State）

_0.4.6 版本新增_。

定义 `rx.ComponentState` 的子类会创建一种特殊类型的状态，它与组件的
实例绑定，而不是全局存在于应用中。组件状态将
[UI 代码](/docs/ui/overview)与状态[变量（Vars）](/docs/vars/base-vars)和
[事件处理程序（Event Handlers）](/docs/events/events-overview)结合在一起，
对于创建彼此独立运行的可重用组件非常有用。

```md alert warning
# ComponentState 不能在 `rx.foreach()` 内部使用，因为它只会为循环中的所有元素创建一个状态实例。foreach 的每次迭代将共享相同的状态，这可能导致意外行为。
```

## 使用 ComponentState

```python demo exec
class ReusableCounter(rx.ComponentState):
    count: int = 0

    @rx.event
    def set_count(self, value: int):
        self.count = value

    @rx.event
    def increment(self):
        self.count += 1

    @rx.event
    def decrement(self):
        self.count -= 1

    @classmethod
    def get_component(cls, **props):
        return rx.hstack(
            rx.button("Decrement", on_click=cls.decrement),
            rx.text(cls.count),
            rx.button("Increment", on_click=cls.increment),
            **props,
        )


reusable_counter = ReusableCounter.create


def multiple_counters():
    return rx.vstack(
        reusable_counter(),
        reusable_counter(),
        reusable_counter(),
    )
```

在 `ReusableCounter` 类上定义的变量和事件处理程序
类似于普通的 State 类，但将限定在组件实例的范围内。每次创建
`reusable_counter` 时，也会为该组件实例创建一个新的状态类。

`get_component` 类方法用于定义组件的 UI 并将其链接到 State，
通过 `cls` 参数访问。返回的组件也可以引用其他状态，但
`cls` 始终是返回的组件唯一的 `ComponentState` 实例。

## 传递属性

与普通 Component 类似，`ComponentState.create` 类方法接受任意的
`*children` 和 `**props` 参数，并默认将它们传递给你的 `get_component` 类方法。
这些参数可用于自定义组件，通过应用默认值或
将属性传递给某些子组件。

```python eval
rx.divider()
```

在以下示例中，我们实现了一个可编辑文本组件，允许用户点击
文本将其变为输入字段。如果用户没有提供自己的 `value` 或 `on_change`
属性，则将使用 `EditableText` 类中定义的默认值。

```python demo exec
class EditableText(rx.ComponentState):
    text: str = "Click to edit"
    original_text: str
    editing: bool = False

    @rx.event
    def set_text(self, value: str):
        self.text = value

    @rx.event
    def start_editing(self, original_text: str):
        self.original_text = original_text
        self.editing = True

    @rx.event
    def stop_editing(self):
        self.editing = False
        self.original_text = ""

    @classmethod
    def get_component(cls, **props):
        # 在传递 **props 之前弹出带有默认值的组件特定属性
        value = props.pop("value", cls.text)
        on_change = props.pop("on_change", cls.set_text)
        cursor = props.pop("cursor", "pointer")

        # 设置状态变量的初始值。
        initial_value = props.pop("initial_value", None)
        if initial_value is not None:
            # 更新 pydantic 模型以使用初始值作为默认值。
            cls.__fields__["text"].default = initial_value

        # 用于编辑、保存和还原文本的表单元素。
        edit_controls = rx.hstack(
            rx.input(
                value=value,
                on_change=on_change,
                **props,
            ),
            rx.icon_button(
                rx.icon("x"),
                on_click=[
                    on_change(cls.original_text),
                    cls.stop_editing,
                ],
                type="button",
                color_scheme="red",
            ),
            rx.icon_button(rx.icon("check")),
            align="center",
            width="100%",
        )

        # 根据 editing 变量返回文本或表单。
        return rx.cond(
            cls.editing,
            rx.form(
                edit_controls,
                on_submit=lambda _: cls.stop_editing(),
            ),
            rx.text(
                value,
                on_click=cls.start_editing(value),
                cursor=cursor,
                **props,
            ),
        )


editable_text = EditableText.create


def editable_text_example():
    return rx.vstack(
        editable_text(),
        editable_text(initial_value="Edit me!", color="blue"),
        editable_text(
            initial_value="Reflex is fun", font_family="monospace", width="100%"
        ),
    )
```

```python eval
rx.divider()
```

由于此 `EditableText` 组件设计为可重用的，它可以处理
`value` 和 `on_change` 链接到普通全局状态的情况。

```python exec
# Hack because flexdown re-inits modules
EditableText._per_component_state_instance_count = 4
```

```python demo exec
class EditableTextDemoState(rx.State):
    value: str = "Global state text"

    @rx.event
    def set_value(self, value: str):
        self.value = value


def editable_text_with_global_state():
    return rx.vstack(
        editable_text(
            value=EditableTextDemoState.value, on_change=EditableTextDemoState.set_value
        ),
        rx.text(EditableTextDemoState.value.upper()),
    )
```

## 访问状态

`ComponentState` 的底层状态类可通过 `.State` 属性访问。要使用它，
将组件的实例分配给局部变量，然后在页面中包含该实例。

```python exec
# Hack because flexdown re-inits modules
ReusableCounter._per_component_state_instance_count = 4
```

```python demo exec
def counter_sum():
    counter1 = reusable_counter()
    counter2 = reusable_counter()
    return rx.vstack(
        rx.text(f"Total: {counter1.State.count + counter2.State.count}"),
        counter1,
        counter2,
    )
```

```python eval
rx.divider()
```

其他组件也可以通过 `.State` 属性引用其事件处理程序或变量来影响 `ComponentState`。

```python exec
# Hack because flexdown re-inits modules
ReusableCounter._per_component_state_instance_count = 6
```

```python demo exec
def extended_counter():
    counter1 = reusable_counter()
    return rx.vstack(
        counter1,
        rx.hstack(
            rx.icon_button(rx.icon("step_back"), on_click=counter1.State.set_count(0)),
            rx.icon_button(rx.icon("plus"), on_click=counter1.State.increment),
            rx.button(
                "Double", on_click=counter1.State.set_count(counter1.State.count * 2)
            ),
            rx.button(
                "Triple", on_click=counter1.State.set_count(counter1.State.count * 3)
            ),
        ),
    )
```
