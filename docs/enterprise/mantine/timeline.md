---
title: Timeline
---

# Timeline 组件
`rxe.mantine.timeline` 是一个以线性格式显示事件或里程碑序列的组件。它适用于可视化进度、历史记录或任何顺序信息。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def timeline_example():
    return rx.vstack(
        rxe.mantine.timeline(
            rxe.mantine.timeline.item(
                title="Step 1",
                bullet="•",
            ),
            rxe.mantine.timeline.item(
                title="Step 2",
                bullet="•",
            ),
            rxe.mantine.timeline.item(
                title="Step 3",
                bullet="•",
            ),
            active=1,
            bullet_size=24,
            line_width=2,
            color="blue",
        )
    )
```
