---
title: Ring Progress
---

# Ring Progress 组件

`rxe.mantine.ring_progress` 是一个以环形格式显示进度的组件。它适用于以紧凑且美观的方式可视化完成百分比或其他指标。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import random


class RingProgressState(rx.State):
    value: int = 50

    @rx.event
    def random(self):
        self.value = random.randint(0, 100)


def ring_progress_example():
    return rx.vstack(
        rxe.mantine.ring_progress(
            size=100,
            sections=[
                {"value": RingProgressState.value, "color": "blue"},
            ],
        ),
        rx.button("Randomize", on_click=RingProgressState.random),
    )
```
