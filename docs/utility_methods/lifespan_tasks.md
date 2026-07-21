# 生命周期任务（Lifespan Tasks）

_在 v0.5.2 中添加_

生命周期任务是当后端服务器运行时执行的协程。它们
对于设置应用的初始全局状态、运行周期性任务以及在服务器关闭时清理资源非常有用。

生命周期任务被定义为异步协程（async coroutines）或异步上下文管理器（async contextmanagers）。为避免
阻塞事件线程，切勿在生命周期任务中使用 `time.sleep` 或执行非异步 I/O 操作。

任务按注册顺序执行。

在开发模式下，当热重载发生时，生命周期任务将停止并重新启动。

## 生命周期任务 vs 后台事件（Background Events）

生命周期任务并不是在工作流之外运行工作的唯一方式。
根据启动工作的原因以及操作的对象来选择：

- 使用**生命周期任务**处理自动、持续、应用级别的工作，这些工作独立于任何用户或会话运行——它在应用启动时开始，不与 UI 状态绑定。示例：按间隔轮询外部 API、监控服务、刷新共享缓存、计划维护。
- 使用[后台事件](/docs/events/background-events)（`@rx.event(background=True)`）处理用户触发的、与会话绑定的工作，这些工作需要读取或更新该用户的 UI 状态。示例：用户点击提交后处理文件、按需调用 API、在用户发起的长时间运行操作期间显示进度。

例如，"每 5 分钟检查一次外部 API"是一个生命周期任务，而"当用户点击提交时处理数据"是一个后台事件。

## 任务

任何异步协程都可以用作生命周期任务。它将在后端启动时开始运行，并持续运行直到返回或因服务器关闭而被取消。长时间运行的任务应捕获 `asyncio.CancelledError` 以执行必要的清理。

```python
async def long_running_task(foo, bar):
    print(f"Starting {foo} {bar} task")
    some_api = SomeApi(foo)
    try:
        while True:
            updates = some_api.poll_for_updates()
            other_api.push_changes(updates, bar)
            await asyncio.sleep(5)  # 添加一些轮询延迟以避免运行过于频繁
    except asyncio.CancelledError:
        some_api.close()  # 如果需要，清理 API
        print("Task was stopped")
```

### 注册任务

要注册生命周期任务，使用 `app.register_lifespan_task(coro_func, **kwargs)`。
注册时指定的任何关键字参数都将传递给任务。

如果任务接受特殊参数 `app`，则将传递 Reflex 应用实例（`rx.App`/`LifespanMixin`）。

如果任务接受特殊参数 `starlette_app`，则将传递底层的 `Starlette` 应用实例。

```python
app = rx.App()
app.register_lifespan_task(long_running_task, foo=42, bar=os.environ["BAR_PARAM"])
```

所有任务必须在应用启动之前注册。在生命周期已经开始之后调用 `register_lifespan_task`（例如，从事件处理程序或另一个生命周期任务内部）将引发 `RuntimeError`。

### 检查已注册的任务

要获取当前注册的生命周期任务，使用 `app.get_lifespan_tasks()`，它返回按注册顺序排列的任务 `tuple`（元组）。

## 上下文管理器

生命周期任务也可以定义为异步上下文管理器。这对于设置和拆除资源非常有用，其行为类似于 ASGI 生命周期协议。

首个 `yield` 之前的代码将在后端启动时运行。在后端关闭时，`yield` 之后的代码将运行以进行清理。

```python
from contextlib import asynccontextmanager


def fake_answer_to_everything_ml_model(x: float):
    return x * 42


ml_models = {}


@asynccontextmanager
async def setup_model(app):
    # 加载 ML 模型
    ml_models["answer_to_everything"] = fake_answer_to_everything_ml_model
    yield
    # 清理 ML 模型并释放资源
    ml_models.clear()


...

app = rx.App()
app.register_lifespan_task(setup_model)
```
