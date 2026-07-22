```python exec
import reflex as rx
```

# 智能复选框组

一个智能复选框组，你可以跟踪所有选中的框，以及限制可以选中多少个框。

## 配方

```python eval
rx.center(
    rx.image(
        src="https://web.reflex-assets.dev/templates/smart_checkboxes.webp",
        alt="Smart checkboxes recipe screenshot",
    )
)
```

此配方使用 `dict[str, bool]` 进行复选框状态跟踪。
此外，限制阻止用户选中超过允许数量的框，使用计算变量。

```python
class CBoxeState(rx.State):
    choices: dict[str, bool] = {k: False for k in ["Choice A", "Choice B", "Choice C"]}
    _check_limit = 2

    def check_choice(self, value, index):
        self.choices[index] = value

    @rx.var
    def choice_limit(self):
        return sum(self.choices.values()) >= self._check_limit

    @rx.var
    def checked_choices(self):
        choices = [l for l, v in self.choices.items() if v]
        return " / ".join(choices) if choices else "None"


import reflex as rx


def render_checkboxes(values, limit, handler):
    return rx.vstack(
        rx.foreach(
            values,
            lambda choice: rx.checkbox(
                choice[0],
                checked=choice[1],
                disabled=~choice[1] & limit,
                on_change=lambda val: handler(val, choice[0]),
            ),
        )
    )


def index() -> rx.Component:

    return rx.center(
        rx.vstack(
            rx.text("Make your choices (2 max):"),
            render_checkboxes(
                CBoxeState.choices,
                CBoxeState.choice_limit,
                CBoxeState.check_choice,
            ),
            rx.text("Your choices: ", CBoxeState.checked_choices),
        ),
        height="100vh",
    )
```
