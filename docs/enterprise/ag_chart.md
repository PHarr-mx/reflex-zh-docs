# AG Chart

AG Chart 是一个强大的图表库，为企业应用提供交互式图表和数据可视化组件。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def basic_chart():
    return rxe.ag_chart(
        options={
            "data": [
                {"month": "Jan", "value": 10},
                {"month": "Feb", "value": 20},
                {"month": "Mar", "value": 15},
            ],
            "series": [
                {
                    "type": "line",
                    "xKey": "month",
                    "yKey": "value",
                }
            ],
        },
        width="100%",
        height="400px",
    )
```

如需更详细的文档，请参阅 [AG Chart 文档](https://charts.ag-grid.com/)。
