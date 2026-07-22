---
title: Number Formatter
---

# Number Formatter 组件
`rxe.mantine.number_formatter` 是一个以用户友好的方式格式化数字的组件。它允许你指定格式、精度和其他数字显示选项。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def number_formatter_example():
    return rx.vstack(
        rxe.mantine.number_formatter(
            value=100,
            prefix="$",
        ),
        rxe.mantine.number_formatter(
            value=100,
            suffix="€",
        ),
        rxe.mantine.number_formatter(
            value=1234567.89,
            thousand_separator=True,
        ),
    )
```
