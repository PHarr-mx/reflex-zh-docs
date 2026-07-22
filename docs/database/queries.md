# 查询（Queries）

查询用于从数据库中检索数据。

查询是从数据库表或表组合中请求信息。查询可用于从单个表或多个表中检索数据。查询也可用于向表中插入、更新或删除数据。

## 会话（Session）

要执行查询，你必须首先创建一个 `rx.session`。你可以使用会话
使用 SQLModel 或 SQLAlchemy 语法查询数据库。

`rx.session` 语句将在代码块完成时自动关闭会话。**如果未调用 `session.commit()`，更改将被回滚且不会持久化到数据库。**代码也可以通过 `session.rollback()` 显式回滚而不关闭会话。

以下示例展示如何创建会话并查询数据库。
首先我们创建一个名为 `User` 的表。

```python
class User(rx.Model, table=True):
    username: str
    email: str
```

### 选择（Select）

然后我们创建一个会话并查询 User 表。

```python
class QueryUser(rx.State):
    name: str
    users: list[User]

    @rx.event
    def get_users(self):
        with rx.session() as session:
            self.users = session.exec(
                User.select().where(User.username.contains(self.name))
            ).all()
```

`get_users` 方法将查询数据库以获取所有包含状态变量 `name` 值的用户。

### 插入（Insert）

类似地，`session.add()` 方法向数据库添加新记录或持久化现有对象。

```python
class AddUser(rx.State):
    username: str
    email: str

    @rx.event
    def add_user(self):
        with rx.session() as session:
            session.add(User(username=self.username, email=self.email))
            session.commit()
```

### 更新（Update）

要更新用户，首先查询数据库以获取对象，进行所需的
修改，将对象 `.add` 到会话，最后调用 `.commit()`。

```python
class ChangeEmail(rx.State):
    username: str
    email: str

    @rx.event
    def modify_user(self):
        with rx.session() as session:
            user = session.exec(
                User.select().where((User.username == self.username))
            ).first()
            user.email = self.email
            session.add(user)
            session.commit()
```

### 删除（Delete）

要删除用户，首先查询数据库以获取对象，然后在会话上调用
`.delete()`，最后调用 `.commit()`。

```python
class RemoveUser(rx.State):
    username: str

    @rx.event
    def delete_user(self):
        with rx.session() as session:
            user = session.exec(
                User.select().where(User.username == self.username)
            ).first()
            session.delete(user)
            session.commit()
```

## ORM 对象生命周期

查询返回的对象绑定到创建它们的会话，通常不能
在该会话之外使用。添加或更新对象后，并非所有字段都会自动
更新，因此访问某些属性可能会触发额外的查询来刷新对象。

为避免这种情况，可以使用 `session.refresh()` 方法显式更新对象，并
确保在退出会话之前所有字段都是最新的。

```python
class AddUserForm(rx.State):
    user: User | None = None

    @rx.event
    def add_user(self, form_data: dict[str, Any]):
        with rx.session() as session:
            self.user = User(**form_data)
            session.add(self.user)
            session.commit()
            session.refresh(self.user)
```

现在 `self.user` 对象将具有对自动生成的
主键 `id` 的正确引用，即使在从表单数据创建对象时未提供它。

如果 `self.user` 需要在新会话中修改或用于另一个查询，
必须将其添加到会话中。将对象添加到会话不一定
创建对象，而是将其与会话关联，在那里它
可能会被相应地创建或更新。

```python
class AddUserForm(rx.State):
    ...

    @rx.event
    def update_user(self, form_data: dict[str, Any]):
        if self.user is None:
            return
        with rx.session() as session:
            self.user.set(**form_data)
            session.add(self.user)
            session.commit()
            session.refresh(self.user)
```

如果 ORM 对象将在会话之外被引用和访问，你
应该对其调用 `.refresh()` 以避免过时的对象异常。

## 直接使用 SQL

避免 SQL 是使用 ORM 的主要好处之一，但有时对于
特别复杂的查询或使用数据库特定功能时是必要的。

SQLModel 暴露了 `session.execute()` 方法，可用于执行原始
SQL 字符串。如果需要参数绑定，查询可以包装在
[`sqlalchemy.text`](https://docs.sqlalchemy.org/en/14/core/sqlelement.html#sqlalchemy.sql.expression.text) 中，
它允许使用冒号前缀名称作为占位符。

```md alert info
# 切勿使用字符串格式化来构造 SQL 查询，因为这可能导致应用中的 SQL 注入漏洞。
```

```python
import sqlalchemy

import reflex as rx


class State(rx.State):
    @rx.event
    def insert_user_raw(self, username, email):
        with rx.session() as session:
            session.execute(
                sqlalchemy.text(
                    "INSERT INTO user (username, email) VALUES (:username, :email)"
                ),
                {"username": username, "email": email},
            )
            session.commit()

    @rx.var
    def raw_user_tuples(self) -> list[list]:
        with rx.session() as session:
            return [list(row) for row in session.execute("SELECT * FROM user").all()]
```

## 异步数据库操作

Reflex 提供了一个名为 `rx.asession` 的会话函数的异步版本，用于异步数据库操作。当你需要在异步上下文中执行数据库操作时（例如在异步事件处理程序中），这非常有用。

### 配置异步数据库 URL

`rx.asession` 需要在 `rxconfig.py` 中有自己的 `async_db_url`。它必须指向
与 `db_url` 相同的数据库，但使用异步驱动程序。在未设置时调用 `rx.asession()`
会引发错误。

```python
config = rx.Config(
    app_name="my_app",
    db_url="sqlite:///reflex.db",
    async_db_url="sqlite+aiosqlite:///reflex.db",
)
```

匹配的异步 DBAPI 驱动程序不是 `reflex[db]` 额外项的一部分，因此
需要单独安装。对于上面的 SQLite URL，安装 `aiosqlite`：

```bash
pip install aiosqlite
```

对于 PostgreSQL，安装 `psycopg`（psycopg3）。它既可以作为同步也可以作为
异步驱动程序，因此 `db_url` 和 `async_db_url` 可以共享一个
`postgresql+psycopg://...` 方案：

```bash
pip install "psycopg[binary]"
```

`rx.asession` 函数返回一个异步 SQLAlchemy 会话，必须与异步上下文管理器一起使用。对 `asession` 的大多数操作必须被等待（awaited）。

```python
import sqlalchemy.ext.asyncio
import sqlalchemy

import reflex as rx


class AsyncUserState(rx.State):
    users: list[User] = []

    @rx.event(background=True)
    async def get_users_async(self):
        async with rx.asession() as asession:
            result = await asession.execute(User.select())
            async with self:
                self.users = result.all()
```

### 异步选择

以下示例展示如何异步查询数据库：

```python
class AsyncQueryUser(rx.State):
    name: str
    users: list[User] = []

    @rx.event(background=True)
    async def get_users(self):
        async with rx.asession() as asession:
            stmt = User.select().where(User.username.contains(self.name))
            result = await asession.execute(stmt)
            async with self:
                self.users = result.all()
```

### 异步插入

要异步向数据库添加新记录：

```python
class AsyncAddUser(rx.State):
    username: str
    email: str

    @rx.event(background=True)
    async def add_user(self):
        async with rx.asession() as asession:
            asession.add(User(username=self.username, email=self.email))
            await asession.commit()
```

### 异步更新

要异步更新用户：

```python
class AsyncChangeEmail(rx.State):
    username: str
    email: str

    @rx.event(background=True)
    async def modify_user(self):
        async with rx.asession() as asession:
            stmt = User.select().where(User.username == self.username)
            result = await asession.execute(stmt)
            user = result.first()
            if user:
                user.email = self.email
                asession.add(user)
                await asession.commit()
```

### 异步删除

要异步删除用户：

```python
class AsyncRemoveUser(rx.State):
    username: str

    @rx.event(background=True)
    async def delete_user(self):
        async with rx.asession() as asession:
            stmt = User.select().where(User.username == self.username)
            result = await asession.execute(stmt)
            user = result.first()
            if user:
                await asession.delete(user)
                await asession.commit()
```

### 异步刷新

与常规会话类似，你可以刷新对象以确保所有字段都是最新的：

```python
class AsyncAddUserForm(rx.State):
    user: User | None = None

    @rx.event(background=True)
    async def add_user(self, form_data: dict[str, str]):
        async with rx.asession() as asession:
            async with self:
                self.user = User(**form_data)
            asession.add(self.user)
            await asession.commit()
            await asession.refresh(self.user)
```

### 异步 SQL 执行

你也可以异步执行原始 SQL：

```python
class AsyncRawSQL(rx.State):
    users: list[list] = []

    @rx.event(background=True)
    async def insert_user_raw(self, username, email):
        async with rx.asession() as asession:
            await asession.execute(
                sqlalchemy.text(
                    "INSERT INTO user (username, email) VALUES (:username, :email)"
                ),
                dict(username=username, email=email),
            )
            await asession.commit()

    @rx.event(background=True)
    async def get_raw_users(self):
        async with rx.asession() as asession:
            result = await asession.execute("SELECT * FROM user")
            async with self:
                self.users = [list(row) for row in result.all()]
```

```md alert info
# 异步数据库操作的重要说明

- 始终对异步事件处理程序使用 `@rx.event(background=True)` 装饰器
- 对 `asession` 的大多数操作必须被等待，包括 `commit()`、`execute()`、`refresh()` 和 `delete()`
- `add()` 方法不需要被等待
- 查询的结果对象具有 `all()` 和 `first()` 等方法，这些方法是同步的并直接返回数据
- 在后台任务中更新状态变量时使用 `async with self:`
```
