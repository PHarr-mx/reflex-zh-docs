# 关系（Relationships）

外键关系用于将两个表链接在一起。例如，
`Post` 模型可能有一个字段 `user_id`，其外键为 `user.id`，
引用 `User` 模型。这将允许我们自动查询与用户关联的 `Post` 对象，
或找到与 `Post` 关联的 `User` 对象。

要建立双向关系，模型必须正确设置
`Relationship` 上的 `back_populates` 关键字参数，指向_另一个_模型中的关系
属性。

## 外键关系

要创建关系，首先向模型添加一个引用
相关表主键的字段，然后添加一个 `sqlmodel.Relationship` 属性，
可用于访问相关对象。

像这样定义关系需要使用 `sqlmodel` 对象，
如示例所示。

```python
from typing import List, Optional

import sqlmodel

import reflex as rx


class Post(rx.Model, table=True):
    title: str
    body: str
    user_id: int = sqlmodel.Field(foreign_key="user.id")

    user: Optional["User"] = sqlmodel.Relationship(back_populates="posts")
    flags: Optional[List["Flag"]] = sqlmodel.Relationship(back_populates="post")


class User(rx.Model, table=True):
    username: str
    email: str

    posts: List[Post] = sqlmodel.Relationship(back_populates="user")
    flags: List["Flag"] = sqlmodel.Relationship(back_populates="user")


class Flag(rx.Model, table=True):
    post_id: int = sqlmodel.Field(foreign_key="post.id")
    user_id: int = sqlmodel.Field(foreign_key="user.id")
    message: str

    post: Optional[Post] = sqlmodel.Relationship(back_populates="flags")
    user: Optional[User] = sqlmodel.Relationship(back_populates="flags")
```

有关更多详细信息，请参阅 [SQLModel 关系文档](https://sqlmodel.tiangolo.com/tutorial/relationship-attributes/define-relationships-attributes/)。

## 查询关系

### 插入链接对象

以下示例假设标记用户作为
`User` 实例存储在状态中，并且帖子 `id` 在表单中提交的数据中提供。

```python
class FlagPostForm(rx.State):
    user: User

    @rx.event
    def flag_post(self, form_data: dict[str, Any]):
        with rx.session() as session:
            post = session.get(Post, int(form_data.pop("post_id")))
            flag = Flag(message=form_data.pop("message"), post=post, user=self.user)
            session.add(flag)
            session.commit()
```

### 关系如何解引用？

默认情况下，关系属性处于**延迟加载**或 `"select"`
模式，它在_访问_关系属性时生成查询。延迟
加载通常适用于单个对象查找和操作，但在
访问许多链接对象以进行序列化时可能效率低下。

有几种可用的替代加载机制，可以在
关系对象上或执行查询时设置。

- "joined" 或 `joinload` - 生成单个查询以一次加载所有相关对象。
- "subquery" 或 `subqueryload` - 生成单个查询以一次加载所有相关
  对象，但使用子查询进行连接，而不是在主查询中进行连接。
- "selectin" 或 `selectinload` - 发出第二个（或更多）SELECT 语句，
  将父对象的主键标识符组装到 IN 子句中，
  以便所有相关集合/标量引用的成员都通过主键一次加载

还有非加载机制 "raise" 和 "noload"，用于
特别避免加载关系。

每种加载方法都有权衡，有些更适合不同的
数据访问模式。
有关更多详细信息，请参阅 [SQLAlchemy: 关系加载技术](https://docs.sqlalchemy.org/en/14/orm/loading_relationships.html)。

### 查询链接对象

要查询 `Post` 表并预先包含所有 `User` 和 `Flag` 对象，
将使用 `.options` 接口为所需的关系指定 `selectinload`。使用此方法，链接的对象将可用于
在前端代码中渲染，无需额外步骤。

```python
import sqlalchemy


class PostState(rx.State):
    posts: List[Post]

    @rx.event
    def load_posts(self):
        with rx.session() as session:
            self.posts = session.exec(
                Post.select.options(
                    sqlalchemy.orm.selectinload(Post.user),
                    sqlalchemy.orm.selectinload(Post.flags).options(
                        sqlalchemy.orm.selectinload(Flag.user),
                    ),
                ).limit(15)
            ).all()
```

加载方法创建新的查询对象，因此如果
关系本身有需要加载的其他关系，则可以链接。在本
示例中，由于 `Flag` 引用 `User`，`Flag.user` 关系必须从 `Post.flags` 关系链式加载。

### 在关系上指定加载机制

或者，可以通过向 `sqlmodel.Relationship` 传递 `sa_relationship_kwargs={"lazy": method}` 在关系上指定加载机制，
这将默认在所有查询中使用给定的加载机制。

```python
from typing import List, Optional

import sqlmodel

import reflex as rx


class Post(rx.Model, table=True):
    ...
    user: Optional["User"] = sqlmodel.Relationship(
        back_populates="posts",
        sa_relationship_kwargs={"lazy": "selectin"},
    )
    flags: Optional[List["Flag"]] = sqlmodel.Relationship(
        back_populates="post",
        sa_relationship_kwargs={"lazy": "selectin"},
    )
```
