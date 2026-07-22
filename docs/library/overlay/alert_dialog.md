---
components:
  - rx.alert_dialog.root
  - rx.alert_dialog.content
  - rx.alert_dialog.trigger
  - rx.alert_dialog.title
  - rx.alert_dialog.description
  - rx.alert_dialog.action
  - rx.alert_dialog.cancel

only_low_level:
  - True

AlertDialogRoot: |
  lambda **props: rx.alert_dialog.root(
      rx.alert_dialog.trigger(
          rx.button("Revoke access"),
      ),
      rx.alert_dialog.content(
          rx.alert_dialog.title("Revoke access"),
          rx.alert_dialog.description(
              "Are you sure? This application will no longer be accessible and any existing sessions will be expired.",
          ),
          rx.flex(
              rx.alert_dialog.cancel(
                  rx.button("Cancel"),
              ),
              rx.alert_dialog.action(
                  rx.button("Revoke access"),
              ),
              spacing="3",
          ),
      ),
      **props
  )

AlertDialogContent: |
  lambda **props: rx.alert_dialog.root(
      rx.alert_dialog.trigger(
          rx.button("Revoke access"),
      ),
      rx.alert_dialog.content(
          rx.alert_dialog.title("Revoke access"),
          rx.alert_dialog.description(
              "Are you sure? This application will no longer be accessible and any existing sessions will be expired.",
          ),
          rx.flex(
              rx.alert_dialog.cancel(
                  rx.button("Cancel"),
              ),
              rx.alert_dialog.action(
                  rx.button("Revoke access"),
              ),
              spacing="3",
          ),
          **props
      ),
  )
---

```python exec
import reflex as rx
```

# Alert Dialog（警告对话框）

警告对话框（Alert Dialog）是一种模态确认对话框，它会中断用户操作并期望用户做出响应。

`alert_dialog.root` 包含对话框的所有部分。

`alert_dialog.trigger` 包裹用于打开对话框的控件。

`alert_dialog.content` 包含对话框的内容。

`alert_dialog.title` 是对话框打开时播报的标题。

`alert_dialog.description` 是对话框打开时播报的可选描述。

`alert_dialog.action` 包裹用于关闭对话框的控件。它应在视觉上与 `alert_dialog.cancel` 控件区分开来。

`alert_dialog.cancel` 包裹用于关闭对话框的控件。它应在视觉上与 `alert_dialog.action` 控件区分开来。

## 基本示例

```python demo
rx.alert_dialog.root(
    rx.alert_dialog.trigger(
        rx.button("Revoke access"),
    ),
    rx.alert_dialog.content(
        rx.alert_dialog.title("Revoke access"),
        rx.alert_dialog.description(
            "Are you sure? This application will no longer be accessible and any existing sessions will be expired.",
        ),
        rx.flex(
            rx.alert_dialog.cancel(
                rx.button("Cancel"),
            ),
            rx.alert_dialog.action(
                rx.button("Revoke access"),
            ),
            spacing="3",
        ),
    ),
)
```

此示例使用了不同的配色方案，并且 `cancel` 和 `action` 按钮右对齐。

```python demo
rx.alert_dialog.root(
    rx.alert_dialog.trigger(
        rx.button("Revoke access", color_scheme="red"),
    ),
    rx.alert_dialog.content(
        rx.alert_dialog.title("Revoke access"),
        rx.alert_dialog.description(
            "Are you sure? This application will no longer be accessible and any existing sessions will be expired.",
            size="2",
        ),
        rx.flex(
            rx.alert_dialog.cancel(
                rx.button("Cancel", variant="soft", color_scheme="gray"),
            ),
            rx.alert_dialog.action(
                rx.button("Revoke access", color_scheme="red", variant="solid"),
            ),
            spacing="3",
            margin_top="16px",
            justify="end",
        ),
        style={"max_width": 450},
    ),
)
```

使用 `inset` 组件可以使内容与对话框的边缘齐平对齐。

```python demo
rx.alert_dialog.root(
    rx.alert_dialog.trigger(
        rx.button("Delete Users", color_scheme="red"),
    ),
    rx.alert_dialog.content(
        rx.alert_dialog.title("Delete Users"),
        rx.alert_dialog.description(
            "Are you sure you want to delete these users? This action is permanent and cannot be undone.",
            size="2",
        ),
        rx.inset(
            rx.table.root(
                rx.table.header(
                    rx.table.row(
                        rx.table.column_header_cell("Full Name"),
                        rx.table.column_header_cell("Email"),
                        rx.table.column_header_cell("Group"),
                    ),
                ),
                rx.table.body(
                    rx.table.row(
                        rx.table.row_header_cell("Danilo Rosa"),
                        rx.table.cell("danilo@example.com"),
                        rx.table.cell("Developer"),
                    ),
                    rx.table.row(
                        rx.table.row_header_cell("Zahra Ambessa"),
                        rx.table.cell("zahra@example.com"),
                        rx.table.cell("Admin"),
                    ),
                ),
            ),
            side="x",
            margin_top="24px",
            margin_bottom="24px",
        ),
        rx.flex(
            rx.alert_dialog.cancel(
                rx.button("Cancel", variant="soft", color_scheme="gray"),
            ),
            rx.alert_dialog.action(
                rx.button("Delete users", color_scheme="red"),
            ),
            spacing="3",
            justify="end",
        ),
        style={"max_width": 500},
    ),
)
```

## 警告对话框打开或关闭时的事件

当对话框的 `open` 状态发生变化时，会调用 `on_open_change` 事件。它与 `open` prop 配合使用。

```python demo exec
class AlertDialogState(rx.State):
    num_opens: int = 0
    opened: bool = False

    @rx.event
    def count_opens(self, value: bool):
        self.opened = value
        self.num_opens += 1


def alert_dialog():
    return rx.flex(
        rx.heading(
            f"Number of times alert dialog opened or closed: {AlertDialogState.num_opens}",
            as_="h2",
        ),
        rx.heading(f"Alert Dialog open: {AlertDialogState.opened}", as_="h2"),
        rx.alert_dialog.root(
            rx.alert_dialog.trigger(
                rx.button("Revoke access", color_scheme="red"),
            ),
            rx.alert_dialog.content(
                rx.alert_dialog.title("Revoke access"),
                rx.alert_dialog.description(
                    "Are you sure? This application will no longer be accessible and any existing sessions will be expired.",
                    size="2",
                ),
                rx.flex(
                    rx.alert_dialog.cancel(
                        rx.button("Cancel", variant="soft", color_scheme="gray"),
                    ),
                    rx.alert_dialog.action(
                        rx.button("Revoke access", color_scheme="red", variant="solid"),
                    ),
                    spacing="3",
                    margin_top="16px",
                    justify="end",
                ),
                style={"max_width": 450},
            ),
            on_open_change=AlertDialogState.count_opens,
        ),
        direction="column",
        spacing="3",
    )
```

## 通过 State 控制警告对话框

此示例展示了如何通过状态控制对话框是否打开。这是一种无需使用 `rx.alert_dialog.trigger` 即可显示对话框的简便方式。

`rx.alert_dialog.root` 有一个 `open` prop，可以设置为布尔值来控制对话框是否打开。

我们通过对话框外部的按钮以及对话框内部的 `rx.alert_dialog.cancel` 和 `rx.alert_dialog.action` 按钮来切换此 `open` prop。

```python demo exec
class AlertDialogState2(rx.State):
    opened: bool = False

    @rx.event
    def dialog_open(self):
        self.opened = ~self.opened


def alert_dialog2():
    return rx.box(
        rx.alert_dialog.root(
            rx.alert_dialog.content(
                rx.alert_dialog.title("Revoke access"),
                rx.alert_dialog.description(
                    "Are you sure? This application will no longer be accessible and any existing sessions will be expired.",
                ),
                rx.flex(
                    rx.alert_dialog.cancel(
                        rx.button("Cancel", on_click=AlertDialogState2.dialog_open),
                    ),
                    rx.alert_dialog.action(
                        rx.button(
                            "Revoke access", on_click=AlertDialogState2.dialog_open
                        ),
                    ),
                    spacing="3",
                ),
            ),
            open=AlertDialogState2.opened,
        ),
        rx.button("Button to Open the Dialog", on_click=AlertDialogState2.dialog_open),
    )
```

## 从警告对话框提交表单到数据库

此示例使用表单从警告对话框向数据库添加新用户。

1. 它定义了一个包含 name 和 email 字段的 User1 模型。
2. `add_user_to_db` 方法向数据库添加新用户，并检查邮箱是否已存在。
3. 表单提交时，调用 `add_user_to_db` 方法。
4. UI 组件包含：

- 一个用于打开警告对话框的按钮
- 一个包含添加新用户表单的警告对话框
- 姓名和邮箱输入框
- 提交和取消按钮

```python demo exec
class User1(rx.Model, table=True):
    """The user model."""

    name: str
    email: str


class State(rx.State):
    current_user: User1 = User1()

    @rx.event
    def add_user_to_db(self, form_data: dict):
        self.current_user = form_data
        ### Uncomment the code below to add your data to a database ###
        # with rx.session() as session:
        #     if session.exec(
        #         select(User1).where(user.email == self.current_user["email"])
        #     ).first():
        #         return rx.window_alert("User with this email already exists")
        #     session.add(User1(**self.current_user))
        #     session.commit()

        return rx.toast.info(
            f"User {self.current_user['name']} has been added.", position="bottom-right"
        )


def index() -> rx.Component:
    return rx.alert_dialog.root(
        rx.alert_dialog.trigger(
            rx.button(
                rx.icon("plus", size=26),
                rx.text("Add User", size="4"),
            ),
        ),
        rx.alert_dialog.content(
            rx.alert_dialog.title(
                "Add New User",
            ),
            rx.alert_dialog.description(
                "Fill the form with the user's info",
            ),
            rx.form(
                rx.flex(
                    rx.input(placeholder="User Name", name="name"),
                    rx.input(placeholder="user@reflex.dev", name="email"),
                    rx.flex(
                        rx.alert_dialog.cancel(
                            rx.button(
                                "Cancel",
                                variant="soft",
                                color_scheme="gray",
                            ),
                        ),
                        rx.alert_dialog.action(
                            rx.button("Submit", type="submit"),
                        ),
                        spacing="3",
                        justify="end",
                    ),
                    direction="column",
                    spacing="4",
                ),
                on_submit=State.add_user_to_db,
                reset_on_submit=False,
            ),
            max_width="450px",
        ),
    )
```
