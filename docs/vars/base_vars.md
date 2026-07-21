```python exec
import random
import time

import reflex as rx
```

# 基本变量（Base Vars）

变量（Vars）是应用中可能随时间变化的任何字段。Var 被直接渲染到应用的前端。

基本变量（Base vars）在 State 类中定义为字段。

它们可以设置预设的默认值。如果你没有提供默认值，则必须提供类型注解。

```md alert warning
# 状态变量应提供类型注解。

Reflex 依赖类型注解来确定状态变量在编译过程中的类型。
```

```python demo exec
class TickerState(rx.State):
    ticker: str = "AAPL"
    price: str = "$150"


def ticker_example():
    return rx.center(
        rx.vstack(
            rx.heading(TickerState.ticker, as_="h2", size="3"),
            rx.text(f"Current Price: {TickerState.price}", font_size="md"),
            rx.text("Change: 4%", color="green"),
        ),
    )
```

在这个示例中，`ticker` 和 `price` 是应用中的基本变量，可以在运行时修改。

```md alert warning
# Vars 必须是 JSON 可序列化的。

Vars 用于在前端和后端之间通信。它们必须是 Python 基本类型、Plotly 图形、Pandas 数据框（dataframes）或[自定义定义的类型](/docs/vars/custom-vars)。
```

## 在不同页面上访问状态变量

State 只是一个 Python 类，因此可以在一个页面中定义，然后导入并在另一个页面中使用。下面我们在 `state.py` 页面中定义 `TickerState` 类，然后在 `index.py` 页面中导入并使用它。

```python
# state.py


class TickerState(rx.State):
    ticker: str = "AAPL"
    price: str = "$150"
```

```python
# index.py
from .state import TickerState


def ticker_example():
    return rx.center(
        rx.vstack(
            rx.heading(TickerState.ticker, as_="h2", size="3"),
            rx.text(f"Current Price: {TickerState.price}", font_size="md"),
            rx.text("Change: 4%", color="green"),
        ),
    )
```

## 仅后端变量（Backend-only Vars）

State 类中以 `_` 下划线开头的任何 Var 都被视为仅后端变量，并且**不会同步到前端**。与特定会话关联但**不直接在前端渲染的数据应存储在仅后端变量中**，以减少网络流量并提高性能。

它们的优点是无需 JSON 可序列化，但它们仍然必须是可 pickle 的，以便在生产模式下与 Redis 一起使用。它们不能直接在前端渲染，**可用于存储不应发送到客户端的敏感值**。

```md alert warning
# 在仅后端变量中保护认证数据和敏感状态。

常规变量和计算变量会同步到前端，以便在 UI 中渲染。放在常规变量中的任何值都会被序列化并发送到客户端，检查页面或网络流量的任何人都可以看到。

对于权限、认证令牌或其他敏感状态，请将值存储在仅后端变量中（以 `_` 为前缀），这样它们永远不会被发送到客户端。
```

例如，使用仅后端变量存储大型数据结构，然后通过缓存变量（cached vars）分页到前端。

```python demo exec
import numpy as np


class BackendVarState(rx.State):
    _backend: np.ndarray = np.array([random.randint(0, 100) for _ in range(100)])
    offset: int = 0
    limit: int = 10

    @rx.var(cache=True)
    def page(self) -> list[int]:
        return [
            int(x)  # explicit cast to int
            for x in self._backend[self.offset : self.offset + self.limit]
        ]

    @rx.var(cache=True)
    def page_number(self) -> int:
        return (self.offset // self.limit) + 1 + (1 if self.offset % self.limit else 0)

    @rx.var(cache=True)
    def total_pages(self) -> int:
        return len(self._backend) // self.limit + (
            1 if len(self._backend) % self.limit else 0
        )

    @rx.event
    def prev_page(self):
        self.offset = max(self.offset - self.limit, 0)

    @rx.event
    def next_page(self):
        if self.offset + self.limit < len(self._backend):
            self.offset += self.limit

    @rx.event
    def generate_more(self):
        self._backend = np.append(
            self._backend,
            [random.randint(0, 100) for _ in range(random.randint(0, 100))],
        )

    @rx.event
    def set_limit(self, value: str):
        self.limit = int(value)


def backend_var_example():
    return rx.vstack(
        rx.hstack(
            rx.button(
                "Prev",
                on_click=BackendVarState.prev_page,
            ),
            rx.text(
                f"Page {BackendVarState.page_number} / {BackendVarState.total_pages}"
            ),
            rx.button(
                "Next",
                on_click=BackendVarState.next_page,
            ),
            rx.text("Page Size"),
            rx.input(
                width="5em",
                value=BackendVarState.limit,
                on_change=BackendVarState.set_limit,
            ),
            rx.button("Generate More", on_click=BackendVarState.generate_more),
        ),
        rx.list(
            rx.foreach(
                BackendVarState.page,
                lambda x, ix: rx.text(f"_backend[{ix + BackendVarState.offset}] = {x}"),
            ),
        ),
    )
```

## 使用 rx.field / rx.Field 改善变量的类型提示

在定义状态变量时，你可以使用 `rx.Field[T]` 来标注变量的类型。然后，你可以使用 `rx.field(default_value)` 初始化变量，其中 `default_value` 是类型 `T` 的实例。

这种方法使变量的类型显式化，有助于静态分析工具进行类型检查。此外，它还显示哪些方法允许在前端代码中修改变量，因为这些方法列在类型提示中。

以下是两个示例：

```python
import reflex as rx

app = rx.App()


class State(rx.State):
    x: rx.Field[bool] = rx.field(False)

    def flip(self):
        self.x = not self.x


@app.add_page
def index():
    return rx.vstack(
        rx.button("Click me", on_click=State.flip),
        rx.text(State.x),
        rx.text(~State.x),
    )
```

这里 `State.x` 由于被正确标注为 `boolean` 类型变量，获得了更好的代码补全，例如我们可以获得 `to_string()` 或 `equals()` 等选项。

```python
import reflex as rx

app = rx.App()


class State(rx.State):
    x: rx.Field[dict[str, list[int]]] = rx.field(default_factory=dict)


@app.add_page
def index():
    return rx.vstack(
        rx.text(State.x.values()[0][0]),
    )
```

这里 `State.x` 由于被正确标注为 `str` 到 `int` 列表的 `dict` 类型变量，获得了更好的代码补全，例如我们可以获得 `contains()`、`keys()`、`values()`、`items()` 或 `merge()` 等选项。
