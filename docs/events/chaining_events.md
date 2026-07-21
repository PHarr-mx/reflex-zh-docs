```python exec
import reflex as rx
```

# 事件链

## 从事件处理器中调用事件处理器

你可以从事件处理器中调用其他事件处理器，以保持代码的模块化。只需使用 `self.call_handler` 语法来运行另一个事件处理器。与往常一样，你可以在函数中使用 yield 向前端发送增量更新。

```python demo exec id=call-handler
import asyncio


class CallHandlerState(rx.State):
    count: int = 0
    progress: int = 0

    @rx.event
    def set_progress(self, value: int):
        self.progress = value

    @rx.event
    async def run(self):
        # Reset the count.
        self.set_progress(0)
        yield

        # Count to 10 while showing progress.
        for i in range(10):
            # Wait and increment.
            await asyncio.sleep(0.5)
            self.count += 1

            # Update the progress.
            self.set_progress(i + 1)

            # Yield to send the update.
            yield


def call_handler_example():
    return rx.vstack(
        rx.badge(CallHandlerState.count, font_size="1.5em", color_scheme="green"),
        rx.progress(value=CallHandlerState.progress, max=10, width="100%"),
        rx.button("Run", on_click=CallHandlerState.run),
    )
```

## 从事件处理器返回事件

到目前为止，我们只看到了由组件触发的事件。然而，事件处理器也可以返回事件。

在 Reflex 中，事件处理器是同步运行的，因此一次只能运行一个事件处理器，队列中的事件将被阻塞，直到当前事件处理器完成。返回事件与调用事件处理器的区别在于，返回事件会将事件发送到前端并解除队列阻塞。

```md alert info
# 返回事件时，请务必使用类名 `State`（或任何子状态），而不是 `self`。
```

尝试在下面的输入框中输入一个整数，然后点击输入框外部。

```python demo exec id=collatz
class CollatzState(rx.State):
    count: int = 1

    @rx.event
    def start_collatz(self, count: str):
        """Run the collatz conjecture on the given number."""
        self.count = abs(int(count if count else 1))
        return CollatzState.run_step

    @rx.event
    async def run_step(self):
        """Run a single step of the collatz conjecture."""

        while self.count > 1:
            await asyncio.sleep(0.5)

            if self.count % 2 == 0:
                # If the number is even, divide by 2.
                self.count //= 2
            else:
                # If the number is odd, multiply by 3 and add 1.
                self.count = self.count * 3 + 1
            yield


def collatz_example():
    return rx.vstack(
        rx.badge(CollatzState.count, font_size="1.5em", color_scheme="green"),
        rx.input(on_blur=CollatzState.start_collatz),
    )
```

在此示例中，我们对用户输入的数字运行[考拉兹猜想](https://en.wikipedia.org/wiki/Collatz_conjecture)。

当 `on_blur` 事件被触发时，事件处理器 `start_collatz` 被调用。它设置初始计数值，然后调用 `run_step`，后者一直运行直到计数值达到 `1`。
