---
components:
  - rx.recharts.PieChart
  - rx.recharts.Pie
title: Pie Chart
meta_description: "Build interactive pie and donut charts in Python with Reflex. Create Recharts pie charts with custom colors, labels, legends, and tooltips — all in pure Python, no JavaScript required."
---

# 饼图（Pie Chart）

```python exec
import reflex as rx
```

Reflex 中的饼图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。饼图——当中心为空心时也称为环形图（Donut Chart）——是一种圆形统计图形，被分割成多个扇形区域来说明数值比例。

对于饼图，我们必须为每组要绘制的值定义一个 `rx.recharts.pie()` 组件。每个 `rx.recharts.pie()` 组件都有一个 `data`、一个 `data_key` 和一个 `name_key`，用于明确指出我们正在跟踪哪些数据以及数据中的哪些变量。在这个简单示例中，我们将 `value` 列作为 `data_key` 进行绘制，并将 `name` 列设置为 `name_key`。
我们还使用 `fill` 属性来设置饼图扇形的颜色。

```python demo graphing
data01 = [
    {"name": "Group A", "value": 400},
    {"name": "Group B", "value": 300, "fill": "#AC0E08FF"},
    {"name": "Group C", "value": 300, "fill": "rgb(80,40, 190)"},
    {"name": "Group D", "value": 200, "fill": rx.color("yellow", 10)},
    {"name": "Group E", "value": 278, "fill": "purple"},
    {"name": "Group F", "value": 189, "fill": "orange"},
]


def pie_simple():
    return rx.recharts.pie_chart(
        rx.recharts.pie(
            data=data01,
            data_key="value",
            name_key="name",
            fill="#8884d8",
            label=True,
        ),
        width="100%",
        height=300,
    )
```

我们还可以通过使用两个 `rx.recharts.pie` 组件在同一个图表上添加两个饼图。

在这个示例中使用了 `inner_radius` 和 `outer_radius` 属性。它们定义了饼图的环形形状：`inner_radius` 创建空心中心（使用 "0%" 表示完整饼图），而 `outer_radius` 设置整体大小。`padding_angle` 属性（用于下面的绿色饼图）在饼图扇形之间添加间距，增强各个分段的可见性。

```python demo graphing
data01 = [
    {"name": "Group A", "value": 400},
    {"name": "Group B", "value": 300},
    {"name": "Group C", "value": 300},
    {"name": "Group D", "value": 200},
    {"name": "Group E", "value": 278},
    {"name": "Group F", "value": 189},
]
data02 = [
    {"name": "Group A", "value": 2400},
    {"name": "Group B", "value": 4567},
    {"name": "Group C", "value": 1398},
    {"name": "Group D", "value": 9800},
    {"name": "Group E", "value": 3908},
    {"name": "Group F", "value": 4800},
]


def pie_double():
    return rx.recharts.pie_chart(
        rx.recharts.pie(
            data=data01,
            data_key="value",
            name_key="name",
            fill="#82ca9d",
            inner_radius="60%",
            padding_angle=5,
        ),
        rx.recharts.pie(
            data=data02,
            data_key="value",
            name_key="name",
            fill="#8884d8",
            outer_radius="50%",
        ),
        rx.recharts.graphing_tooltip(),
        width="100%",
        height=300,
    )
```

## 动态数据

绑定到 State 变量的图表数据会在状态变化时自动更新，提供了一种根据用户界面元素可视化数据的好方法。查看 "Data" 选项卡以了解驱动此半饼图的子状态。

```python demo exec
from typing import Any


class PieChartState(rx.State):
    resources: list[dict[str, Any]] = [
        dict(type_="🏆", count=1),
        dict(type_="🪵", count=1),
        dict(type_="🥑", count=1),
        dict(type_="🧱", count=1),
    ]

    @rx.var(cache=True)
    def resource_types(self) -> list[str]:
        return [r["type_"] for r in self.resources]

    @rx.event
    def increment(self, type_: str):
        for resource in self.resources:
            if resource["type_"] == type_:
                resource["count"] += 1
                break

    @rx.event
    def decrement(self, type_: str):
        for resource in self.resources:
            if resource["type_"] == type_ and resource["count"] > 0:
                resource["count"] -= 1
                break


def dynamic_pie_example():
    return rx.hstack(
        rx.recharts.pie_chart(
            rx.recharts.pie(
                data=PieChartState.resources,
                data_key="count",
                name_key="type_",
                cx="50%",
                cy="50%",
                start_angle=180,
                end_angle=0,
                fill="#8884d8",
                label=True,
            ),
            rx.recharts.graphing_tooltip(),
        ),
        rx.vstack(
            rx.foreach(
                PieChartState.resource_types,
                lambda type_, i: rx.hstack(
                    rx.button("-", on_click=PieChartState.decrement(type_)),
                    rx.text(type_, PieChartState.resources[i]["count"]),
                    rx.button("+", on_click=PieChartState.increment(type_)),
                ),
            ),
        ),
        width="100%",
        height="15em",
    )
```

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [径向柱状图](/docs/library/graphing/charts/radialbarchart)
- [漏斗图](/docs/library/graphing/charts/funnelchart)
- [柱状图](/docs/library/graphing/charts/barchart)
