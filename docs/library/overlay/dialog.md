---
components:
  - rx.dialog.root
  - rx.dialog.trigger
  - rx.dialog.title
  - rx.dialog.content
  - rx.dialog.description
  - rx.dialog.close

only_low_level:
  - True

DialogRoot: |
  lambda **props: rx.dialog.root(
      rx.dialog.trigger(rx.button("Open Dialog")),
      rx.dialog.content(
          rx.dialog.title("Welcome to Reflex!"),
          rx.dialog.description(
              "This is a dialog component. You can render anything you want in here.",
          ),
          rx.dialog.close(
              rx.button("Close Dialog"),
          ),
      ),
      **props,
  )

DialogContent: |
  lambda **props: rx.dialog.root(
      rx.dialog.trigger(rx.button("Open Dialog")),
      rx.dialog.content(
          rx.dialog.title("Welcome to Reflex!"),
          rx.dialog.description(
              "This is a dialog component. You can render anything you want in here.",
          ),
          rx.dialog.close(
              rx.button("Close Dialog"),
          ),
          **props,
      ),
  )
---

```python exec
import reflex as rx
```

# Dialog（对话框）

`dialog.root` 包含对话框的所有部分。

`dialog.trigger` 包裹用于打开对话框的控件。

`dialog.content` 包含对话框的内容。

`dialog.title` 是对话框打开时播报的标题。

`dialog.description` 是对话框打开时播报的描述。

`dialog.close` 包裹用于关闭对话框的控件。

```python demo
rx.dialog.root(
    rx.dialog.trigger(rx.button("Open Dialog")),
    rx.dialog.content(
        rx.dialog.title("Welcome to Reflex!"),
        rx.dialog.description(
            "This is a dialog component. You can render anything you want in here.",
        ),
        rx.dialog.close(
            rx.button("Close Dialog", size="3"),
        ),
    ),
)
```

## 实际场景示例

```python demo
rx.dialog.root(
    rx.dialog.trigger(rx.button("Edit Profile", size="4")),
    rx.dialog.content(
        rx.dialog.title("Edit Profile"),
        rx.dialog.description(
            "Change your profile details and preferences.",
            size="2",
            margin_bottom="16px",
        ),
        rx.flex(
            rx.text("Name", as_="div", size="2", margin_bottom="4px", weight="bold"),
            rx.input(default_value="Freja Johnson", placeholder="Enter your name"),
            rx.text("Email", as_="div", size="2", margin_bottom="4px", weight="bold"),
            rx.input(default_value="freja@example.com", placeholder="Enter your email"),
            direction="column",
            spacing="3",
        ),
        rx.flex(
            rx.dialog.close(
                rx.button("Cancel", color_scheme="gray", variant="soft"),
            ),
            rx.dialog.close(
                rx.button("Save"),
            ),
            spacing="3",
            margin_top="16px",
            justify="end",
        ),
    ),
)
```

```python demo
rx.dialog.root(
    rx.dialog.trigger(rx.button("View users", size="4")),
    rx.dialog.content(
        rx.dialog.title("Users"),
        rx.dialog.description("The following users have access to this project."),
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
            rx.dialog.close(
                rx.button("Close", variant="soft", color_scheme="gray"),
            ),
            spacing="3",
            justify="end",
        ),
    ),
)
```

## 对话框打开或关闭时的事件

当对话框的 `open` 状态发生变化时，会调用 `on_open_change` 事件。它与 `open` prop 配合使用，`open` 的值会传递给事件处理器。

```python demo exec
class DialogState(rx.State):
    num_opens: int = 0
    opened: bool = False

    @rx.event
    def count_opens(self, value: bool):
        self.opened = value
        self.num_opens += 1


def dialog_example():
    return rx.flex(
        rx.heading(
            f"Number of times dialog opened or closed: {DialogState.num_opens}",
            as_="h2",
        ),
        rx.heading(f"Dialog open: {DialogState.opened}", as_="h2"),
        rx.dialog.root(
            rx.dialog.trigger(rx.button("Open Dialog")),
            rx.dialog.content(
                rx.dialog.title("Welcome to Reflex!"),
                rx.dialog.description(
                    "This is a dialog component. You can render anything you want in here.",
                ),
                rx.dialog.close(
                    rx.button("Close Dialog", size="3"),
                ),
            ),
            on_open_change=DialogState.count_opens,
        ),
        direction="column",
        spacing="3",
    )
```

查看[菜单文档](/docs/library/overlay/dropdown-menu)了解从下拉菜单中打开对话框的示例。

## 从对话框提交表单到数据库

此示例使用表单从对话框向数据库添加新用户。

1. 它定义了一个包含 name 和 email 字段的 User 模型。
2. `add_user_to_db` 方法向数据库添加新用户，并检查邮箱是否已存在。
3. 表单提交时，调用 `add_user_to_db` 方法。
4. UI 组件包含：

- 一个用于打开对话框的按钮
- 一个包含添加新用户表单的对话框
- 姓名和邮箱输入框
- 提交和取消按钮

```python demo exec
class User(rx.Model, table=True):
    """The user model."""

    name: str
    email: str


class State(rx.State):
    current_user: User = User()

    @rx.event
    def add_user_to_db(self, form_data: dict):
        self.current_user = form_data
        ### Uncomment the code below to add your data to a database ###
        # with rx.session() as session:
        #     if session.exec(
        #         select(User).where(user.email == self.current_user["email"])
        #     ).first():
        #         return rx.window_alert("User with this email already exists")
        #     session.add(User(**self.current_user))
        #     session.commit()

        return rx.toast.info(
            f"User {self.current_user['name']} has been added.", position="bottom-right"
        )


def index() -> rx.Component:
    return rx.dialog.root(
        rx.dialog.trigger(
            rx.button(
                rx.icon("plus", size=26),
                rx.text("Add User", size="4"),
            ),
        ),
        rx.dialog.content(
            rx.dialog.title(
                "Add New User",
            ),
            rx.dialog.description(
                "Fill the form with the user's info",
            ),
            rx.form(
                rx.flex(
                    rx.input(placeholder="User Name", name="name"),
                    rx.input(placeholder="user@reflex.dev", name="email"),
                    rx.flex(
                        rx.dialog.close(
                            rx.button(
                                "Cancel",
                                variant="soft",
                                color_scheme="gray",
                            ),
                        ),
                        rx.dialog.close(
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
