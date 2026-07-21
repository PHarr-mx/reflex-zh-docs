```python exec
import random
import time
import asyncio

import reflex as rx
```

# 计算变量（Computed Vars）

计算变量的值来源于后端上的其他属性。它们在 State 类中定义为使用 `@rx.var` 装饰器的方法。

尝试在输入框中输入内容，然后点击外部。

```python demo exec id=upper
class UppercaseState(rx.State):
    text: str = "hello"

    def set_text(self, value: str):
        self.text = value

    @rx.var
    def upper_text(self) -> str:
        # This will be recomputed whenever `text` changes.
        return self.text.upper()


def uppercase_example():
    return rx.vstack(
        rx.heading(UppercaseState.upper_text, as_="h2"),
        rx.input(on_blur=UppercaseState.set_text, placeholder="Type here..."),
    )
```

这里，`upper_text` 是一个计算变量，始终保存 `text` 的大写版本。

我们建议始终为计算变量使用类型注解。

## 缓存变量（Cached Vars）

默认情况下，所有计算变量都是缓存的（`cache=True`）。缓存变量只在其依赖的其他状态变量发生更改时才重新计算。这对于昂贵的计算很有用，但在某些情况下，它可能不会在你期望时更新。

要创建一个每次状态更新时都重新计算（不依赖检测）的计算变量，请使用 `@rx.var(cache=False)`。

Reflex 的早期版本有一个 `@rx.cached_var` 装饰器，现在已被 `@rx.var` 的 `cache` 参数取代（默认为 `True`）。

```python demo exec
class CachedVarState(rx.State):
    counter_a: int = 0
    counter_b: int = 0

    @rx.var(cache=False)
    def last_touch_time(self) -> str:
        # This is updated anytime the state is updated.
        return time.strftime("%H:%M:%S")

    @rx.event
    def increment_a(self):
        self.counter_a += 1

    @rx.var(cache=True)
    def last_counter_a_update(self) -> str:
        # This is updated only when `counter_a` changes.
        return f"{self.counter_a} at {time.strftime('%H:%M:%S')}"

    @rx.event
    def increment_b(self):
        self.counter_b += 1

    @rx.var(cache=True)
    def last_counter_b_update(self) -> str:
        # This is updated only when `counter_b` changes.
        return f"{self.counter_b} at {time.strftime('%H:%M:%S')}"


def cached_var_example():
    return rx.vstack(
        rx.text(f"State touched at: {CachedVarState.last_touch_time}"),
        rx.text(f"Counter A: {CachedVarState.last_counter_a_update}"),
        rx.text(f"Counter B: {CachedVarState.last_counter_b_update}"),
        rx.hstack(
            rx.button("Increment A", on_click=CachedVarState.increment_a),
            rx.button("Increment B", on_click=CachedVarState.increment_b),
        ),
    )
```

在这个示例中，`last_touch_time` 使用 `cache=False` 来确保每次状态被修改时都会更新。`last_counter_a_update` 是一个缓存的计算变量（使用默认的 `cache=True`），只依赖于 `counter_a`，所以它只在 `counter_a` 更改时重新计算。类似地，`last_counter_b_update` 只依赖于 `counter_b`，因此只会在 `counter_b` 更改时更新。

## 异步计算变量（Async Computed Vars）

异步计算变量允许你在计算变量中使用异步操作。它们被定义为 State 类中带有 `@rx.var` 装饰器的异步方法。异步计算变量对于需要异步处理的操作非常有用，例如：

- 从外部 API 获取数据
- 数据库操作
- 文件 I/O 操作
- 任何其他受益于 async/await 的操作

```python demo exec
class AsyncVarState(rx.State):
    count: int = 0

    @rx.var
    async def delayed_count(self) -> int:
        # Simulate an async operation like an API call
        await asyncio.sleep(0.5)
        return self.count * 2

    @rx.event
    def increment(self):
        self.count += 1


def async_var_example():
    return rx.vstack(
        rx.heading("Async Computed Var Example", as_="h2"),
        rx.text(f"Count: {AsyncVarState.count}"),
        rx.text(f"Delayed count (x2): {AsyncVarState.delayed_count}"),
        rx.button("Increment", on_click=AsyncVarState.increment),
        spacing="4",
    )
```

在这个示例中，`delayed_count` 是一个异步计算变量，在模拟延迟后返回乘以 2 的计数值。当计数值更改时，异步计算变量会自动重新计算。

### 缓存异步计算变量

与常规计算变量一样，异步计算变量也可以被缓存。这对于昂贵的异步操作（如 API 调用或数据库查询）特别有用。

```python demo exec
class AsyncCachedVarState(rx.State):
    user_id: int = 1
    refresh_trigger: int = 0

    @rx.var(cache=True)
    async def user_data(self) -> dict:
        # In a real app, this would be an API call
        await asyncio.sleep(1)  # Simulate network delay

        # Simulate different user data based on user_id
        users = {
            1: {"name": "Alice", "email": "alice@example.com"},
            2: {"name": "Bob", "email": "bob@example.com"},
            3: {"name": "Charlie", "email": "charlie@example.com"},
        }

        return users.get(self.user_id, {"name": "Unknown", "email": "unknown"})

    @rx.event
    def change_user(self):
        # Cycle through users 1-3
        self.user_id = (self.user_id % 3) + 1

    @rx.event
    def force_refresh(self):
        # This will not affect user_data dependencies, but will trigger a state update
        self.refresh_trigger += 1


def async_cached_var_example():
    return rx.vstack(
        rx.heading("Cached Async Computed Var Example", as_="h2"),
        rx.text(f"User ID: {AsyncCachedVarState.user_id}"),
        rx.text(f"User Name: {AsyncCachedVarState.user_data['name']}"),
        rx.text(f"User Email: {AsyncCachedVarState.user_data['email']}"),
        rx.hstack(
            rx.button("Change User", on_click=AsyncCachedVarState.change_user),
            rx.button(
                "Force Refresh (No Effect)", on_click=AsyncCachedVarState.force_refresh
            ),
        ),
        rx.text(
            "Note: The cached async var only updates when user_id changes, not when refresh_trigger changes."
        ),
        spacing="4",
    )
```

在这个示例中，`user_data` 是一个缓存异步计算变量，模拟获取用户数据。它只在 `user_id` 更改时重新计算，而不是在其他状态变量（如 `refresh_trigger`）更改时。这展示了缓存如何与异步计算变量一起工作，以优化昂贵操作的性能。
