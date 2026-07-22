---
components:
  - rx.recharts.ComposedChart
title: Composed Chart
meta_description: "Build composed charts in Python with Reflex. Combine bars, lines, and areas in a single Recharts composed chart with shared axes, tooltips, and legends — all in pure Python."
---

```python exec
import reflex as rx
```

# 组合图（Composed Chart）

Reflex 中的组合图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。`composed_chart`（也称为混合图表）是一种高级组件图表，由多个图表组合而成，其他图表作为 `composed_chart` 的子组件。图表按照在 `composed_chart` 函数中提供的顺序依次叠放。

```md alert info
# 要了解更多关于各个图表的信息，请查看：**[area_chart](/docs/library/graphing/charts/areachart)**、**[line_chart](/docs/library/graphing/charts/linechart)** 或 **[bar_chart](/docs/library/graphing/charts/barchart)**。
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


def composed():
    return rx.recharts.composed_chart(
        rx.recharts.area(data_key="uv", stroke="#8884d8", fill="#8884d8"),
        rx.recharts.bar(data_key="amt", bar_size=20, fill="#413ea0"),
        rx.recharts.line(data_key="pv", type_="monotone", stroke="#ff7300"),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.cartesian_grid(stroke_dasharray="3 3"),
        rx.recharts.graphing_tooltip(),
        data=data,
        height=250,
        width="100%",
    )
```

## 何时使用组合图

当单一图表类型无法完整讲述数据故事时，组合图非常有用。常见的组合包括：在 `bar` 总量上叠加趋势 `line`、用 `area` 表示范围并用 `line` 表示实际值，或者将累计指标与分期指标进行对比。由于每个系列——`rx.recharts.bar()`、`rx.recharts.line()` 和 `rx.recharts.area()`——都是 `rx.recharts.composed_chart()` 的子组件，它们共享相同的 `data`、x 轴、工具提示和图例。系列按照你列出的顺序绘制，因此后面的子组件会渲染在前面的之上。

## 双轴组合图

当两个系列使用差异很大的比例（例如，计数和金额）时，单个 y 轴会使较小的系列难以阅读。添加第二个 y 轴，并使用 `y_axis_id` 属性将每个系列指向其中一个轴：这里 `orders` 柱形使用左轴，`revenue` 折线使用右轴。

```python demo graphing
data = [
    {"name": "Mon", "orders": 40, "revenue": 2400},
    {"name": "Tue", "orders": 30, "revenue": 1398},
    {"name": "Wed", "orders": 55, "revenue": 9800},
    {"name": "Thu", "orders": 27, "revenue": 3908},
    {"name": "Fri", "orders": 62, "revenue": 4800},
    {"name": "Sat", "orders": 48, "revenue": 3800},
    {"name": "Sun", "orders": 35, "revenue": 4300},
]


def composed_dual_axis():
    return rx.recharts.composed_chart(
        rx.recharts.bar(
            data_key="orders",
            bar_size=20,
            fill=rx.color("accent", 8),
            y_axis_id="left",
        ),
        rx.recharts.line(
            data_key="revenue",
            type_="monotone",
            stroke=rx.color("green", 9),
            y_axis_id="right",
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(y_axis_id="left"),
        rx.recharts.y_axis(y_axis_id="right", orientation="right"),
        rx.recharts.cartesian_grid(stroke_dasharray="3 3"),
        rx.recharts.graphing_tooltip(),
        rx.recharts.legend(),
        data=data,
        height=300,
        width="100%",
    )
```

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [柱状图](/docs/library/graphing/charts/barchart)
- [折线图](/docs/library/graphing/charts/linechart)
- [面积图](/docs/library/graphing/charts/areachart)
