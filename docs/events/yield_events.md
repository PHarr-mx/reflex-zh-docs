```python exec
import reflex as rx
```

# 通过 Yield 更新

常规的事件处理器会在运行完成后发送一个 `StateUpdate`。这对基本事件来说没问题，但有时我们需要更复杂的逻辑。为了在事件处理器中多次更新 UI，我们可以在需要发送更新的时候使用 `yield`。

为此，我们可以使用 Python 关键字 `yield`。函数内部的每个 yield 都会向前端发送一个 `StateUpdate`，其中包含事件处理器执行到该点的所有更改。

下面的示例展示了如何向 UI 发送 100 次更新。

```python demo exec
class MultiUpdateState(rx.State):
    count: int = 0

    @rx.event
    def timed_update(self):
        for i in range(100):
            self.count += 1
            yield


def multi_update():
    return rx.vstack(
        rx.text(MultiUpdateState.count),
        rx.button("Start", on_click=MultiUpdateState.timed_update),
    )
```

以下是另一个使用加载图标进行多次更新的示例。

```python demo exec
import asyncio


class ProgressExampleState(rx.State):
    count: int = 0
    show_progress: bool = False

    @rx.event
    async def increment(self):
        self.show_progress = True
        yield
        # Think really hard.
        await asyncio.sleep(0.5)
        self.count += 1
        self.show_progress = False


def progress_example():
    return rx.button(
        ProgressExampleState.count,
        on_click=ProgressExampleState.increment,
        loading=ProgressExampleState.show_progress,
    )
```

```md video https://youtube.com/embed/ITOZkzjtjUA?start=6463&end=6835
# Video: Asyncio with Yield
```

## Yield 其他事件

事件也可以 yield 其他事件。这在需要将事件链接在一起时很有用。为此，你可以直接 yield 事件处理器函数本身。

```md alert
# 通过类引用其他事件处理器

在使用 `yield` 链式调用其他事件处理器时，请通过状态类（state class）来访问它，而不是 `self`。
```

```python demo exec
import asyncio


class YieldEventsState(rx.State):
    count: int = 0
    show_progress: bool = False

    @rx.event
    async def add_five(self):
        self.show_progress = True
        yield
        # Think really hard.
        await asyncio.sleep(1)
        self.count += 5
        self.show_progress = False

    @rx.event
    async def increment(self):
        yield YieldEventsState.add_five
        yield YieldEventsState.add_five
        yield YieldEventsState.add_five


def multiple_yield_example():
    return rx.button(
        YieldEventsState.count,
        on_click=YieldEventsState.increment,
        loading=YieldEventsState.show_progress,
    )
```
