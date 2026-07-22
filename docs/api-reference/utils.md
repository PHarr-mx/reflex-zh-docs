```python exec
import reflex as rx
```

# 实用函数（Utility Functions）

Reflex 提供实用函数来帮助处理应用中的常见任务。

## run_in_thread

`run_in_thread` 函数允许你在单独的线程中运行**非异步**函数，这对于防止长时间运行的操作阻塞 UI 事件队列非常有用。

```python
async def run_in_thread(func: Callable) -> Any
```

### 参数

- `func`：要在单独线程中运行的非异步函数。

### 返回值

- 函数的返回值。

### 异常

- `ValueError`：如果函数是异步函数。

### 用法

```python demo exec id=run_in_thread_demo
import asyncio
import dataclasses
import time
import reflex as rx


def quick_blocking_function():
    time.sleep(0.5)
    return "Quick task completed successfully!"


def slow_blocking_function():
    time.sleep(3.0)
    return "This should never be returned due to timeout!"


@dataclasses.dataclass
class TaskInfo:
    result: str = "No result yet"
    status: str = "Idle"


class RunInThreadState(rx.State):
    tasks: list[TaskInfo] = []

    @rx.event(background=True)
    async def run_quick_task(self):
        """Run a quick task that completes within the timeout."""
        async with self:
            task_ix = len(self.tasks)
            self.tasks.append(TaskInfo(status="Running quick task..."))
            task_info = self.tasks[task_ix]

        try:
            result = await rx.run_in_thread(quick_blocking_function)
            async with self:
                task_info.result = result
                task_info.status = "Complete"
        except Exception as e:
            async with self:
                task_info.result = f"Error: {str(e)}"
                task_info.status = "Failed"

    @rx.event(background=True)
    async def run_slow_task(self):
        """Run a slow task that exceeds the timeout."""
        async with self:
            task_ix = len(self.tasks)
            self.tasks.append(TaskInfo(status="Running slow task..."))
            task_info = self.tasks[task_ix]

        try:
            # Run with a timeout of 1 second (not enough time)
            result = await asyncio.wait_for(
                rx.run_in_thread(slow_blocking_function),
                timeout=1.0,
            )
            async with self:
                task_info.result = result
                task_info.status = "Complete"
        except asyncio.TimeoutError:
            async with self:
                # Warning: even though we stopped waiting for the task,
                # it may still be running in thread
                task_info.result = "Task timed out after 1 second!"
                task_info.status = "Timeout"
        except Exception as e:
            async with self:
                task_info.result = f"Error: {str(e)}"
                task_info.status = "Failed"


def run_in_thread_example():
    return rx.vstack(
        rx.heading("run_in_thread Example", as_="h2", size="3"),
        rx.hstack(
            rx.button(
                "Run Quick Task",
                on_click=RunInThreadState.run_quick_task,
                color_scheme="green",
            ),
            rx.button(
                "Run Slow Task (exceeds timeout)",
                on_click=RunInThreadState.run_slow_task,
                color_scheme="red",
            ),
        ),
        rx.vstack(
            rx.foreach(
                RunInThreadState.tasks.reverse()[:10],
                lambda task: rx.hstack(
                    rx.text(task.status),
                    rx.spacer(),
                    rx.text(task.result),
                ),
            ),
            align="start",
            width="100%",
        ),
        width="100%",
        align_items="start",
        spacing="4",
    )
```

### 何时使用 run_in_thread

在以下情况使用 `run_in_thread`：

1. 执行否则会阻塞事件循环的 CPU 密集型操作
2. 调用没有异步等效项的同步库
3. 防止长时间运行的操作阻塞 UI 响应性

### 示例：处理大文件

```python
import reflex as rx
import time


class FileProcessingState(rx.State):
    progress: str = "Ready"

    @rx.event(background=True)
    async def process_large_file(self):
        async with self:
            self.progress = "Processing file..."

        def process_file():
            # Simulate processing a large file
            time.sleep(5)
            return "File processed successfully!"

        # Save the result to a local variable to avoid blocking the event loop.
        result = await rx.run_in_thread(process_file)
        async with self:
            # Then assign the local result to the state while holding the lock.
            self.progress = result
```
