---
components:
  - rx.text_area

TextArea: |
  lambda **props: rx.text_area(**props)
---

```python exec
import reflex as rx
```

# 文本域（Text Area）

文本域（Text Area）是一个多行文本输入框。

## 基本示例

文本域组件可以通过单个值来控制。`on_blur` 属性可用于在文本域失去焦点时更新值。

```python demo exec
class TextAreaBlur(rx.State):
    text: str = "Hello World!"

    @rx.event
    def set_text(self, text: str):
        self.text = text


def blur_example():
    return rx.vstack(
        rx.heading(TextAreaBlur.text, as_="h2"),
        rx.text_area(
            placeholder="Type here...",
            on_blur=TextAreaBlur.set_text,
        ),
    )
```

## 在表单中使用文本域

这里展示了如何在表单中使用文本域。我们使用 `name` 属性来标识表单数据中的文本域。表单数据随后会传递给 `submit_feedback` 方法进行处理。

```python demo exec
class TextAreaFeedbackState(rx.State):
    feedback: str = ""
    submitted: bool = False

    @rx.event
    def set_feedback(self, value: str):
        self.feedback = value

    @rx.event
    def submit_feedback(self, form_data: dict):
        self.submitted = True

    @rx.event
    def reset_form(self):
        self.feedback = ""
        self.submitted = False


def feedback_form():
    return rx.cond(
        TextAreaFeedbackState.submitted,
        rx.card(
            rx.vstack(
                rx.text("Thank you for your feedback!"),
                rx.button(
                    "Submit another response", on_click=TextAreaFeedbackState.reset_form
                ),
            ),
        ),
        rx.card(
            rx.form(
                rx.flex(
                    rx.text("Are you enjoying Reflex?"),
                    rx.text_area(
                        placeholder="Write your feedback…",
                        value=TextAreaFeedbackState.feedback,
                        on_change=TextAreaFeedbackState.set_feedback,
                        resize="vertical",
                    ),
                    rx.button("Send", type="submit"),
                    direction="column",
                    spacing="3",
                ),
                on_submit=TextAreaFeedbackState.submit_feedback,
            ),
        ),
    )
```
