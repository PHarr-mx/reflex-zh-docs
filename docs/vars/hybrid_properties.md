```python exec
import reflex as rx
from reflex.experimental import hybrid_property
```

# 混合属性（Hybrid Properties）

混合属性使用**一个同时在后端和前端工作的方法**从其他变量派生值。它通过 `hybrid_property` 装饰器定义，目前作为实验性功能提供：

```python
from reflex.experimental import hybrid_property  # also exposed as rx._x.hybrid_property
```

当你在 UI 中引用混合属性时，Reflex 会将其编译为基于它所读取的变量构建的**客户端变量表达式** —— 不会创建或发送额外的数据。同样的方法在后端也能工作，在那里它的行为类似于普通的 Python 属性。

尝试在下面的输入框中输入 —— `full_name` 会在客户端实时更新：

```python demo exec id=hybrid_full_name
class NameState(rx.State):
    first_name: str = "Jane"
    last_name: str = "Doe"

    @rx.event
    def set_first_name(self, value: str):
        self.first_name = value

    @rx.event
    def set_last_name(self, value: str):
        self.last_name = value

    @hybrid_property
    def full_name(self) -> str:
        return f"{self.first_name} {self.last_name}"


def hybrid_full_name_example():
    return rx.vstack(
        rx.heading(NameState.full_name),
        rx.input(value=NameState.first_name, on_change=NameState.set_first_name),
        rx.input(value=NameState.last_name, on_change=NameState.set_last_name),
    )
```

这里 `full_name` 直接在浏览器中渲染为 `first_name + " " + last_name`。因为它是属性，**同样的**方法在后端也可用 —— 在事件处理器内部，`self.full_name` 返回实际的组合字符串。

## 混合属性 vs. 计算变量

[计算变量](/docs/vars/computed-vars)和混合属性都从其他状态变量派生值，但它们的运作方式截然不同：

| | 计算变量（`@rx.var`） | 混合属性（`hybrid_property`） |
| --- | --- | --- |
| 生成值的位置 | 在**服务器**上 | 渲染时在**客户端**（在后端访问时则在服务器） |
| 发送到浏览器的数据 | 结果被**缓存并作为状态的额外字段发送** | **不需要额外数据** —— 编译为基于现有变量的客户端表达式 |
| 最适合用于 | 只有服务器才能生成的值：数据库查询、秘密数据、重型或 `async` 工作 | **重新格式化已在客户端的数据**：组合字段、构建标签、格式化 |

计算变量实际上会**复制**数据：Reflex 在服务器上计算值，将其存储在状态中，发送到浏览器，并保持同步。当值依赖于只有服务器拥有的数据时，这正是你想要的。

混合属性**不添加**额外的状态。上面的 `NameState.full_name` 渲染为基于已经存在于客户端的 `first_name` 和 `last_name` 的表达式，因此没有额外需要存储、缓存或传输的内容。当你只需要**重新塑造现有的前端数据**以进行显示时，使用混合属性；当值只能在服务器上生成时，使用计算变量。

## 独立的前端实现

默认情况下，混合属性在前端和后端**重用相同的代码**。当两者应该不同时，使用 `@<name>.var` 注册仅前端的实现。被装饰的函数接收状态类并返回一个 [Var](/docs/vars/base-vars)：

```python demo exec id=hybrid_greeting
class GreetState(rx.State):
    name: str = ""

    @rx.event
    def set_name(self, value: str):
        self.name = value

    @hybrid_property
    def greeting(self) -> str:
        # Backend (plain Python) implementation.
        return f"Hello, {self.name}!" if self.name else "Hello!"

    @greeting.var
    def greeting(cls) -> rx.Var[str]:
        # Frontend (Var) implementation.
        return rx.cond(cls.name, f"Hello, {cls.name}!", "Hello!")


def hybrid_greeting_example():
    return rx.vstack(
        rx.heading(GreetState.greeting),
        rx.input(value=GreetState.name, on_change=GreetState.set_name),
    )
```

由于前端表达式仅从到达客户端的数据构建，混合属性的前端逻辑可以引用常规变量，但**不能引用仅后端变量**（那些以 `_` 为前缀的变量）。在构建前端值时读取后端变量会引发错误 —— 请改用计算变量在服务器上生成此类值。

## 嵌套对象

在用作变量的数据类、Pydantic 模型或 SQLAlchemy 模型上定义的混合属性也能正常工作。通过对象变量访问属性时，会像直接在状态上访问一样渲染：

```python
from dataclasses import dataclass


@dataclass
class Info:
    first_name: str
    last_name: str

    @hybrid_property
    def full_name(self) -> str:
        return f"{self.first_name} {self.last_name}"


class ProfileState(rx.State):
    info: Info = Info(first_name="Jane", last_name="Doe")


# Renders as `info.first_name + " " + info.last_name` on the client:
rx.text(ProfileState.info.full_name)
```
