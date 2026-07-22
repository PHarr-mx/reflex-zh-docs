---
title: Semi Circle Progress
---

# Semi Circle Progress 组件
`rxe.mantine.semi_circle_progress` 是一个以半圆形格式显示进度的组件。它适用于以紧凑且美观的方式可视化完成百分比或其他指标。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import random


class SemiCircleProgressState(rx.State):
    value: int = 50

    @rx.event
    def random(self):
        self.value = random.randint(0, 100)


def semi_circle_progress_example():
    return rx.vstack(
        rxe.mantine.semi_circle_progress(
            size=100,
            value=SemiCircleProgressState.value,
        ),
        rx.button("Randomize", on_click=SemiCircleProgressState.random),
    )
```
