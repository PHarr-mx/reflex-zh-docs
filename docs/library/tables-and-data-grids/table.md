---
meta_description: "Build tables in Python with Reflex. The rx.table component is a semantic, composable React table for presenting tabular data with headers, rows, and cells — all in pure Python, no JavaScript."
components:
  - rx.table.root
  - rx.table.header
  - rx.table.row
  - rx.table.column_header_cell
  - rx.table.body
  - rx.table.cell
  - rx.table.row_header_cell

only_low_level:
  - True

TableRoot: |
  lambda **props: rx.table.root(
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
          width="80%",
          **props,
      )

TableRow: |
  lambda **props: rx.table.root(
          rx.table.header(
              rx.table.row(
                  rx.table.column_header_cell("Full Name"),
                  rx.table.column_header_cell("Email"),
                  rx.table.column_header_cell("Group"),
                  **props,
              ),
          ),
          rx.table.body(
              rx.table.row(
                  rx.table.row_header_cell("Danilo Rosa"),
                  rx.table.cell(rx.text("danilo@example.com", as_="p"), rx.text("danilo@yahoo.com", as_="p"), rx.text("danilo@gmail.com", as_="p"),),
                  rx.table.cell("Developer"),
                  **props,
              ),
              rx.table.row(
                  rx.table.row_header_cell("Zahra Ambessa"),
                  rx.table.cell("zahra@example.com"),
                  rx.table.cell("Admin"),
                  **props,
              ),
          ),
          width="80%",
      )

TableColumnHeaderCell: |
  lambda **props: rx.table.root(
          rx.table.header(
              rx.table.row(
                  rx.table.column_header_cell("Full Name", **props,),
                  rx.table.column_header_cell("Email", **props,),
                  rx.table.column_header_cell("Group", **props,),
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
          width="80%",
      )

TableCell: |
  lambda **props: rx.table.root(
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
                  rx.table.cell("danilo@example.com", **props,),
                  rx.table.cell("Developer", **props,),
              ),
              rx.table.row(
                  rx.table.row_header_cell("Zahra Ambessa"),
                  rx.table.cell("zahra@example.com", **props,),
                  rx.table.cell("Admin", **props,),
              ),
          ),
          width="80%",
      )

TableRowHeaderCell: |
  lambda **props: rx.table.root(
          rx.table.header(
              rx.table.row(
                  rx.table.column_header_cell("Full Name"),
                  rx.table.column_header_cell("Email"),
                  rx.table.column_header_cell("Group"),
              ),
          ),
          rx.table.body(
              rx.table.row(
                  rx.table.row_header_cell("Danilo Rosa", **props,),
                  rx.table.cell("danilo@example.com"),
                  rx.table.cell("Developer"),
              ),
              rx.table.row(
                  rx.table.row_header_cell("Zahra Ambessa", **props,),
                  rx.table.cell("zahra@example.com"),
                  rx.table.cell("Admin"),
              ),
          ),
          width="80%",
      )
---

```python exec
import reflex as rx


class Customer(rx.Model, table=True):
    name: str
    email: str
    phone: str
    address: str
```

# Table（表格）

Reflex 表格组件（`rx.table`）是一个语义化的、可组合的表格，用于在纯 Python 中展示表格数据。它渲染一个标准的 React/HTML 表格，包含你自己组合的表头、行和单元格。

如果你只是想[展示静态数据](/docs/library/tables-and-data-grids/data-table)，那么 [`rx.data_table`](/docs/library/tables-and-data-grids/data-table) 可能更适合你的用例，因为它内置了分页、搜索和排序功能。

## 基本示例

```python demo
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell("Full name"),
            rx.table.column_header_cell("Email"),
            rx.table.column_header_cell("Group"),
        ),
    ),
    rx.table.body(
        rx.table.row(
            rx.table.row_header_cell("Danilo Sousa"),
            rx.table.cell("danilo@example.com"),
            rx.table.cell("Developer"),
        ),
        rx.table.row(
            rx.table.row_header_cell("Zahra Ambessa"),
            rx.table.cell("zahra@example.com"),
            rx.table.cell("Admin"),
        ),
        rx.table.row(
            rx.table.row_header_cell("Jasper Eriks"),
            rx.table.cell("jasper@example.com"),
            rx.table.cell("Developer"),
        ),
    ),
    width="100%",
)
```

```md alert info
# 设置表格的 `width` 使其适应容器，防止溢出。
```

## 显示 State 数据（使用 foreach）

很多时候我们需要表格中展示的数据是动态的。动态数据必须放在 `State` 中。稍后我们将展示如何从数据库访问数据以及如何从源文件加载数据的示例。

在此示例中，`State` 中有一个 `people` 数据结构，通过 [`rx.foreach` 进行迭代](/docs/components/rendering-iterables)。

```python demo exec
class TableForEachState(rx.State):
    people: list[list] = [
        ["Danilo Sousa", "danilo@example.com", "Developer"],
        ["Zahra Ambessa", "zahra@example.com", "Admin"],
        ["Jasper Eriks", "jasper@example.com", "Developer"],
    ]


def show_person_from_list(person: list):
    """Show a person in a table row."""
    return rx.table.row(
        rx.table.cell(person[0]),
        rx.table.cell(person[1]),
        rx.table.cell(person[2]),
    )


def foreach_table_example():
    return rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Full name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Group"),
            ),
        ),
        rx.table.body(rx.foreach(TableForEachState.people, show_person_from_list)),
        width="100%",
    )
```

也可以定义一个 `class`（如下面的 `Person`），然后以 `list[Person]` 的形式迭代此数据结构。

```python
import dataclasses


@dataclasses.dataclass
class Person:
    full_name: str
    email: str
    group: str
```

## 排序和筛选（搜索）

在此示例中，我们展示了两种排序和筛选数据的方法：

1. 对数据库支持的模型使用类 SQL 操作（模拟）
2. 对内存中的数据使用 Python 操作

两种方法使用相同的 UI 组件：`rx.select` 用于排序，`rx.input` 用于筛选。

### 方法 1：数据库筛选和排序

对于数据库支持的模型，我们通常使用带有 `select`、`where` 和 `order_by` 的 SQL 查询。在此示例中，我们将使用模拟数据来模拟此行为。

```python demo exec
# 使用模拟数据模拟数据库操作
class DatabaseTableState(rx.State):
    # 模拟数据库记录的模拟数据
    users: list = [
        {
            "name": "John Doe",
            "email": "john@example.com",
            "phone": "555-1234",
            "address": "123 Main St",
        },
        {
            "name": "Jane Smith",
            "email": "jane@example.com",
            "phone": "555-5678",
            "address": "456 Oak Ave",
        },
        {
            "name": "Bob Johnson",
            "email": "bob@example.com",
            "phone": "555-9012",
            "address": "789 Pine Rd",
        },
        {
            "name": "Alice Brown",
            "email": "alice@example.com",
            "phone": "555-3456",
            "address": "321 Maple Dr",
        },
    ]
    filtered_users: list[dict] = []
    sort_value = ""
    search_value = ""

    @rx.event
    def load_entries(self):
        """Simulate querying the database with filter and sort."""
        # 从所有用户开始
        result = self.users.copy()

        # 如果存在搜索值则应用筛选
        if self.search_value != "":
            search_term = self.search_value.lower()
            result = [
                user
                for user in result
                if any(search_term in str(value).lower() for value in user.values())
            ]

        # 如果选择了排序列则应用排序
        if self.sort_value != "":
            result = sorted(result, key=lambda x: x[self.sort_value])

        self.filtered_users = result
        yield

    @rx.event
    def sort_values(self, sort_value):
        """Update sort value and reload data."""
        self.sort_value = sort_value
        yield DatabaseTableState.load_entries()

    @rx.event
    def filter_values(self, search_value):
        """Update search value and reload data."""
        self.search_value = search_value
        yield DatabaseTableState.load_entries()


def show_customer(user):
    """Show a customer in a table row."""
    return rx.table.row(
        rx.table.cell(user["name"]),
        rx.table.cell(user["email"]),
        rx.table.cell(user["phone"]),
        rx.table.cell(user["address"]),
    )


def database_table_example():
    return rx.vstack(
        rx.select(
            ["name", "email", "phone", "address"],
            placeholder="Sort By: Name",
            on_change=lambda value: DatabaseTableState.sort_values(value),
        ),
        rx.input(
            placeholder="Search here...",
            on_change=lambda value: DatabaseTableState.filter_values(value),
        ),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Phone"),
                    rx.table.column_header_cell("Address"),
                ),
            ),
            rx.table.body(rx.foreach(DatabaseTableState.filtered_users, show_customer)),
            on_mount=DatabaseTableState.load_entries,
            width="100%",
        ),
        width="100%",
    )
```

### 方法 2：内存中筛选和排序

对于内存中的数据，我们使用 `sorted()` 和列表推导式等 Python 操作。

状态变量 `_people` 被设置为仅后端变量。这样做是为了在变量非常大时减少网络流量并提高性能。

当选择 `select` 项时，`on_change` 事件触发器连接到 `set_sort_value` 事件处理器，该处理器更新 `sort_value` 状态变量。

`current_people` 是一个 `rx.var(cache=True)`。它是一个仅在其依赖的其他状态变量发生变化时才重新计算的 var。这确保了表格中显示的 `People` 在搜索或排序时始终是最新的。

```python demo exec
import dataclasses


@dataclasses.dataclass
class Person:
    full_name: str
    email: str
    group: str


class InMemoryTableState(rx.State):
    _people: list[Person] = [
        Person(full_name="Danilo Sousa", email="danilo@example.com", group="Developer"),
        Person(full_name="Zahra Ambessa", email="zahra@example.com", group="Admin"),
        Person(
            full_name="Jasper Eriks", email="zjasper@example.com", group="B-Developer"
        ),
    ]

    sort_value = ""
    search_value = ""

    @rx.event
    def set_sort_value(self, value: str):
        self.sort_value = value

    @rx.event
    def set_search_value(self, value: str):
        self.search_value = value

    @rx.var(cache=True)
    def current_people(self) -> list[Person]:
        people = self._people

        if self.sort_value != "":
            people = sorted(
                people, key=lambda user: getattr(user, self.sort_value).lower()
            )

        if self.search_value != "":
            people = [
                person
                for person in people
                if any(
                    self.search_value.lower() in getattr(person, attr).lower()
                    for attr in ["full_name", "email", "group"]
                )
            ]
        return people


def show_person(person: Person):
    """Show a person in a table row."""
    return rx.table.row(
        rx.table.cell(person.full_name),
        rx.table.cell(person.email),
        rx.table.cell(person.group),
    )


def in_memory_table_example():
    return rx.vstack(
        rx.select(
            ["full_name", "email", "group"],
            placeholder="Sort By: full_name",
            on_change=InMemoryTableState.set_sort_value,
        ),
        rx.input(
            placeholder="Search here...",
            on_change=InMemoryTableState.set_search_value,
        ),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Full name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Group"),
                ),
            ),
            rx.table.body(rx.foreach(InMemoryTableState.current_people, show_person)),
            width="100%",
        ),
        width="100%",
    )
```

### 何时使用哪种方法

- **数据库方法**：最适合大型数据集或数据已存在于数据库中的情况
- **内存方法**：最适合较小的数据集、原型开发，或数据是静态的或从 API 加载的情况

两种方法都提供相同的用户体验，具有筛选和排序功能。

## 数据库

构建 `rx.table` 更常见的用例是使用数据库中的数据。

下面的代码展示了如何从数据库加载数据并将其放入 `rx.table` 中。

## 将数据加载到表格中

定义了一个继承自 `rx.Model` 的 `Customer` [模型](/docs/database/tables)。

`load_entries` 事件处理器执行一个[查询](/docs/database/queries)，用于从数据库表中请求信息。此 `load_entries` 事件处理器在 `rx.table.root` 的 `on_mount` 事件触发器上被调用。

如果你想在应用页面加载时加载数据，可以将 `app.add_page()` 中的 `on_load` 设置为此事件处理器，例如 `app.add_page(page_name, on_load=State.load_entries)`。

```python
class Customer(rx.Model, table=True):
    """The customer model."""

    name: str
    email: str
    phone: str
    address: str
```

```python exec
class LoadingDataTableState(rx.State):
    users: list[dict] = []

    @rx.event
    def load_entries(self):
        """Get all users from the database."""
        customers_json = [
            {
                "name": "John Doe",
                "email": "john@example.com",
                "phone": "555-1234",
                "address": "123 Main St",
            },
            {
                "name": "Jane Smith",
                "email": "jane@example.com",
                "phone": "555-5678",
                "address": "456 Oak Ave",
            },
            {
                "name": "Bob Johnson",
                "email": "bob@example.com",
                "phone": "555-9012",
                "address": "789 Pine Blvd",
            },
            {
                "name": "Alice Williams",
                "email": "alice@example.com",
                "phone": "555-3456",
                "address": "321 Maple Dr",
            },
        ]
        self.users = customers_json


def show_customer(user: dict):
    return rx.table.row(
        rx.table.cell(user["name"]),
        rx.table.cell(user["email"]),
        rx.table.cell(user["phone"]),
        rx.table.cell(user["address"]),
    )


def loading_data_table_example():
    return rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Phone"),
                rx.table.column_header_cell("Address"),
            ),
        ),
        rx.table.body(rx.foreach(LoadingDataTableState.users, show_customer)),
        on_mount=LoadingDataTableState.load_entries,
        width="100%",
        margin_bottom="1em",
    )
```

```python eval
loading_data_table_example()
```

```python
from sqlmodel import select


class DatabaseTableState(rx.State):
    users: list[Customer] = []

    @rx.event
    def load_entries(self):
        """Get all users from the database."""
        with rx.session() as session:
            self.users = session.exec(select(Customer)).all()


def show_customer(user: Customer):
    """Show a customer in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.phone),
        rx.table.cell(user.address),
    )


def loading_data_table_example():
    return rx.table.root(
        rx.table.header(
            rx.table.row(
                rx.table.column_header_cell("Name"),
                rx.table.column_header_cell("Email"),
                rx.table.column_header_cell("Phone"),
                rx.table.column_header_cell("Address"),
            ),
        ),
        rx.table.body(rx.foreach(DatabaseTableState.users, show_customer)),
        on_mount=DatabaseTableState.load_entries,
        width="100%",
    )
```

## 筛选（搜索）和排序

在此示例中，我们对数据进行排序和筛选。

排序使用 `rx.select` 组件。数据根据 `Customer` 类的属性进行排序。当选择某个 select 项时，由于 `on_change` 事件触发器连接到 `sort_values` 事件处理器，数据会根据所选的状态变量 `sort_value` 属性进行排序。

排序查询根据状态变量 `sort_value` 获取 `sort_column`，使用 sql 的 `asc` 函数获取排序顺序，最后使用 `order_by` 函数。

筛选使用 `rx.input` 组件。数据根据输入到 `rx.input` 组件中的搜索查询进行筛选。当输入搜索查询时，由于 `on_change` 事件触发器连接到 `filter_values` 事件处理器，数据会根据状态变量 `search_value` 是否存在于该特定 `Customer` 的任何数据中进行筛选。

`search_value` 前后的 `%` 字符使其成为通配符模式，匹配 `search_value` 前后的任何字符序列。`query.where(...)` 修改现有查询以包含筛选条件。`or_` 运算符是逻辑 OR 运算符，用于组合多个条件。查询将返回匹配任何这些条件的结果。`Customer.name.ilike(search_value)` 检查 `Customer` 表的 `name` 列是否以不区分大小写的方式匹配 `search_value` 模式（`ilike` 代表"不区分大小写的 like"）。

```python
class Customer(rx.Model, table=True):
    """The customer model."""

    name: str
    email: str
    phone: str
    address: str
```

```python exec
class DatabaseTableState2(rx.State):
    # 模拟数据库记录的模拟数据
    _users: list[dict] = [
        {
            "name": "John Doe",
            "email": "john@example.com",
            "phone": "555-1234",
            "address": "123 Main St",
        },
        {
            "name": "Jane Smith",
            "email": "jane@example.com",
            "phone": "555-5678",
            "address": "456 Oak Ave",
        },
        {
            "name": "Bob Johnson",
            "email": "bob@example.com",
            "phone": "555-9012",
            "address": "789 Pine Rd",
        },
        {
            "name": "Alice Brown",
            "email": "alice@example.com",
            "phone": "555-3456",
            "address": "321 Maple Dr",
        },
        {
            "name": "Charlie Wilson",
            "email": "charlie@example.com",
            "phone": "555-7890",
            "address": "654 Elm St",
        },
        {
            "name": "Emily Davis",
            "email": "emily@example.com",
            "phone": "555-2345",
            "address": "987 Cedar Ln",
        },
    ]

    users: list[dict] = []
    sort_value = ""
    search_value = ""

    @rx.event
    def load_entries(self):
        # 从所有用户开始
        result = self._users.copy()

        # 如果存在搜索值则应用筛选
        if self.search_value != "":
            search_term = self.search_value.lower()
            result = [
                user
                for user in result
                if any(search_term in str(value).lower() for value in user.values())
            ]

        # 如果选择了排序列则应用排序
        if self.sort_value != "":
            result = sorted(result, key=lambda x: x[self.sort_value])

        self.users = result

    @rx.event
    def sort_values(self, sort_value):
        self.sort_value = sort_value
        yield DatabaseTableState2.load_entries()

    @rx.event
    def filter_values(self, search_value):
        self.search_value = search_value
        yield DatabaseTableState2.load_entries()


def show_customer_2(user: dict):
    return rx.table.row(
        rx.table.cell(user["name"]),
        rx.table.cell(user["email"]),
        rx.table.cell(user["phone"]),
        rx.table.cell(user["address"]),
    )


def loading_data_table_example_2():
    return rx.vstack(
        rx.select(
            ["name", "email", "phone", "address"],
            placeholder="Sort By: Name",
            on_change=lambda value: DatabaseTableState2.sort_values(value),
        ),
        rx.input(
            placeholder="Search here...",
            on_change=lambda value: DatabaseTableState2.filter_values(value),
        ),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Phone"),
                    rx.table.column_header_cell("Address"),
                ),
            ),
            rx.table.body(rx.foreach(DatabaseTableState2.users, show_customer_2)),
            on_mount=DatabaseTableState2.load_entries,
            width="100%",
        ),
        width="100%",
        margin_bottom="1em",
    )
```

```python eval
loading_data_table_example_2()
```

```python
from sqlmodel import select, asc, or_


class DatabaseTableState2(rx.State):
    users: list[Customer] = []

    sort_value = ""
    search_value = ""

    @rx.event
    def load_entries(self):
        """Get all users from the database."""
        with rx.session() as session:
            query = select(Customer)

            if self.search_value != "":
                search_value = self.search_value.lower()
                query = query.where(
                    or_(
                        Customer.name.ilike(search_value),
                        Customer.email.ilike(search_value),
                        Customer.phone.ilike(search_value),
                        Customer.address.ilike(search_value),
                    )
                )

            if self.sort_value != "":
                sort_column = getattr(Customer, self.sort_value)
                order = asc(sort_column)
                query = query.order_by(order)

            self.users = session.exec(query).all()

    @rx.event
    def sort_values(self, sort_value):
        print(sort_value)
        self.sort_value = sort_value
        self.load_entries()

    @rx.event
    def filter_values(self, search_value):
        print(search_value)
        self.search_value = search_value
        self.load_entries()


def show_customer(user: Customer):
    """Show a customer in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.phone),
        rx.table.cell(user.address),
    )


def loading_data_table_example2():
    return rx.vstack(
        rx.select(
            ["name", "email", "phone", "address"],
            placeholder="Sort By: Name",
            on_change=lambda value: DatabaseTableState2.sort_values(value),
        ),
        rx.input(
            placeholder="Search here...",
            on_change=lambda value: DatabaseTableState2.filter_values(value),
        ),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Phone"),
                    rx.table.column_header_cell("Address"),
                ),
            ),
            rx.table.body(rx.foreach(DatabaseTableState2.users, show_customer)),
            on_mount=DatabaseTableState2.load_entries,
            width="100%",
        ),
        width="100%",
    )
```

## 分页

分页是数据库管理的重要组成部分，尤其是在处理大型数据集时。它通过将结果分解为可管理的负载来实现高效的数据检索。

此代码的目的是根据指定的分页参数 `offset` 和 `limit` 从 `Customer` 表中检索特定的行子集。

`query.offset(self.offset)` 修改查询以在返回结果之前跳过一定数量的行。要跳过的行数由 `self.offset` 指定。

`query.limit(self.limit)` 修改查询以限制返回的行数。要返回的最大行数由 `self.limit` 指定。

```python exec
class DatabaseTableState3(rx.State):
    _mock_data: list[Customer] = [
        Customer(
            name="John Doe",
            email="john@example.com",
            phone="555-1234",
            address="123 Main St",
        ),
        Customer(
            name="Jane Smith",
            email="jane@example.com",
            phone="555-5678",
            address="456 Oak Ave",
        ),
        Customer(
            name="Bob Johnson",
            email="bob@example.com",
            phone="555-9012",
            address="789 Pine Rd",
        ),
        Customer(
            name="Alice Brown",
            email="alice@example.com",
            phone="555-3456",
            address="321 Maple Dr",
        ),
        Customer(
            name="Charlie Wilson",
            email="charlie@example.com",
            phone="555-7890",
            address="654 Elm St",
        ),
        Customer(
            name="Emily Davis",
            email="emily@example.com",
            phone="555-2345",
            address="987 Cedar Ln",
        ),
    ]
    users: list[Customer] = []

    total_items: int
    offset: int = 0
    limit: int = 3

    @rx.var(cache=True)
    def page_number(self) -> int:
        return (self.offset // self.limit) + 1 + (1 if self.offset % self.limit else 0)

    @rx.var(cache=True)
    def total_pages(self) -> int:
        return self.total_items // self.limit + (
            1 if self.total_items % self.limit else 0
        )

    @rx.event
    def prev_page(self):
        self.offset = max(self.offset - self.limit, 0)
        self.load_entries()

    @rx.event
    def next_page(self):
        if self.offset + self.limit < self.total_items:
            self.offset += self.limit
        self.load_entries()

    def _get_total_items(self, session):
        self.total_items = session.exec(select(func.count(Customer.id))).one()

    @rx.event
    def load_entries(self):
        self.users = self._mock_data[self.offset : self.offset + self.limit]
        self.total_items = len(self._mock_data)


def show_customer(user: Customer):
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.phone),
        rx.table.cell(user.address),
    )


def loading_data_table_example3():
    return rx.vstack(
        rx.hstack(
            rx.button(
                "Prev",
                on_click=DatabaseTableState3.prev_page,
            ),
            rx.text(
                f"Page {DatabaseTableState3.page_number} / {DatabaseTableState3.total_pages}"
            ),
            rx.button(
                "Next",
                on_click=DatabaseTableState3.next_page,
            ),
        ),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Phone"),
                    rx.table.column_header_cell("Address"),
                ),
            ),
            rx.table.body(rx.foreach(DatabaseTableState3.users, show_customer)),
            on_mount=DatabaseTableState3.load_entries,
            width="100%",
        ),
        width="100%",
        margin_bottom="1em",
    )
```

```python eval
loading_data_table_example3()
```

```python
from sqlmodel import select, func


class DatabaseTableState3(rx.State):
    users: list[Customer] = []

    total_items: int
    offset: int = 0
    limit: int = 3

    @rx.var(cache=True)
    def page_number(self) -> int:
        return (self.offset // self.limit) + 1 + (1 if self.offset % self.limit else 0)

    @rx.var(cache=True)
    def total_pages(self) -> int:
        return self.total_items // self.limit + (
            1 if self.total_items % self.limit else 0
        )

    @rx.event
    def prev_page(self):
        self.offset = max(self.offset - self.limit, 0)
        self.load_entries()

    @rx.event
    def next_page(self):
        if self.offset + self.limit < self.total_items:
            self.offset += self.limit
        self.load_entries()

    def _get_total_items(self, session):
        """Return the total number of items in the Customer table."""
        self.total_items = session.exec(select(func.count(Customer.id))).one()

    @rx.event
    def load_entries(self):
        """Get all users from the database."""
        with rx.session() as session:
            query = select(Customer)

            # 应用分页
            query = query.offset(self.offset).limit(self.limit)

            self.users = session.exec(query).all()
            self._get_total_items(session)


def show_customer(user: Customer):
    """Show a customer in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.phone),
        rx.table.cell(user.address),
    )


def loading_data_table_example3():
    return rx.vstack(
        rx.hstack(
            rx.button(
                "Prev",
                on_click=DatabaseTableState3.prev_page,
            ),
            rx.text(
                f"Page {DatabaseTableState3.page_number} / {DatabaseTableState3.total_pages}"
            ),
            rx.button(
                "Next",
                on_click=DatabaseTableState3.next_page,
            ),
        ),
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Phone"),
                    rx.table.column_header_cell("Address"),
                ),
            ),
            rx.table.body(rx.foreach(DatabaseTableState3.users, show_customer)),
            on_mount=DatabaseTableState3.load_entries,
            width="100%",
        ),
        width="100%",
    )
```

## 更高级的示例

`rx.table` 的真正强大之处在于你可以在应用中实时可视化、添加和编辑数据。查看这些应用和代码以了解如何实现：应用：https://customer-data-app.reflex.run 代码：https://github.com/reflex-dev/templates/tree/main/customer_data_app 以及代码：https://github.com/reflex-dev/templates/tree/main/sales。

## 下载

大多数用户在获得表格中所需的子集后会想要下载数据。

在此示例中，有按钮可以将数据下载为 `json` 和 `csv` 格式。

对于 `json` 下载，`rx.download` 位于前端代码中，附加到按钮的 `on_click` 事件触发器上。这之所以有效，是因为如果 `Var` 还不是字符串，它将使用 `JSON.stringify` 转换为字符串。

对于 `csv` 下载，`rx.download` 位于后端代码中，作为事件处理器 `download_csv_data`。还有一个辅助函数 `_convert_to_csv`，将 `self.users` 中的数据转换为 `csv` 格式。

```python exec
import io
import csv
import json


class TableDownloadState(rx.State):
    _mock_data: list[Customer] = [
        Customer(
            name="John Doe",
            email="john@example.com",
            phone="555-1234",
            address="123 Main St",
        ),
        Customer(
            name="Jane Smith",
            email="jane@example.com",
            phone="555-5678",
            address="456 Oak Ave",
        ),
        Customer(
            name="Bob Johnson",
            email="bob@example.com",
            phone="555-9012",
            address="789 Pine Rd",
        ),
    ]
    users: list[Customer] = []

    @rx.event
    def load_entries(self):
        self.users = self._mock_data

    def _convert_to_csv(self) -> str:
        if not self.users:
            self.load_entries()

        fieldnames = ["id", "name", "email", "phone", "address"]
        output = io.StringIO()
        writer = csv.DictWriter(output, fieldnames=fieldnames)
        writer.writeheader()
        for user in self.users:
            writer.writerow(user.dict())

        csv_data = output.getvalue()
        output.close()
        return csv_data

    def _convert_to_json(self) -> str:
        return json.dumps([u.dict() for u in self._mock_data], indent=2)

    @rx.event
    def download_csv_data(self):
        csv_data = self._convert_to_csv()
        return rx.download(
            data=csv_data,
            filename="data.csv",
        )

    @rx.event
    def download_json_data(self):
        json_data = self._convert_to_json()
        return rx.download(
            data=json_data,
            filename="data.json",
        )


def show_customer(user: Customer):
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.phone),
        rx.table.cell(user.address),
    )


def download_data_table_example():
    return rx.vstack(
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Phone"),
                    rx.table.column_header_cell("Address"),
                ),
            ),
            rx.table.body(rx.foreach(TableDownloadState.users, show_customer)),
            width="100%",
            on_mount=TableDownloadState.load_entries,
        ),
        rx.hstack(
            rx.button(
                "Download as JSON",
                on_click=TableDownloadState.download_json_data,
            ),
            rx.button(
                "Download as CSV",
                on_click=TableDownloadState.download_csv_data,
            ),
            spacing="7",
        ),
        width="100%",
        spacing="5",
        margin_bottom="1em",
    )
```

```python eval
download_data_table_example()
```

```python
import io
import csv
from sqlmodel import select


class TableDownloadState(rx.State):
    users: list[Customer] = []

    @rx.event
    def load_entries(self):
        """Get all users from the database."""
        with rx.session() as session:
            self.users = session.exec(select(Customer)).all()

    def _convert_to_csv(self) -> str:
        """Convert the users data to CSV format."""

        # 确保先加载数据
        if not self.users:
            self.load_entries()

        # 根据 Customer 模型的属性定义 CSV 文件头
        fieldnames = list(Customer.__fields__)

        # 创建字符串缓冲区来保存 CSV 数据
        output = io.StringIO()
        writer = csv.DictWriter(output, fieldnames=fieldnames)
        writer.writeheader()
        for user in self.users:
            writer.writerow(user.dict())

        # 获取 CSV 数据字符串
        csv_data = output.getvalue()
        output.close()
        return csv_data

    @rx.event
    def download_csv_data(self):
        csv_data = self._convert_to_csv()
        return rx.download(
            data=csv_data,
            filename="data.csv",
        )


def show_customer(user: Customer):
    """Show a customer in a table row."""
    return rx.table.row(
        rx.table.cell(user.name),
        rx.table.cell(user.email),
        rx.table.cell(user.phone),
        rx.table.cell(user.address),
    )


def download_data_table_example():
    return rx.vstack(
        rx.table.root(
            rx.table.header(
                rx.table.row(
                    rx.table.column_header_cell("Name"),
                    rx.table.column_header_cell("Email"),
                    rx.table.column_header_cell("Phone"),
                    rx.table.column_header_cell("Address"),
                ),
            ),
            rx.table.body(rx.foreach(TableDownloadState.users, show_customer)),
            width="100%",
            on_mount=TableDownloadState.load_entries,
        ),
        rx.hstack(
            rx.button(
                "Download as JSON",
                on_click=rx.download(
                    data=TableDownloadState.users,
                    filename="data.json",
                ),
            ),
            rx.button(
                "Download as CSV",
                on_click=TableDownloadState.download_csv_data,
            ),
            spacing="7",
        ),
        width="100%",
        spacing="5",
    )
```

## 真实场景 UI 示例

```python demo
rx.flex(
    rx.heading("Your Team", as_="h2"),
    rx.text("Invite and manage your team members"),
    rx.flex(
        rx.input(placeholder="Email Address"),
        rx.button("Invite"),
        justify="center",
        spacing="2",
    ),
    rx.table.root(
        rx.table.body(
            rx.table.row(
                rx.table.cell(rx.avatar(fallback="DS")),
                rx.table.row_header_cell(rx.link("Danilo Sousa")),
                rx.table.cell("danilo@example.com"),
                rx.table.cell("Developer"),
                align="center",
            ),
            rx.table.row(
                rx.table.cell(rx.avatar(fallback="ZA")),
                rx.table.row_header_cell(rx.link("Zahra Ambessa")),
                rx.table.cell("zahra@example.com"),
                rx.table.cell("Admin"),
                align="center",
            ),
            rx.table.row(
                rx.table.cell(rx.avatar(fallback="JE")),
                rx.table.row_header_cell(rx.link("Jasper Eriksson")),
                rx.table.cell("jasper@example.com"),
                rx.table.cell("Developer"),
                align="center",
            ),
        ),
        width="100%",
    ),
    width="100%",
    direction="column",
    spacing="2",
)
```

## 相关内容

探索在 Reflex 中处理表格数据的其他方法，全部使用纯 Python：

- [Data Table](/docs/library/tables-and-data-grids/data-table)
- [Data Editor](/docs/library/tables-and-data-grids/data-editor)
- [表格和数据网格](/docs/library/tables-and-data-grids/)
