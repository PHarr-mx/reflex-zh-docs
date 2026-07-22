# 数据库（Database）

Reflex 使用 [sqlmodel](https://sqlmodel.tiangolo.com) 提供一个封装 SQLAlchemy 的内置 ORM。

本页面上的示例特别涉及 Reflex 如何使用各种工具
暴露集成的数据库接口。下面只涵盖基本用例，
但你可以参考
[sqlmodel 教程](https://sqlmodel.tiangolo.com/tutorial/select/)
获取更多示例和信息，只需将 `SQLModel` 替换为 `rx.Model`，将
`Session(engine)` 替换为 `rx.session()`

对于高级用例，请参阅
[SQLAlchemy 文档](https://docs.sqlalchemy.org/en/14/orm/quickstart.html)（v1.4）。

```md alert info
# 使用 NoSQL 数据库

如果你使用的是 NoSQL 数据库（例如 MongoDB），你可以通过安装适当的 Python 客户端库在 Reflex 中使用它。在这种情况下，Reflex 不会提供任何 ORM 功能。
```

## 安装

ORM 依赖项（SQLModel 和 Alembic）是可选的额外项。
使用 `db` 额外项安装它们：

```bash
pip install "reflex[db]"
```

## 连接

Reflex 提供一个内置的 SQLite 数据库用于存储和检索数据。

你可以通过修改 `rxconfig.py` 文件中的数据库 URL 来连接自己的 SQL 兼容数据库。

```python
config = rx.Config(
    app_name="my_app",
    db_url="sqlite:///reflex.db",
)
```

有关可以使用的更多数据库 URL 示例，请参阅 [SQLAlchemy
文档](https://docs.sqlalchemy.org/en/14/core/engines.html#backend-specific-urls)。
确保为你打算使用的数据库安装适当的 DBAPI 驱动程序。

## 表

要创建表，创建一个继承自 `rx.Model` 的类并指定
它是一个表。

```python
class User(rx.Model, table=True):
    username: str
    email: str
    password: str
```

## 迁移

Reflex 利用 [alembic](https://alembic.sqlalchemy.org/en/latest/)
来管理数据库架构更改。

在新应用中可以使用数据库功能之前，你必须调用 `reflex db init`
来初始化 alembic 并使用当前架构创建迁移脚本。

在对架构进行更改后，使用
`reflex db makemigrations --message 'something changed'`
在 `alembic/versions` 目录中生成一个脚本，该脚本将更新
数据库架构。建议在应用之前检查生成的脚本。

请记住，除非在应用程序中的某处导入并使用，否则 `reflex db makemigrations`
命令不会检测到你的最新模型。

`reflex db migrate` 命令用于应用迁移脚本以使
数据库保持最新。在应用启动期间，如果 Reflex 检测到当前
数据库架构不是最新的，将在控制台上显示警告。

## 查询

要查询数据库，你可以创建一个 `rx.session()`
它处理打开和关闭数据库连接。

你可以使用正常的 SQLAlchemy 查询来查询数据库。

```python
with rx.session() as session:
    session.add(User(username="test", email="admin@reflex.dev", password="admin"))
    session.commit()
```

```md video https://youtube.com/embed/ITOZkzjtjUA?start=6835&end=8225
# 视频：数据库模型与表单、模型字段更改和迁移以及添加 DateTime 字段的教程
```
