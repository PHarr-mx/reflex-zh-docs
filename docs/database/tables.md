# 表（Tables）

表是包含数据库中所有数据的数据库对象。

在表中，数据以类似于电子表格的行和列格式进行逻辑组织。每行代表一条唯一的记录，每列代表记录中的一个字段。

## 创建表

要创建表，创建一个继承自 `rx.Model` 的类。

以下示例展示如何创建一个名为 `User` 的表。

```python
class User(rx.Model, table=True):
    username: str
    email: str
```

`table=True` 参数告诉 Reflex 为这个类在数据库中创建一个表。

### 主键

默认情况下，Reflex 会为每个表创建一个名为 `id` 的主键列。

但是，如果 `rx.Model` 定义了一个带有 `primary_key=True` 的不同字段，则不会创建默认的 `id` 字段。表也可以根据需要重新定义 `id`。

目前无法创建没有主键的表。

## 高级列类型

SQLModel 自动将基本 Python 类型映射到 SQLAlchemy 列类型，但
对于更高级的用例，可以直接使用 `sqlalchemy` 定义列类型。例如，我们可以向
post 示例添加一个最后更新时间戳，作为带有时间区的适当 `DateTime` 字段。

```python
import datetime

import sqlmodel
import sqlalchemy


class Post(rx.Model, table=True):
    ...
    update_ts: datetime.datetime = sqlmodel.Field(
        default=None,
        sa_column=sqlalchemy.Column(
            "update_ts",
            sqlalchemy.DateTime(timezone=True),
            server_default=sqlalchemy.func.now(),
        ),
    )
```

为了使 `Post` 模型在前端更可用，可以提供一个 `dict` 方法
将任何字段转换为 JSON 可序列化的值。在本例中，dict 方法
覆盖默认的 `datetime` 序列化器以去掉微秒部分。

```python
class Post(rx.Model, table=True):
    ...

    def dict(self, *args, **kwargs) -> dict:
        d = super().dict(*args, **kwargs)
        d["update_ts"] = self.update_ts.replace(microsecond=0).isoformat()
        return d
```
