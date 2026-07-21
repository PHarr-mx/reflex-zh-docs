```python exec
import reflex as rx
```

# 后台任务

后台任务是一种特殊类型的 `EventHandler`，它可以与其他 `EventHandler` 函数并发运行。这使得长时间运行的任务可以在不阻塞 UI 交互的情况下执行。

后台任务通过使用 `@rx.event(background=True)` 装饰一个异步的 State 方法来定义。

```md alert warning
# `@rx.event(background=True)` 以前称为 `@rx.background`。

在 Reflex 0.6.5 及更高版本中，`@rx.background` 装饰器已重命名为 `@rx.event(background=True)`。
```

每当后台任务需要与状态交互时，**它必须进入 `async with self` 上下文块**，该块会刷新状态并获取排他锁，以防止其他任务或事件处理器同时修改它。由于其他 `EventHandler` 函数可能在任务运行时修改状态，**在上下文块外部，后台任务访问的 Vars 可能是过期的**。尝试在上下文块外从后台任务修改状态将引发 `ImmutableStateError` 异常。

在以下示例中，`my_task` 事件处理器使用 `@rx.event(background=True)` 装饰，并在满足特定条件时每半秒递增 `counter` 变量。在它运行时，UI 保持交互状态，并继续正常处理事件。

```md alert info
# 后台事件类似于简单的任务队列，如 [Celery](https://www.fullstackpython.com/celery.html)，允许异步事件处理。
```

```python demo exec id=background_demo
import asyncio
import reflex as rx


class MyTaskState(rx.State):
    counter: int = 0
    max_counter: int = 10
    running: bool = False
    _n_tasks: int = 0

    @rx.event
    def set_max_counter(self, value: str):
        self.max_counter = int(value)

    @rx.event(background=True)
    async def my_task(self):
        async with self:
            # The latest state values are always available inside the context
            if self._n_tasks > 0:
                # only allow 1 concurrent task
                return

            # State mutation is only allowed inside context block
            self._n_tasks += 1

        while True:
            async with self:
                # Check for stopping conditions inside context
                if self.counter >= self.max_counter:
                    self.running = False
                if not self.running:
                    self._n_tasks -= 1
                    return

                self.counter += 1

            # Await long operations outside the context to avoid blocking UI
            await asyncio.sleep(0.5)

    @rx.event
    def toggle_running(self):
        self.running = not self.running
        if self.running:
            return MyTaskState.my_task

    @rx.event
    def clear_counter(self):
        self.counter = 0


def background_task_example():
    return rx.hstack(
        rx.heading(MyTaskState.counter, " /", as_="h2"),
        rx.input(
            value=MyTaskState.max_counter,
            on_change=MyTaskState.set_max_counter,
            width="8em",
        ),
        rx.button(
            rx.cond(~MyTaskState.running, "Start", "Stop"),
            on_click=MyTaskState.toggle_running,
        ),
        rx.button(
            "Reset",
            on_click=MyTaskState.clear_counter,
        ),
    )
```

## 在页面关闭或导航离开时终止后台任务

有时，即使用户导航离开页面或关闭浏览器标签页，后台任务仍可能继续运行。为了处理这种情况，你可以检查与状态关联的 WebSocket 是否已断开连接，并在必要时终止后台任务。

解决方案涉及检查 `client_token` 在 `app.event_namespace.token_to_sid` 映射中是否仍然有效。如果会话丢失（例如用户导航离开或关闭页面），后台任务将停止。

```python
import asyncio
import reflex as rx


class State(rx.State):
    @rx.event(background=True)
    async def loop_function(self):
        while True:
            if self.router.session.client_token not in app.event_namespace.token_to_sid:
                print(
                    "WebSocket connection closed or user navigated away. Stopping background task."
                )
                break

            print("Running background task...")
            await asyncio.sleep(2)


@rx.page(on_load=State.loop_function)
def index():
    return rx.text(
        "Hello, this page will manage background tasks and stop them when the page is closed or navigated away."
    )
```

## 任务生命周期

当后台任务被触发时，它会立即启动，并在 `app.background_tasks` 中保存一个引用。任务完成后，它会从该集合中移除。

同一后台任务的多个实例可以并发运行，框架不会尝试阻止重复任务的启动。

由开发者负责确保在不需要的情况下不会创建重复任务。在上面的示例中，`_n_tasks` 后端变量用于控制 `my_task` 是进入递增循环还是提前退出。

## 后台任务限制

后台任务在大多数情况下与普通的 `EventHandler` 方法类似，但有一些例外：

- 后台任务必须是 `async` 函数。
- 后台任务不能在 `async with self` 上下文块外部修改状态。
- 后台任务可以在 `async with self` 上下文块外部读取状态，但值可能是过期的。
- 后台任务不能直接从其他事件处理器或后台任务调用。应使用 `yield` 或 `return` 来触发后台任务。
