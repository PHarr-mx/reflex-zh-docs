---
components:
  - rx.recharts.LineChart
  - rx.recharts.Line
title: Line Chart
meta_description: "Build interactive line charts in pure Python with Reflex. Plot single or multi-line Recharts time series with custom axes, tooltips, legends, and styling — no JavaScript needed."
---

# 折线图（Line Chart）

```python exec
import random
from typing import Any
import reflex as rx
```

Reflex 中的折线图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。折线图是一种用于展示随时间变化的信息的图表类型。折线图通过绘制一系列数据点并用直线将它们连接起来而创建。

## 简单示例

对于折线图，我们必须为每组要绘制的值定义一个 `rx.recharts.line()` 组件。每个 `rx.recharts.line()` 组件都有一个 `data_key`，用于明确指出我们正在跟踪数据中的哪个变量。在这个简单示例中，我们将 `pv` 和 `uv` 绘制为独立的折线，并将 `name` 列设置为 `rx.recharts.x_axis` 中的 `data_key`。像这样绘制多条折线会创建一个多系列折线图。

```python demo graphing
data = [
    {"name": "Page A", "uv": 4000, "pv": 2400, "amt": 2400},
    {"name": "Page B", "uv": 3000, "pv": 1398, "amt": 2210},
    {"name": "Page C", "uv": 2000, "pv": 9800, "amt": 2290},
    {"name": "Page D", "uv": 2780, "pv": 3908, "amt": 2000},
    {"name": "Page E", "uv": 1890, "pv": 4800, "amt": 2181},
    {"name": "Page F", "uv": 2390, "pv": 3800, "amt": 2500},
    {"name": "Page G", "uv": 3490, "pv": 4300, "amt": 2100},
]


def line_simple():
    return rx.recharts.line_chart(
        rx.recharts.line(
            data_key="pv",
        ),
        rx.recharts.line(
            data_key="uv",
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=data,
        width="100%",
        height=300,
    )
```

## 属性示例

第二个示例使用与第一个示例完全相同的数据，但现在我们为折线图添加了一些额外功能。我们为 `rx.recharts.line` 添加了 `type_` 属性来设置不同的折线样式。此外，我们添加了 `rx.recharts.cartesian_grid` 以获得背景网格，添加了 `rx.recharts.legend` 以获得图例，还添加了 `rx.recharts.graphing_tooltip` 以在鼠标悬停在图表元素上时显示带有文本的提示框。

```python demo graphing
data = [
    {"name": "Page A", "uv": 4000, "pv": 2400, "amt": 2400},
    {"name": "Page B", "uv": 3000, "pv": 1398, "amt": 2210},
    {"name": "Page C", "uv": 2000, "pv": 9800, "amt": 2290},
    {"name": "Page D", "uv": 2780, "pv": 3908, "amt": 2000},
    {"name": "Page E", "uv": 1890, "pv": 4800, "amt": 2181},
    {"name": "Page F", "uv": 2390, "pv": 3800, "amt": 2500},
    {"name": "Page G", "uv": 3490, "pv": 4300, "amt": 2100},
]


def line_features():
    return rx.recharts.line_chart(
        rx.recharts.line(
            data_key="pv",
            type_="monotone",
            stroke="#8884d8",
        ),
        rx.recharts.line(
            data_key="uv",
            type_="monotone",
            stroke="#82ca9d",
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.cartesian_grid(stroke_dasharray="3 3"),
        rx.recharts.graphing_tooltip(),
        rx.recharts.legend(),
        data=data,
        width="100%",
        height=300,
    )
```

## 布局

`layout` 属性允许你将图表的方向设置为垂直或水平。`margin` 属性定义图表周围的间距。

```md alert info
# 在图表周围添加边距以确保适当的间距并提高可读性。默认情况下，在图表的所有侧面提供边距，以创建美观且实用的数据展示效果。
```

```python demo graphing
data = [
    {"name": "Page A", "uv": 4000, "pv": 2400, "amt": 2400},
    {"name": "Page B", "uv": 3000, "pv": 1398, "amt": 2210},
    {"name": "Page C", "uv": 2000, "pv": 9800, "amt": 2290},
    {"name": "Page D", "uv": 2780, "pv": 3908, "amt": 2000},
    {"name": "Page E", "uv": 1890, "pv": 4800, "amt": 2181},
    {"name": "Page F", "uv": 2390, "pv": 3800, "amt": 2500},
    {"name": "Page G", "uv": 3490, "pv": 4300, "amt": 2100},
]


def line_vertical():
    return rx.recharts.line_chart(
        rx.recharts.line(
            data_key="pv",
            stroke=rx.color("accent", 9),
        ),
        rx.recharts.line(
            data_key="uv",
            stroke=rx.color("green", 9),
        ),
        rx.recharts.x_axis(type_="number"),
        rx.recharts.y_axis(data_key="name", type_="category"),
        layout="vertical",
        margin={"top": 20, "right": 20, "left": 20, "bottom": 20},
        data=data,
        height=300,
        width="100%",
    )
```

## 动态数据

通过将 `data` 属性绑定到 State 变量，可以修改图表数据。大多数其他属性（如 `type_`）也可以动态控制。在下面的示例中，"Munge Data" 按钮可用于随机修改数据，两个 `select` 元素用于更改折线的 `type_`。由于数据和样式保存在每个浏览器标签页的 State 中，因此更改对其他访问者不可见。

```python demo exec
initial_data = data


class LineChartState(rx.State):
    data: list[dict[str, Any]] = initial_data
    pv_type: str = "monotone"
    uv_type: str = "monotone"

    @rx.event
    def set_pv_type(self, pv_type: str):
        self.pv_type = pv_type

    @rx.event
    def set_uv_type(self, uv_type: str):
        self.uv_type = uv_type

    @rx.event
    def munge_data(self):
        for row in self.data:
            row["uv"] += random.randint(-500, 500)
            row["pv"] += random.randint(-1000, 1000)


def line_dynamic():
    return rx.vstack(
        rx.recharts.line_chart(
            rx.recharts.line(
                data_key="pv",
                type_=LineChartState.pv_type,
                stroke="#8884d8",
            ),
            rx.recharts.line(
                data_key="uv",
                type_=LineChartState.uv_type,
                stroke="#82ca9d",
            ),
            rx.recharts.x_axis(data_key="name"),
            rx.recharts.y_axis(),
            data=LineChartState.data,
            margin={"top": 20, "right": 20, "left": 20, "bottom": 20},
            width="100%",
            height=300,
        ),
        rx.hstack(
            rx.button("Munge Data", on_click=LineChartState.munge_data),
            rx.select(
                ["monotone", "linear", "step", "stepBefore", "stepAfter"],
                value=LineChartState.pv_type,
                on_change=LineChartState.set_pv_type,
            ),
            rx.select(
                ["monotone", "linear", "step", "stepBefore", "stepAfter"],
                value=LineChartState.uv_type,
                on_change=LineChartState.set_uv_type,
            ),
        ),
        width="100%",
    )
```

要了解如何使用 `sync_id`、`x_axis_id` 和 `y_axis_id` 属性，请查看面积图的[文档](/docs/library/graphing/charts/areachart)，其中对这些属性都有详细的示例说明。

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [面积图](/docs/library/graphing/charts/areachart)
- [柱状图](/docs/library/graphing/charts/barchart)
- [散点图](/docs/library/graphing/charts/scatterchart)
