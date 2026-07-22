---
components:
  - rx.form

FormRoot: |
  lambda **props: rx.form.root(
      rx.form.field(
          rx.flex(
              rx.form.label("Email"),
              rx.form.control(
                  rx.input(
                      placeholder="Email Address",
                      # type attribute is required for "typeMismatch" validation
                      type="email",
                  ),
                  as_child=True,
              ),
              rx.form.message("Please enter a valid email"),
              rx.form.submit(
                  rx.button("Submit"),
                  as_child=True,
              ),
              direction="column",
              spacing="2",
              align="stretch",
          ),
          name="email",
      ),
      **props,
  )

FormField: |
  lambda **props: rx.form.root(
      rx.form.field(
          rx.flex(
              rx.form.label("Email"),
              rx.form.control(
                  rx.input(
                      placeholder="Email Address",
                      # type attribute is required for "typeMismatch" validation
                      type="email",
                  ),
                  as_child=True,
              ),
              rx.form.message("Please enter a valid email", match="typeMismatch"),
              rx.form.submit(
                  rx.button("Submit"),
                  as_child=True,
              ),
              direction="column",
              spacing="2",
              align="stretch",
          ),
          **props,
      ),
      reset_on_submit=True,
  )

FormLabel: |
  lambda **props: rx.form.root(
      rx.form.field(
          rx.flex(
              rx.form.label("Email", **props,),
              rx.form.control(
                  rx.input(
                      placeholder="Email Address",
                      # type attribute is required for "typeMismatch" validation
                      type="email",
                  ),
                  as_child=True,
              ),
              rx.form.message("Please enter a valid email", match="typeMismatch"),
              rx.form.submit(
                  rx.button("Submit"),
                  as_child=True,
              ),
              direction="column",
              spacing="2",
              align="stretch",
          ),
      ),
      reset_on_submit=True,
  )

FormMessage: |
  lambda **props: rx.form.root(
              rx.form.field(
                  rx.flex(
                      rx.form.label("Email"),
                      rx.form.control(
                          rx.input(
                              placeholder="Email Address",
                              # type attribute is required for "typeMismatch" validation
                              type="email",
                          ),
                          as_child=True,
                      ),
                      rx.form.message("Please enter a valid email", **props,),
                      rx.form.submit(
                          rx.button("Submit"),
                          as_child=True,
                      ),
                      direction="column",
                      spacing="2",
                      align="stretch",
                  ),
                  name="email",
              ),
              on_submit=lambda form_data: rx.window_alert(form_data.to_string()),
              reset_on_submit=True,
          )
---

```python exec
import reflex as rx
```

# 表单（Form）

表单（Form）用于收集用户输入。`rx.form` 组件用于将输入控件组合在一起并一起提交。

表单组件的子元素可以是表单控件，如 `rx.input`、`rx.checkbox`、`rx.slider`、`rx.text_area`、`rx.radio_group`、`rx.select` 或 `rx.switch`。控件应该有一个 `name` 属性，用于在表单数据中标识该控件。`on_submit` 事件触发器将表单数据作为字典提交给 `handle_submit` 事件处理函数。

当用户点击提交按钮或在表单控件上按下回车键时，表单会被提交。

```python demo exec
class FormState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        self.form_data = form_data


def form_example():
    return rx.vstack(
        rx.form(
            rx.vstack(
                rx.input(placeholder="First Name", name="first_name"),
                rx.input(placeholder="Last Name", name="last_name"),
                rx.hstack(
                    rx.checkbox("Checked", name="check"),
                    rx.switch("Switched", name="switch"),
                ),
                rx.button("Submit", type="submit"),
            ),
            on_submit=FormState.handle_submit,
            reset_on_submit=True,
        ),
        rx.divider(),
        rx.heading("Results", as_="h2"),
        rx.text(FormState.form_data.to_string()),
    )
```

```md alert warning
# 使用表单时，必须包含一个带有 `type='submit'` 的按钮或输入框。
```

```md alert info
# 使用 `name` 还是 `id`。

在 `rx.switch`、`rx.radio_group` 和 `rx.checkbox` 等表单控件中使用 `name` 属性时，这些控件只有在设置了值的情况下才会包含在表单数据中（例如，复选框被选中、开关被切换、或选择了某个单选项）。

如果你需要这些控件即使在未设置值的情况下也包含在表单数据中，可以使用 `id` 属性代替 name。id 属性确保控件始终包含在提交的表单数据中，无论其值是否已设置。
```

```md video https://youtube.com/embed/ITOZKzjtjUA?start=5287&end=6040
# 视频：表单
```

## 使用 TypedDict 验证表单数据

`on_submit` 处理函数通常以普通 `dict` 的形式接收表单数据，这意味着访问字段时没有类型信息（`form_data["email"]` 返回 `Any`），而且 `name` 中的拼写错误直到运行时才会被发现。

作为替代，你可以使用 [`TypedDict`](https://docs.python.org/3/library/typing.html#typing.TypedDict) 来标注处理函数的参数。这样你可以在处理函数内部获得带类型提示和自动补全的字段访问，而且 Reflex 会在**编译时**验证表单：`TypedDict` 中的每个必需键都必须有对应的表单控件。如果某个必需字段没有对应 `name`（或 `id`）的控件，Reflex 会在应用启动前抛出 `EventHandlerValueError`，精确指出缺少哪些字段。

```python demo exec
from typing import TypedDict

from typing_extensions import NotRequired


class ContactForm(TypedDict):
    first_name: str
    last_name: str
    email: str
    message: NotRequired[str]  # 可选字段


class TypedFormState(rx.State):
    form_data: ContactForm | None = None

    @rx.event
    def handle_submit(self, form_data: ContactForm):
        """处理表单提交。"""
        # form_data 是有类型的：编辑器会自动补全下面的键。
        self.form_data = form_data


def typed_form_example():
    return rx.vstack(
        rx.form(
            rx.vstack(
                rx.input(placeholder="First Name", name="first_name"),
                rx.input(placeholder="Last Name", name="last_name"),
                rx.input(placeholder="Email", name="email", type="email"),
                rx.text_area(placeholder="Message", name="message"),
                rx.button("Submit", type="submit"),
            ),
            on_submit=TypedFormState.handle_submit,
            reset_on_submit=True,
        ),
        rx.divider(),
        rx.heading("Results", as_="h2"),
        rx.text(TypedFormState.form_data.to_string()),
    )
```

### 必需字段和可选字段

默认情况下，`TypedDict` 中声明的每个键都是**必需的**，必须有对应的表单控件。使用 `NotRequired`（或通过继承 `total=False` 的基类）将字段标记为可选，这样 Reflex 就不会要求对应的控件：

```python
from typing import TypedDict

from typing_extensions import NotRequired


class ContactForm(TypedDict):
    name: str  # 必需：必须存在名为 "name" 的控件
    email: str  # 必需：必须存在名为 "email" 的控件
    message: NotRequired[str]  # 可选：不需要对应的控件
```

如果缺少必需字段，创建表单时会立即失败，并显示一条消息列出预期的、缺少的和匹配的字段：

```python
class SignupForm(TypedDict):
    username: str
    email: str


class SignupState(rx.State):
    @rx.event
    def handle_submit(self, form_data: SignupForm): ...


# 抛出 EventHandlerValueError：表单没有名为 "email" 的控件。
rx.form(
    rx.input(name="username"),
    rx.button("Submit", type="submit"),
    on_submit=SignupState.handle_submit,
)
```

```md alert info
# 什么时候会跳过验证？

该检查仅在表单字段是静态已知时运行。当控件的 `name`/`id` 值是动态的（例如，使用 `rx.foreach` 构建的），或者当表单有 `id` 时（因为控件可以通过 HTML `form` 属性从其他地方关联），检查会自动跳过。在这些情况下，`TypedDict` 仍然在处理函数内部提供类型化的访问。在运行时，`form_data` 始终是一个普通字典。
```

## 动态表单

可以通过使用 `rx.foreach` 遍历状态变量来动态创建表单。

此示例允许用户在提交前向表单添加新字段，所有字段都会包含在传递给 `handle_submit` 函数的表单数据中。

```python demo exec
class DynamicFormState(rx.State):
    form_data: dict = {}
    form_fields: list[str] = ["first_name", "last_name", "email"]

    @rx.var(cache=True)
    def form_field_placeholders(self) -> list[str]:
        return [
            " ".join(w.capitalize() for w in field.split("_"))
            for field in self.form_fields
        ]

    @rx.event
    def add_field(self, form_data: dict):
        new_field = form_data.get("new_field")
        if not new_field:
            return
        field_name = new_field.strip().lower().replace(" ", "_")
        self.form_fields.append(field_name)

    @rx.event
    def handle_submit(self, form_data: dict):
        self.form_data = form_data


def dynamic_form():
    return rx.vstack(
        rx.form(
            rx.vstack(
                rx.foreach(
                    DynamicFormState.form_fields,
                    lambda field, idx: rx.input(
                        placeholder=DynamicFormState.form_field_placeholders[idx],
                        name=field,
                    ),
                ),
                rx.button("Submit", type="submit"),
            ),
            on_submit=DynamicFormState.handle_submit,
            reset_on_submit=True,
        ),
        rx.form(
            rx.hstack(
                rx.input(placeholder="New Field", name="new_field"),
                rx.button("+", type="submit"),
            ),
            on_submit=DynamicFormState.add_field,
            reset_on_submit=True,
        ),
        rx.divider(),
        rx.heading("Results", as_="h2"),
        rx.text(DynamicFormState.form_data.to_string()),
    )
```
