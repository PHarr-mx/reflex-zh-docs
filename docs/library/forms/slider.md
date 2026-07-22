---
components:
  - rx.slider

Slider: |
  lambda **props: rx.center(rx.slider(default_value=40, height="100%", **props), height="4em", width="100%")
---

```python exec
import reflex as rx
```

# 滑块（Slider）

提供从一系列值中进行选择的功能。

## 基本示例

滑块可以通过单个值或一个值范围来控制。滑块可以与状态（State）绑定来控制其值。传入包含两个值的列表会创建一个范围滑块。

```python demo exec
class SliderState(rx.State):
    value: int = 50

    @rx.event
    def set_end(self, value: list[int | float]):
        self.value = value[0]


def slider_intro():
    return rx.vstack(
        rx.heading(SliderState.value, as_="h2"),
        rx.slider(on_value_commit=SliderState.set_end),
        width="100%",
    )
```

## 范围滑块

通过向 `default_value` 属性传入包含两个值的列表来创建范围滑块。列表应包含两个在滑块范围内的值。

```python demo exec
class RangeSliderState(rx.State):
    value_start: int = 25
    value_end: int = 75

    @rx.event
    def set_end(self, value: list[int | float]):
        self.value_start = value[0]
        self.value_end = value[1]


def range_slider_intro():
    return rx.vstack(
        rx.hstack(
            rx.heading(RangeSliderState.value_start, as_="h2"),
            rx.heading(RangeSliderState.value_end, as_="h2"),
        ),
        rx.slider(
            default_value=[25, 75],
            min_=0,
            max=100,
            size="1",
            on_value_commit=RangeSliderState.set_end,
        ),
        width="100%",
    )
```

## 实时更新滑块

你可以使用 `on_change` 属性在交互时实时更新滑块的值。`on_change` 属性接受一个函数，该函数会在滑块值变化时被调用。

这里我们使用 `throttle` 方法来限制函数的调用频率，这对于防止过多的更新很有用。在此示例中，滑块的值每 100 毫秒更新一次。

```python demo exec
class LiveSliderState(rx.State):
    value: int = 50

    @rx.event
    def set_end(self, value: list[int | float]):
        self.value = value[0]


def live_slider_intro():
    return rx.vstack(
        rx.heading(LiveSliderState.value, as_="h2"),
        rx.slider(
            default_value=50,
            min_=0,
            max=100,
            on_change=LiveSliderState.set_end.throttle(100),
        ),
        width="100%",
    )
```

## 在表单中使用滑块

这里展示了如何在表单中使用滑块。我们使用 `name` 属性来标识表单数据中的滑块。表单数据随后会传递给 `handle_submit` 方法进行处理。

```python demo exec
class FormSliderState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        self.form_data = form_data


def slider_form_example():
    return rx.card(
        rx.vstack(
            rx.heading("Example Form", as_="h2"),
            rx.form.root(
                rx.hstack(
                    rx.slider(default_value=40, name="slider"),
                    rx.button("Submit", type="submit"),
                    width="100%",
                ),
                on_submit=FormSliderState.handle_submit,
                reset_on_submit=True,
            ),
            rx.divider(),
            rx.hstack(
                rx.heading("Results:", as_="h2"),
                rx.badge(FormSliderState.form_data.to_string()),
            ),
            align_items="left",
            width="100%",
        ),
        width="50%",
    )
```
