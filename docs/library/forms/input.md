---
components:
  - rx.input

TextFieldRoot: |
  lambda **props: rx.input(placeholder="Search the docs", **props)

TextFieldSlot: |
  lambda **props: rx.input(
      rx.input.slot(
          rx.icon(tag="search", height="16", width="16"),
          **props,
      ),
      placeholder="Search the docs",
  )
---

```python exec
import reflex as rx
```

# 输入框（Input）

`input` 组件是用户可以输入内容的输入框。

```md video https://youtube.com/embed/ITOZKzjtjUA?start=1517&end=1869
# 视频：输入框
```

## 基本示例

当焦点离开 `input` 时，会调用 `on_blur` 事件处理函数。例如，当用户点击已聚焦的文本输入框外部时，就会触发它。

```python demo exec
class TextfieldBlur(rx.State):
    text: str = "Hello World!"

    @rx.event
    def set_text(self, value: str):
        self.text = value


def blur_example():
    return rx.vstack(
        rx.heading(TextfieldBlur.text, as_="h2"),
        rx.input(
            placeholder="Search here...",
            on_blur=TextfieldBlur.set_text,
        ),
    )
```

当 `input` 的 `value` 发生变化时，会调用 `on_change` 事件处理函数。

```python demo exec
class TextfieldControlled(rx.State):
    text: str = "Hello World!"

    @rx.event
    def set_text(self, value: str):
        self.text = value


def controlled_example():
    return rx.vstack(
        rx.heading(TextfieldControlled.text, as_="h2"),
        rx.input(
            placeholder="Search here...",
            value=TextfieldControlled.text,
            on_change=TextfieldControlled.set_text,
        ),
    )
```

在底层实现中，input 组件被实现为一个防抖输入，以避免在用户仍在输入时逐字符向后端发送状态更新。这使得状态变量可以直接从后端控制 `value` 属性，而用户不会感受到输入延迟。

## 输入类型

`type` 属性控制输入框的渲染方式（例如纯文本、密码、文件选择器）。

它接受与原生 HTML `<input type>` 属性相同的值，例如：

- `"text"`（默认）
- `"password"`
- `"email"`
- `"number"`
- `"file"`
- `"checkbox"`
- `"radio"`
- `"date"`
- `"time"`
- `"url"`
- `"color"`

以及其他几种类型。完整列表请参阅 [MDN 参考文档](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#input_types)。

```python demo
rx.vstack(
    rx.input(placeholder="Username", type="text"),
    rx.input(placeholder="Password", type="password"),
    rx.input(type="date"),
)
```

## 使用输入框提交表单

提交表单时需要 `name` 属性，作为名称/值对的一部分。

当 `required` 属性为 `True` 时，表示用户必须在提交所属表单之前输入文本。

这里将 `type` 设置为 `password`。该元素呈现为单行纯文本编辑控件，其中文本被隐藏，无法被读取。`type` 属性可以接受 `email`、`file`、`password`、`text` 以及其他几种值。了解更多请参见[此处](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input)。

```python demo exec
class FormInputState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        self.form_data = form_data


def form_input1():
    return rx.card(
        rx.vstack(
            rx.heading("Example Form", as_="h2"),
            rx.form.root(
                rx.hstack(
                    rx.input(
                        name="input",
                        placeholder="Enter text...",
                        type="text",
                        required=True,
                    ),
                    rx.button("Submit", type="submit"),
                    width="100%",
                ),
                on_submit=FormInputState.handle_submit,
                reset_on_submit=True,
            ),
            rx.divider(),
            rx.hstack(
                rx.heading("Results:", as_="h2"),
                rx.badge(FormInputState.form_data.to_string()),
            ),
            align_items="left",
            width="100%",
        ),
        width="50%",
    )
```

要了解更多关于如何使用表单的信息，请参阅[表单](/docs/library/forms/form)文档。

## 不使用 State 变量设置值

设置指定引用元素的值，无需将其链接到 State 变量。这是修改 `input` 值的另一种方式。

```python demo
rx.hstack(
    rx.input(id="input1"),
    rx.button("Erase", on_click=rx.set_value("input1", "")),
)
```
