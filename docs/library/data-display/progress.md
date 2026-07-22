---
components:
  - rx.progress

Progress: |
  lambda **props: rx.box(rx.progress(value=50, **props), width="20rem")
---

# 进度条（Progress）

进度条（Progress）用于显示需要较长时间或由多个步骤组成的任务的进度状态。

```python exec
import reflex as rx
```

## 基本示例

`rx.progress` 需要通过 `value` 属性来设置进度值。
`width` 默认为 100%，即父组件的宽度。

```python demo
rx.vstack(
    rx.progress(value=0),
    rx.progress(value=50),
    rx.progress(value=100),
    width="50%",
)
```

对于动态进度条，你可以将状态变量赋值给 `value` 属性，而不是常量值。

```python demo exec
import asyncio


class ProgressState(rx.State):
    value: int = 0

    @rx.event(background=True)
    async def start_progress(self):
        async with self:
            self.value = 0
        while self.value < 100:
            await asyncio.sleep(0.1)
            async with self:
                self.value += 1


def live_progress():
    return rx.hstack(
        rx.progress(value=ProgressState.value),
        rx.button("Start", on_click=ProgressState.start_progress),
        width="50%",
    )
```
