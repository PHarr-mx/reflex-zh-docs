---
components:
  - rx.form.root
  - rx.form.field
  - rx.form.control
  - rx.form.label
  - rx.form.message
  - rx.form.submit

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

# 表单（Form）

```python exec
import reflex as rx
import reflex.components.radix.primitives as rdxp
```

```md warning info
## 底层表单是实验性功能

目前生产环境请使用高层表单。
```

表单（Form）用于从用户那里收集信息。表单将输入控件组合在一起并一起提交。

## 基本示例

下面是一个收集电子邮件地址的表单示例，带有内置的邮箱验证。如果输入的邮箱无效，则无法提交表单。请注意，`form.submit` 按钮不会自动禁用。它仍然可以点击，但不会提交表单数据。成功提交后，会弹出一个警告窗口并清空表单。示例中使用了一些 `flex` 容器来控制表单组件的布局。

```python demo
rx.form.root(
    rx.form.field(
        rx.flex(
            rx.form.label("Email"),
            rx.form.control(
                rx.input(
                    placeholder="Email Address",
                    # type 属性是 "typeMismatch" 验证所必需的
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
        name="email",
    ),
    on_submit=lambda form_data: rx.window_alert(form_data.to_string()),
    reset_on_submit=True,
)
```

在此示例中，`rx.input` 具有 `type="email"` 属性，`form.message` 具有 `match="typeMismatch"` 属性。这些是表单按类型验证输入所必需的。当使用其他组件来构建 Form 组件时，需要设置 `as_child="True"` 属性。此示例使用 `rx.input` 来构建 Form Control，使用 `button` 来构建 Form Submit。

## 表单结构

```python eval
rx.code_block(
    """form.root(
    form.field(
        form.label(...),
        form.control(...),
        form.message(...),
    ),
    form.submit(...),
)""",
    language="python",
)
```

Form Root（`form.root`）包含表单的所有部分。Form Field（`form.field`）、Form Submit（`form.submit`）等都应该在 Form Root 内部。一个 Form Field 可以包含 Form Label（`form.label`）、Form Control（`form.control`）和 Form Message（`form.message`）。Form Label 是一个标签元素。Form Control 是用户输入或进行选择的地方。默认情况下，Form Control 是一个输入框。支持使用其他表单组件来构建 Form Control。为此，需要在 Form Control 上设置 `as_child=True` 属性。

```md alert info
当前版本的 Radix Forms 不支持将 **Form Control** 与其他 Radix 表单原语（如 **Checkbox**、**Select** 等）组合使用。
```

Form Message 是一个验证消息，它会自动连接功能和可访问性。当 Form Control 判定输入无效时，会显示 Form Message。`match` 属性用于启用[客户端验证](#client-side-validation)。要执行[服务器端验证](#server-side-validation)，需要**同时**设置 Form Control 的 `force_match` 属性和 Form Field 的 `server_invalid` 属性。

Form Submit 默认是一个提交表单的按钮。要使用其他按钮组件作为 Form Submit，将该按钮作为子元素包含在 `form.submit` 中并设置 `as_child=True` 属性。

Form Root 的 `on_submit` 属性接受一个事件处理函数。它会在提交时被调用，并接收提交的表单数据字典。要在提交后清空表单，设置 `reset_on_submit=True` 属性。

## 数据提交

如前所述，表单中的各种数据会作为一个字典一起提交。表单控件或输入组件必须具有 `name` 属性。这个 `name` 是从表单数据字典中获取值的键。如果不需要验证，表单类型组件（如 Checkbox、Radio Groups、TextArea）可以直接放在 Form Root 下面，而不需要放在 Form Control 内部。

```python demo exec
import reflex as rx
import reflex.components.radix.primitives as rdxp


class RadixFormSubmissionState(rx.State):
    form_data: dict

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        self.form_data = form_data

    @rx.var
    def form_data_keys(self) -> list:
        return list(self.form_data.keys())

    @rx.var
    def form_data_values(self) -> list:
        return list(self.form_data.values())


def radix_form_submission_example():
    return rx.flex(
        rx.form.root(
            rx.flex(
                rx.flex(
                    rx.checkbox(
                        default_checked=True,
                        name="box1",
                    ),
                    rx.text("box1 checkbox"),
                    direction="row",
                    spacing="2",
                    align="center",
                ),
                rx.radio.root(
                    rx.flex(
                        rx.radio.item(value="1"),
                        "1",
                        direction="row",
                        align="center",
                        spacing="2",
                    ),
                    rx.flex(
                        rx.radio.item(value="2"),
                        "2",
                        direction="row",
                        align="center",
                        spacing="2",
                    ),
                    rx.flex(
                        rx.radio.item(value="3"),
                        "3",
                        direction="row",
                        align="center",
                        spacing="2",
                    ),
                    default_value="1",
                    name="box2",
                ),
                rx.input(
                    placeholder="box3 textfield input",
                    name="box3",
                ),
                rx.select.root(
                    rx.select.trigger(
                        placeholder="box4 select",
                    ),
                    rx.select.content(
                        rx.select.group(
                            rx.select.item("Orange", value="orange"),
                            rx.select.item("Apple", value="apple"),
                        ),
                    ),
                    name="box4",
                ),
                rx.flex(
                    rx.switch(
                        default_checked=True,
                        name="box5",
                    ),
                    "box5 switch",
                    spacing="2",
                    align="center",
                    direction="row",
                ),
                rx.flex(
                    rx.slider(
                        default_value=[40],
                        width="100%",
                        name="box6",
                    ),
                    "box6 slider",
                    direction="row",
                    spacing="2",
                    align="center",
                ),
                rx.text_area(
                    placeholder="Enter for box7 textarea",
                    name="box7",
                ),
                rx.form.submit(
                    rx.button("Submit"),
                    as_child=True,
                ),
                direction="column",
                spacing="4",
            ),
            on_submit=RadixFormSubmissionState.handle_submit,
        ),
        rx.divider(size="4"),
        rx.text(
            "Results",
            weight="bold",
        ),
        rx.foreach(
            RadixFormSubmissionState.form_data_keys,
            lambda key, idx: rx.text(
                key, " : ", RadixFormSubmissionState.form_data_values[idx]
            ),
        ),
        direction="column",
        spacing="4",
    )
```

## 验证

服务器端验证通过 State 上的**计算变量（Computed Vars）**来完成。该 **Var** 应该返回一个布尔值，指示输入何时无效。将该 **Var** 同时设置在 `form.field` 的 `server_invalid` 属性和 `form.message` 的 `force_match` 属性上。在[最终示例](#final-example)中有一个如何这样做的例子。

## 最终示例

最终示例展示了一个在注册时收集用户名和邮箱并使用服务器端验证进行验证的表单。当服务器端验证失败时，会以红色显示消息来表明表单中哪些内容不被接受，并且提交按钮会被禁用。提交后，收集的表单数据会显示在表单下方的文本中，并且表单会被清空。

```python demo exec
import re
import reflex as rx
import reflex.components.radix.primitives as rdxp


class RadixFormState(rx.State):
    # 这些实时跟踪用户输入以进行验证
    user_entered_username: str
    user_entered_email: str

    # 这些是提交的数据
    username: str
    email: str

    mock_username_db: list[str] = ["reflex", "admin"]

    # 添加显式的 setter
    def set_user_entered_username(self, value: str):
        self.user_entered_username = value

    def set_user_entered_email(self, value: str):
        self.user_entered_email = value

    def set_username(self, value: str):
        self.username = value

    def set_email(self, value: str):
        self.email = value

    @rx.var
    def invalid_email(self) -> bool:
        return not re.match(r"[^@]+@[^@]+\.[^@]+", self.user_entered_email)

    @rx.var
    def username_empty(self) -> bool:
        return not self.user_entered_username.strip()

    @rx.var
    def username_is_taken(self) -> bool:
        return self.user_entered_username in self.mock_username_db

    @rx.var
    def input_invalid(self) -> bool:
        return self.invalid_email or self.username_is_taken or self.username_empty

    @rx.event
    def handle_submit(self, form_data: dict):
        """处理表单提交。"""
        self.username = form_data.get("username")
        self.email = form_data.get("email")


def radix_form_example():
    return rx.flex(
        rx.form.root(
            rx.flex(
                rx.form.field(
                    rx.flex(
                        rx.form.label("Username"),
                        rx.form.control(
                            rx.input(
                                placeholder="Username",
                                # 解决方法：设置 on_change 时似乎需要 `name`
                                on_change=RadixFormState.set_user_entered_username,
                                name="username",
                            ),
                            as_child=True,
                        ),
                        # 服务器端验证消息可以在 rx.cond 中显示
                        rx.cond(
                            RadixFormState.username_empty,
                            rx.form.message(
                                "Username cannot be empty",
                                color="var(--red-11)",
                            ),
                        ),
                        # 服务器端验证消息可以通过 `force_match` 属性显示
                        rx.form.message(
                            "Username already taken",
                            # 这是一个解决方法：
                            # `force_match` 在没有 `match` 的情况下不起作用
                            # 此情况不需要客户端验证
                            # 并且故意没有在输入框上设置 `required`
                            # 所以 "valueMissing" 始终为 false
                            match="valueMissing",
                            force_match=RadixFormState.username_is_taken,
                            color="var(--red-11)",
                        ),
                        direction="column",
                        spacing="2",
                        align="stretch",
                    ),
                    name="username",
                    server_invalid=RadixFormState.username_is_taken,
                ),
                rx.form.field(
                    rx.flex(
                        rx.form.label("Email"),
                        rx.form.control(
                            rx.input(
                                placeholder="Email Address",
                                on_change=RadixFormState.set_user_entered_email,
                                name="email",
                            ),
                            as_child=True,
                        ),
                        rx.form.message(
                            "A valid Email is required",
                            match="valueMissing",
                            force_match=RadixFormState.invalid_email,
                            color="var(--red-11)",
                        ),
                        direction="column",
                        spacing="2",
                        align="stretch",
                    ),
                    name="email",
                    server_invalid=RadixFormState.invalid_email,
                ),
                rx.form.submit(
                    rx.button(
                        "Submit",
                        disabled=RadixFormState.input_invalid,
                    ),
                    as_child=True,
                ),
                direction="column",
                spacing="4",
                width="25em",
            ),
            on_submit=RadixFormState.handle_submit,
            reset_on_submit=True,
        ),
        rx.divider(size="4"),
        rx.text(
            "Username submitted: ",
            rx.text(
                RadixFormState.username,
                weight="bold",
                color="var(--accent-11)",
            ),
        ),
        rx.text(
            "Email submitted: ",
            rx.text(
                RadixFormState.email,
                weight="bold",
                color="var(--accent-11)",
            ),
        ),
        direction="column",
        spacing="4",
    )
```
