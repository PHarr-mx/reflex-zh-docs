```python exec
import reflex as rx
```

# 数据仪表盘

**约 20 分钟动手实践** · 构建一个小型数据仪表盘，用户可以输入数据，并在表格和图表中渲染出来。

本教程不假设你具备任何已有的 Reflex 知识，但我们建议先快速浏览一下[基础指南](/docs/getting-started/basics)。你将学到的技术对任何 Reflex 应用都是基础性的。

本教程分为几个部分：

- **环境搭建**：准备好你的机器。
- **概览**：组件和 props。
- **使用 State 处理动态数据**：渲染会变化的数据。
- **用表单添加数据**：表单 + 事件处理器。
- **绘制图表**：Reflex 的图表组件。
- **自定义** + **[完整应用](#full-app-styled)**：进行自定义并查看完成的代码。

## 你要构建什么？

一个交互式数据仪表盘：一个用户表格、一个用于添加更多用户的表单，以及一个会随数据变化而更新的柱状图。想直接跳到后面？请跳转到底部的[完整应用](#full-app-styled)。

```python exec
import dataclasses
from collections import Counter


@dataclasses.dataclass
class User:
    """The user model."""

    name: str
    email: str
    gender: str


class State5(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]
    users_for_graph: list[dict] = []

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))
        self.transform_data()

        return rx.toast.info(
            f"User {form_data['name']} has been added.",
            position="bottom-right",
        )

    def transform_data(self):
        """Transform user gender group data into a format suitable for visualization in graphs."""
        # Count users of each gender group
        gender_counts = Counter(user.gender for user in self.users)

        # Transform into list of dict so it can be used in the graph
        self.users_for_graph = [
            {"name": gender_group, "value": count}
            for gender_group, count in gender_counts.items()
        ]


def show_user5(user: User):
    """Show a user in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.gender),
        style={"_hover": {"bg": rx.color("gray", 3)}},
        align="center",
    )


def add_customer_button5() -> rx.Component:
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
                    rx.input(placeholder="User Name", name="name", required=True),
                    rx.input(
                        placeholder="user@reflex.dev",
                        name="email",
                    ),
                    rx.select(
                        ["Male", "Female"],
                        placeholder="male",
                        name="gender",
                    ),
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
                on_submit=State5.add_user,
                reset_on_submit=False,
            ),
            max_width="450px",
        ),
    )


def graph5():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="value",
            fill=rx.color("accent", 9),
            radius=6,
            bar_size=48,
        ),
        rx.recharts.x_axis(
            data_key="name",
            tick_line=False,
            axis_line=False,
            padding={"left": 24, "right": 24},
        ),
        rx.recharts.y_axis(
            tick_line=False,
            axis_line=False,
            allow_decimals=False,
        ),
        rx.recharts.cartesian_grid(
            stroke_dasharray="3 3",
            vertical=False,
            stroke=rx.color("slate", 4),
        ),
        data=State5.users_for_graph,
        width="100%",
        height=200,
        margin={"top": 8, "right": 8, "bottom": 0, "left": 0},
    )
```

```python eval
rx.box(
    rx.vstack(
        rx.hstack(
            rx.vstack(
                rx.text(
                    "Users",
                    size="4",
                    weight="bold",
                    color=rx.color("slate", 12),
                    text_align="left",
                    width="100%",
                ),
                rx.text(
                    "Add customers and watch the chart update.",
                    size="2",
                    color=rx.color("slate", 10),
                    text_align="left",
                    width="100%",
                ),
                spacing="1",
                align="start",
            ),
            rx.spacer(),
            add_customer_button5(),
            align="center",
            width="100%",
        ),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Gender"),
                ),
            ),
            rx.table.body(
                rx.foreach(State5.users, show_user5),
            ),
            variant="surface",
            size="2",
            width="100%",
        ),
        graph5(),
        align="stretch",
        width="100%",
        on_mouse_enter=State5.transform_data,
        spacing="4",
        padding="1.75em 2em",
    ),
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    margin_y="1em",
    background=rx.color("slate", 1),
)
```

## 环境搭建

1. 如果你还没有安装，请先[安装 Reflex](/docs/getting-started/installation)。
2. 创建一个名为 `dashboard_tutorial` 的文件夹并 `cd` 进入其中。
3. 运行 `uv init` 和 `uv add reflex`。
4. 运行 `uv run reflex init` 并选择模板 `0`（空白模板）。
5. 运行 `uv run reflex run` 启动应用并确认一切正常。

## 概览

### 起始代码

`reflex init` 命令会生成一个 `rxconfig.py`（应用[配置](/docs/advanced-onboarding/configuration)）、一个用于存放静态文件的 `assets/` 文件夹，以及一个包含你应用的 `dashboard_tutorial/dashboard_tutorial.py` 模块。打开该模块并替换其内容——我们将从零开始构建应用。

一个最简的 Reflex 页面就是一个组件函数加上一个注册它的应用：

```python
import reflex as rx


def index() -> rx.Component:
    return rx.text("Hello World!")


app = rx.App()
app.add_page(index)
```

```md alert info
在本教程的剩余部分，`app = rx.App()` 和 `app.add_page` 这两行是默认存在的，不会展示——我们将在[自定义](#customize)部分再回到它们。
```

### 创建表格

`rx.table` 组件有一个 `root`，它包裹着一个 `header` 和一个 `body`。header 接收 `row` → `column_header_cell` 组件；body 接收 `row` → `cell` 组件，其中存放实际数据。像 `variant` 和 `size` 这样的 props 可以自定义外观：

```python eval
rx.box(
    rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Gender"),
            ),
        ),
        rx.table.body(
            rx.table.row(
                rx.table.cell("Danilo Sousa"),
                rx.table.cell("danilo@example.com"),
                rx.table.cell("Male"),
            ),
            rx.table.row(
                rx.table.cell("Zahra Ambessa"),
                rx.table.cell("zahra@example.com"),
                rx.table.cell("Female"),
            ),
        ),
        variant="surface",
        size="3",
    ),
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    padding="2em",
)
```

```python
def index() -> rx.Component:
    return rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Gender"),
            ),
        ),
        rx.table.body(
            rx.table.row(
                rx.table.cell("Danilo Sousa"),
                rx.table.cell("danilo@example.com"),
                rx.table.cell("Male"),
            ),
            rx.table.row(
                rx.table.cell("Zahra Ambessa"),
                rx.table.cell("zahra@example.com"),
                rx.table.cell("Female"),
            ),
        ),
        variant="surface",
        size="3",
    )
```

## 使用 State 处理动态数据

上面的表格是静态的——行是硬编码的。为了让它变成动态的，我们将数据移到 **state** 上：state 是一个 Python 类，其字段（[state vars](/docs/state/overview)）保存应用的数据，其方法（[事件处理器](/docs/events/events-overview)）则用于修改这些数据。

我们将每一行建模为一个 `User` dataclass，这样就可以按名称访问字段（`user.name`），而不是按索引访问：

```python
import dataclasses


@dataclasses.dataclass
class User:
    name: str
    email: str
    gender: str


class State(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]
```

要遍历一个列表类型的 state var，请使用 [`rx.foreach`](/docs/components/rendering-iterables)——它接收一个可迭代对象和一个渲染每个元素的函数。在这里 `show_user` 接收一个 `User` 并返回一个 `table.row`：

```python
def show_user(user: User) -> rx.Component:
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.gender),
    )


def index() -> rx.Component:
    return rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Gender"),
            ),
        ),
        rx.table.body(
            rx.foreach(State.users, show_user),
        ),
        variant="surface",
        size="3",
    )
```

```md alert info
# 为什么不用 `for` 循环？

普通的 `for` 循环在编译时运行，但 state vars 在运行时才会变化——因此渲染出的行不会更新。`rx.foreach` 会告诉编译器在 state var 变化时重新渲染。参见[编译时与运行时](/docs/getting-started/basics#compile-time-vs.-runtime)。
```

```python exec
import dataclasses


@dataclasses.dataclass
class User:
    """The user model."""

    name: str
    email: str
    gender: str


class State2(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]


def show_user2(user: User):
    """Show a person in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.gender),
    )
```

```python eval
rx.box(
    rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Gender"),
            ),
        ),
        rx.table.body(
            rx.foreach(State2.users, show_user2),
        ),
        variant="surface",
        size="3",
    ),
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    padding="2em",
)
```

表格看起来一样，但现在行来自 state——接下来我们将添加一个表单，向 `State.users` 追加数据，这样新行就会自动出现。

## 用表单添加数据

我们使用 `rx.form` 来构建表单，它接收若干组件，例如 `rx.input` 和 `rx.select`，这些组件代表允许你添加信息并随表单提交的表单字段。有关表单组件的更多信息，请查看 [form](/docs/library/forms/form) 文档。

`rx.input` 组件接收若干 props。`placeholder` prop 是输入框为空时显示的文本。`name` prop 是输入框的名称，在表单提交时会通过字典传递出去。`required` prop 是一个布尔值，用于确定该输入框是否为必填项。

`rx.select` 组件接收一个在下拉菜单中显示的选项列表。这里使用的其他 props 与 `rx.input` 组件完全相同。

```python demo
rx.form(
    rx.input(placeholder="User Name", name="name", required=True),
    rx.input(
        placeholder="user@reflex.dev",
        name="email",
    ),
    rx.select(
        ["Male", "Female"],
        placeholder="Male",
        name="gender",
    ),
)
```

如示例所示，这个表单非常紧凑，因此我们需要添加一些样式让它看起来更好。我们可以通过在表单字段周围添加一个 `vstack` 组件来做到这一点。`vstack` 组件会将表单字段垂直堆叠。有关如何布局应用的更多信息，请查看 [layout](/docs/styling/layout) 文档。

```python demo
rx.form(
    rx.vstack(
        rx.input(placeholder="User Name", name="name", required=True),
        rx.input(
            placeholder="user@reflex.dev",
            name="email",
        ),
        rx.select(
            ["Male", "Female"],
            placeholder="Male",
            name="gender",
        ),
    ),
)
```

现在你可能已经意识到，我们有了所有的表单字段，但却没有办法提交表单。我们可以通过向 `vstack` 组件添加一个 `rx.button` 组件来为表单添加一个提交按钮。`rx.button` 组件接收显示在按钮上的文本以及 `type` prop，即按钮的类型。`type` prop 被设置为 `submit`，这样点击按钮时表单就会被提交。

除此之外，我们还需要一种方法在表单提交时更新 `users` 状态变量。所有的状态更改都通过 state 类中的函数来处理，这些函数称为[事件处理器（event handlers）](/docs/events/events-overview)。

组件有一些称为事件触发器（event triggers）的特殊 props，例如 `on_submit`，可用于让组件具有交互性。事件触发器将组件与事件处理器连接起来，由事件处理器更新状态。不同的事件触发器期望你所挂钩的事件处理器接收不同的参数（有些则不接收任何参数）。

`rx.form` 的 `on_submit` 事件触发器被挂钩到定义在 `State` 类中的 `add_user` 事件处理器。这个事件触发器期望将一个包含表单数据的 `dict` 传递给它所挂钩的事件处理器。`add_user` 事件处理器以字典形式接收表单数据，并将其追加到 `users` 状态变量中。

```python
class State(rx.State):
    ...

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))


def form():
    return rx.form(
        rx.vstack(
            rx.input(placeholder="User Name", name="name", required=True),
            rx.input(
                placeholder="user@reflex.dev",
                name="email",
            ),
            rx.select(
                ["Male", "Female"],
                placeholder="Male",
                name="gender",
            ),
            rx.button("Submit", type="submit"),
        ),
        on_submit=State.add_user,
        reset_on_submit=True,
    )
```

最后，我们必须将新定义的 `form()` 组件添加到 `index()` 函数中，以便表单渲染在页面上。

下面是目前为止应用的完整代码。如果你试用这个表单，你会发现可以通过填写表单并点击提交按钮来向表格添加新用户。提交时，表单数据还会以 toast（页面角落里的一个小窗口）的形式显示在屏幕上。

```python exec
class State3(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))

        return rx.toast.info(
            f"User has been added: {form_data}.",
            position="bottom-right",
        )


def show_user(user: User):
    """Show a person in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.gender),
    )


def form():
    return rx.form(
        rx.vstack(
            rx.input(placeholder="User Name", name="name", required=True),
            rx.input(
                placeholder="user@reflex.dev",
                name="email",
            ),
            rx.select(
                ["Male", "Female"],
                placeholder="Male",
                name="gender",
            ),
            rx.button("Submit", type="submit"),
        ),
        on_submit=State3.add_user,
        reset_on_submit=True,
    )
```

```python eval
rx.vstack(
    form(),
    rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Gender"),
            ),
        ),
        rx.table.body(
            rx.foreach(State3.users, show_user),
        ),
        variant="surface",
        size="3",
    ),
    spacing="4",
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    padding="2em",
)
```

```python
class State(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))


def show_user(user: User):
    """Show a person in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.gender),
    )


def form():
    return rx.form(
        rx.vstack(
            rx.input(placeholder="User Name", name="name", required=True),
            rx.input(
                placeholder="user@reflex.dev",
                name="email",
            ),
            rx.select(
                ["Male", "Female"],
                placeholder="Male",
                name="gender",
            ),
            rx.button("Submit", type="submit"),
        ),
        on_submit=State.add_user,
        reset_on_submit=True,
    )


def index() -> rx.Component:
    return rx.vstack(
        form(),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Gender"),
                ),
            ),
            rx.table.body(
                rx.foreach(State.users, show_user),
            ),
            variant="surface",
            size="3",
        ),
    )
```

### 将表单放入对话框

在 Reflex 中，我们希望让用户的交互尽可能直观。将我们刚刚构建的表单放置在一个遮罩层中，可以通过调暗背景来创建一个聚焦的交互，并且当你有多个操作点（例如编辑和删除）时，也能确保更简洁的布局。

我们将把表单放置在一个 `rx.dialog` 组件（也称为模态框）内部。`rx.dialog.root` 包含对话框的所有部分，而 `rx.dialog.trigger` 则包裹着将打开对话框的控件。在我们的例子中，触发器将是一个写着 "Add User" 的 `rx.button`，如下所示。

```python
rx.dialog.trigger(
    rx.button(
        rx.icon("plus", size=26),
        rx.text("Add User", size="4"),
    ),
)
```

在触发器之后，我们有 `rx.dialog.content`，它包含对话框内的所有内容，包括标题、描述和我们的表单。关闭对话框的第一种方式是不提交表单直接关闭，第二种方式是通过提交表单来关闭对话框，如下所示。这需要在对话框内放置两个 `rx.dialog.close` 组件。

```python
(
    rx.dialog.close(
        rx.button(
            "Cancel",
            variant="soft",
            color_scheme="gray",
        ),
    ),
)
rx.dialog.close(
    rx.button("Submit", type="submit"),
)
```

包含表单的对话框的完整代码如下。

```python demo
rx.dialog.root(
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
            # flex is similar to vstack and used to layout the form fields
            rx.flex(
                rx.input(placeholder="User Name", name="name", required=True),
                rx.input(
                    placeholder="user@reflex.dev",
                    name="email",
                ),
                rx.select(
                    ["Male", "Female"],
                    placeholder="Male",
                    name="gender",
                ),
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
            on_submit=State3.add_user,
            reset_on_submit=False,
        ),
        # max_width is used to limit the width of the dialog
        max_width="450px",
    ),
)
```

此时，我们有了一个应用，允许你通过填写表单向表格添加用户。该表单被放置在一个对话框中，可以通过点击 "Add User" 按钮打开。我们将组件的名称从 `form` 改为 `add_customer_button`，并在 `index` 组件中更新它。目前为止的完整应用和代码如下。

```python exec
def add_customer_button() -> rx.Component:
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
                    rx.input(placeholder="User Name", name="name", required=True),
                    rx.input(
                        placeholder="user@reflex.dev",
                        name="email",
                    ),
                    rx.select(
                        ["Male", "Female"],
                        placeholder="Male",
                        name="gender",
                    ),
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
                on_submit=State3.add_user,
                reset_on_submit=False,
            ),
            max_width="450px",
        ),
    )
```

```python eval
rx.vstack(
    add_customer_button(),
    rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Gender"),
            ),
        ),
        rx.table.body(
            rx.foreach(State3.users, show_user),
        ),
        variant="surface",
        size="3",
    ),
    spacing="4",
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    padding="2em",
)
```

```python
@dataclasses.dataclass
class User:
    """The user model."""

    name: str
    email: str
    gender: str


class State(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))


def show_user(user: User):
    """Show a person in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.gender),
    )


def add_customer_button() -> rx.Component:
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
                    rx.input(placeholder="User Name", name="name", required=True),
                    rx.input(
                        placeholder="user@reflex.dev",
                        name="email",
                    ),
                    rx.select(
                        ["Male", "Female"],
                        placeholder="Male",
                        name="gender",
                    ),
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
                on_submit=State.add_user,
                reset_on_submit=False,
            ),
            max_width="450px",
        ),
    )


def index() -> rx.Component:
    return rx.vstack(
        add_customer_button(),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Gender"),
                ),
            ),
            rx.table.body(
                rx.foreach(State.users, show_user),
            ),
            variant="surface",
            size="3",
        ),
    )
```


## 绘制图表

接下来，我们将使用 Reflex 内置的 recharts 库将用户数据绘制成图表，按性别统计用户数量。

### 转换数据

Reflex 中的图表组件期望接收一个字典列表。每个字典代表图表上的一个数据点，并包含 x 值和 y 值。我们将在 state 中创建一个名为 `transform_data` 的新事件处理器，将用户数据转换为图表组件所期望的格式。我们还必须创建一个名为 `users_for_graph` 的新状态变量来存储转换后的数据，它将用于渲染图表。

```python
from collections import Counter


class State(rx.State):
    users: list[User] = []
    users_for_graph: list[dict] = []

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))
        self.transform_data()

    def transform_data(self):
        """Transform user gender group data into a format suitable for visualization in graphs."""
        # Count users of each gender group
        gender_counts = Counter(user.gender for user in self.users)

        # Transform into list of dict so it can be used in the graph
        self.users_for_graph = [
            {"name": gender_group, "value": count}
            for gender_group, count in gender_counts.items()
        ]
```

如上所示，`transform_data` 事件处理器使用 `collections` 模块中的 `Counter` 类来统计每种性别的用户数量。然后我们从中创建一个字典列表，并将其设置给状态 var `users_for_graph`。

最后我们可以看到，每当我们通过提交表单并运行 `add_user` 事件处理器添加新用户时，我们都会调用 `transform_data` 事件处理器来更新 `users_for_graph` 状态变量。

### 渲染图表

我们使用 `rx.recharts.bar_chart` 组件来渲染图表。我们将图表数据的状态变量作为 `data=State.users_for_graph` 传递进去。我们还传入了一个 `rx.recharts.bar` 组件，它代表图表上的柱条。`rx.recharts.bar` 组件接收 `data_key` prop，它是数据字典中代表柱条 y 值的键。`stroke` 和 `fill` props 用于设置柱条的颜色。

`rx.recharts.bar_chart` 组件还接收 `rx.recharts.x_axis` 和 `rx.recharts.y_axis` 组件，它们分别代表图表的 x 轴和 y 轴。`rx.recharts.x_axis` 组件的 `data_key` prop 被设置为数据字典中代表柱条 x 值的键。最后，我们添加 `width` 和 `height` props 来设置图表的尺寸。

```python
def graph():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="value",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=State.users_for_graph,
        width="100%",
        height=250,
    )
```

最后，我们将这个 `graph()` 组件添加到 `index()` 组件中，以便图表渲染在页面上。包含图表的完整应用代码如下。如果你试用一下，你会发现每当你向表格添加新用户时，图表都会更新。

```python exec
from collections import Counter


class State4(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]
    users_for_graph: list[dict] = []

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))
        self.transform_data()

        return rx.toast.info(
            f"User {form_data['name']} has been added.",
            position="bottom-right",
        )

    def transform_data(self):
        """Transform user gender group data into a format suitable for visualization in graphs."""
        # Count users of each gender group
        gender_counts = Counter(user.gender for user in self.users)

        # Transform into list of dict so it can be used in the graph
        self.users_for_graph = [
            {"name": gender_group, "value": count}
            for gender_group, count in gender_counts.items()
        ]


def add_customer_button() -> rx.Component:
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
                    rx.input(placeholder="User Name", name="name", required=True),
                    rx.input(
                        placeholder="user@reflex.dev",
                        name="email",
                    ),
                    rx.select(
                        ["Male", "Female"],
                        placeholder="Male",
                        name="gender",
                    ),
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
                on_submit=State4.add_user,
                reset_on_submit=False,
            ),
            max_width="450px",
        ),
    )


def graph():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="value",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=State4.users_for_graph,
        width="100%",
        height=250,
    )
```

```python eval
rx.vstack(
    add_customer_button(),
    rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Gender"),
            ),
        ),
        rx.table.body(
            rx.foreach(State4.users, show_user),
        ),
        variant="surface",
        size="3",
    ),
    graph(),
    spacing="4",
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    padding="2em",
)
```

```python
from collections import Counter


class State(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]
    users_for_graph: list[dict] = []

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))
        self.transform_data()

    def transform_data(self):
        """Transform user gender group data into a format suitable for visualization in graphs."""
        # Count users of each gender group
        gender_counts = Counter(user.gender for user in self.users)

        # Transform into list of dict so it can be used in the graph
        self.users_for_graph = [
            {"name": gender_group, "value": count}
            for gender_group, count in gender_counts.items()
        ]


def show_user(user: User):
    """Show a person in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.gender),
    )


def add_customer_button() -> rx.Component:
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
                    rx.input(placeholder="User Name", name="name", required=True),
                    rx.input(
                        placeholder="user@reflex.dev",
                        name="email",
                    ),
                    rx.select(
                        ["Male", "Female"],
                        placeholder="male",
                        name="gender",
                    ),
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
                on_submit=State.add_user,
                reset_on_submit=False,
            ),
            max_width="450px",
        ),
    )


def graph():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="value",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=State.users_for_graph,
        width="100%",
        height=250,
    )


def index() -> rx.Component:
    return rx.vstack(
        add_customer_button(),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Gender"),
                ),
            ),
            rx.table.body(
                rx.foreach(State.users, show_user),
            ),
            variant="surface",
            size="3",
        ),
        graph(),
    )
```

如果你在本地运行应用且没有种子用户，那么在你添加用户之前图表都是空的——`transform_data` 只有在添加用户时才会运行。下一节将通过在页面加载时调用它来解决这个问题。

## 自定义

### 重新认识 `app.add_page`

在本教程开头，我们提到 `app.add_page` 函数是每个 Reflex 应用都必需的。这个函数用于将组件添加到页面。

`app.add_page` 目前看起来是这样的：`app.add_page(index)`。我们可以通过设置 `route` prop 来更改页面渲染所在的路由，例如 `route="/custom-route"`，这会将该页面的路由更改为 `http://localhost:3000/custom-route`。

我们还可以设置一个显示在浏览器标签页中的 `title`，以及一个显示在搜索结果中的 `description`。

为了解决上面提到的页面加载时图表不加载的问题，我们可以在 `app.add_page` 中使用 `on_load`，在页面加载时调用 `transform_data` 事件处理器。这看起来像 `on_load=State.transform_data`。下面看看添加了上述部分更改后，我们的 `app.add_page` 会是什么样子。

```python eval
rx.vstack(
    add_customer_button(),
    rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Gender"),
            ),
        ),
        rx.table.body(
            rx.foreach(State4.users, show_user),
        ),
        variant="surface",
        size="3",
    ),
    graph(),
    on_mouse_enter=State4.transform_data,
    spacing="4",
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    padding="2em",
)
```

```python
app.add_page(
    index,
    title="Customer Data App",
    description="A simple app to manage customer data.",
    on_load=State.transform_data,
)
```

### 重新认识 `rx.App()`

在教程开头，我们还提到我们使用 `app=rx.App()` 定义了应用。我们也可以向 `rx.App` 组件传入一些 props 来自定义应用。

最重要的是 `theme`，它允许你自定义应用的外观和风格。`theme` prop 接收一个 `rx.theme` 组件，该组件有若干可以设置的 props。

`radius` prop 为应用设置全局圆角值，所有具有 `radius` prop 的组件都会继承它。可以通过手动设置某个特定组件的 `radius` prop 在局部覆盖它。

`accent_color` prop 设置应用的强调色。有关选项的完整列表，请参见 [theme 文档](/docs/library/other/theme)。

要查看可在应用级别设置的其他 props，请查看这份[文档](/docs/styling/theming)

```python
app = rx.App(
    theme=rx.theme(radius="full", accent_color="grass"),
)
```

主题在应用级别生效，因此你需要在本地运行才能看到效果。

## 完整应用（含样式）

最后，让我们做一些样式更新。我们将为表格行添加悬停样式，并在 `show_user` 中使用 `style={"_hover": {"bg": rx.color("gray", 3)}}, align="center"` 将表格居中。

此外，我们将向 `index()` 组件添加一些 `width="100%"` 和 `align="center"`，以使页面上的元素居中并确保它们撑满页面的整个宽度。

请查看下方的完整代码和交互式应用：

```python eval
rx.box(
    rx.vstack(
        rx.hstack(
            rx.vstack(
                rx.text(
                    "Users",
                    size="4",
                    weight="bold",
                    color=rx.color("slate", 12),
                    text_align="left",
                    width="100%",
                ),
                rx.text(
                    "Add customers and watch the chart update.",
                    size="2",
                    color=rx.color("slate", 10),
                    text_align="left",
                    width="100%",
                ),
                spacing="1",
                align="start",
            ),
            rx.spacer(),
            add_customer_button5(),
            align="center",
            width="100%",
        ),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Gender"),
                ),
            ),
            rx.table.body(
                rx.foreach(State5.users, show_user5),
            ),
            variant="surface",
            size="2",
            width="100%",
        ),
        graph5(),
        align="stretch",
        width="100%",
        on_mouse_enter=State5.transform_data,
        spacing="4",
        padding="1.75em 2em",
    ),
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    margin_y="1em",
    background=rx.color("slate", 1),
)
```

```python
import reflex as rx
from collections import Counter


@dataclasses.dataclass
class User:
    """The user model."""

    name: str
    email: str
    gender: str


class State(rx.State):
    users: list[User] = [
        User(name="Danilo Sousa", email="danilo@example.com", gender="Male"),
        User(name="Zahra Ambessa", email="zahra@example.com", gender="Female"),
    ]
    users_for_graph: list[dict] = []

    def add_user(self, form_data: dict):
        self.users.append(User(**form_data))
        self.transform_data()

    def transform_data(self):
        """Transform user gender group data into a format suitable for visualization in graphs."""
        # Count users of each gender group
        gender_counts = Counter(user.gender for user in self.users)

        # Transform into list of dict so it can be used in the graph
        self.users_for_graph = [
            {"name": gender_group, "value": count}
            for gender_group, count in gender_counts.items()
        ]


def show_user(user: User):
    """Show a user in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.gender),
        style={"_hover": {"bg": rx.color("gray", 3)}},
        align="center",
    )


def add_customer_button() -> rx.Component:
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
                    rx.input(placeholder="User Name", name="name", required=True),
                    rx.input(
                        placeholder="user@reflex.dev",
                        name="email",
                    ),
                    rx.select(
                        ["Male", "Female"],
                        placeholder="male",
                        name="gender",
                    ),
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
                on_submit=State.add_user,
                reset_on_submit=False,
            ),
            max_width="450px",
        ),
    )


def graph():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="value",
            fill=rx.color("accent", 9),
            radius=6,
            bar_size=48,
        ),
        rx.recharts.x_axis(
            data_key="name",
            tick_line=False,
            axis_line=False,
            padding={"left": 24, "right": 24},
        ),
        rx.recharts.y_axis(
            tick_line=False,
            axis_line=False,
            allow_decimals=False,
        ),
        rx.recharts.cartesian_grid(
            stroke_dasharray="3 3",
            vertical=False,
            stroke=rx.color("slate", 4),
        ),
        data=State.users_for_graph,
        width="100%",
        height=200,
        margin={"top": 8, "right": 8, "bottom": 0, "left": 0},
    )


def index() -> rx.Component:
    return rx.box(
        rx.vstack(
            rx.hstack(
                rx.vstack(
                    rx.text(
                        "Users",
                        size="4",
                        weight="bold",
                        color=rx.color("slate", 12),
                        text_align="left",
                        width="100%",
                    ),
                    rx.text(
                        "Add customers and watch the chart update.",
                        size="2",
                        color=rx.color("slate", 10),
                        text_align="left",
                        width="100%",
                    ),
                    spacing="1",
                    align="start",
                ),
                rx.spacer(),
                add_customer_button(),
                align="center",
                width="100%",
            ),
            rx.table.root(
                rx.table.header(
                    rx.table.row(
                        rx.table.column_header_cell("Name"),
                        rx.table.column_header_cell("Email"),
                        rx.table.column_header_cell("Gender"),
                    ),
                ),
                rx.table.body(
                    rx.foreach(State.users, show_user),
                ),
                variant="surface",
                size="2",
                width="100%",
            ),
            graph(),
            align="stretch",
            width="100%",
            spacing="4",
            padding="1.75em 2em",
        ),
        border=f"1px solid {rx.color('slate', 5)}",
        border_radius="12px",
        margin_y="1em",
        background=rx.color("slate", 1),
    )


app = rx.App(
    theme=rx.theme(radius="full", accent_color="grass"),
)

app.add_page(
    index,
    title="Customer Data App",
    description="A simple app to manage customer data.",
    on_load=State.transform_data,
)
```

## 回顾

你构建了：

- 一个显示用户数据的表格。
- 一个（位于对话框内的）用于添加新用户的表单。
- 一个可视化分布情况的柱状图。

在此过程中，你学到了：

- **State**——如何存储随时间变化的数据。
- **Events**——如何响应用户操作并更新 UI。
- **Styling**——调整主题、布局和悬停状态。
