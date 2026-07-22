---
components:
  - rx.recharts.AreaChart
  - rx.recharts.Area
title: Area Chart
meta_description: "Create area charts in Python with Reflex. Build stacked, gradient, and multi-series Recharts area charts with custom axes, tooltips, legends, and colors — all in pure Python."
---

# 面积图（Area Chart）

```python exec
import reflex as rx
import random
```

Reflex 中的面积图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。Recharts 面积图通过连接数据点的折线与坐标轴之间的填充区域来展示定量数据。

## 基本示例

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


def area_simple():
    return rx.recharts.area_chart(
        rx.recharts.area(
            data_key="uv",
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=data,
        width="100%",
        height=250,
    )
```

## 图表同步

`sync_id` 属性允许你同步两个图表。在示例中，两个图表都设置为 "1"，表示它们应该同步。这意味着在一个图表上执行的任何交互（如刷选）都会反映到另一个图表上。

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


def area_sync():
    return rx.vstack(
        rx.recharts.bar_chart(
            rx.recharts.graphing_tooltip(),
            rx.recharts.bar(data_key="uv", stroke="#8884d8", fill="#8884d8"),
            rx.recharts.bar(
                data_key="pv",
                stroke="#82ca9d",
                fill="#82ca9d",
            ),
            rx.recharts.x_axis(data_key="name"),
            rx.recharts.y_axis(),
            data=data,
            sync_id="1",
            width="100%",
            height=200,
        ),
        rx.recharts.composed_chart(
            rx.recharts.area(data_key="uv", stroke="#8884d8", fill="#8884d8"),
            rx.recharts.line(
                data_key="pv",
                type_="monotone",
                stroke="#ff7300",
            ),
            rx.recharts.x_axis(data_key="name"),
            rx.recharts.y_axis(),
            rx.recharts.graphing_tooltip(),
            rx.recharts.brush(data_key="name", height=30, stroke="#8884d8"),
            data=data,
            sync_id="1",
            width="100%",
            height=250,
        ),
        width="100%",
    )
```

## 堆叠面积图（Stacked Area Chart）

`stack_id` 属性允许你将多个面积区域堆叠在一起，创建堆叠面积图。在示例中，两个图表都设置为 "1"，表示它们应该堆叠在一起。这意味着图表的面积区域将垂直堆叠，每个图表的值共同构成堆叠区域的总高度。

这与 `sync_id` 属性类似，但它不是同步图表之间的交互，而是简单地将图表堆叠在一起。

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


def area_stack():
    return rx.recharts.area_chart(
        rx.recharts.area(
            data_key="uv",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
            stack_id="1",
        ),
        rx.recharts.area(
            data_key="pv",
            stroke=rx.color("green", 9),
            fill=rx.color("green", 8),
            stack_id="1",
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=data,
        stack_offset="none",
        margin={"top": 5, "right": 5, "bottom": 5, "left": 5},
        width="100%",
        height=300,
    )
```

## 多坐标轴

可以使用多个坐标轴在同一图表上显示具有不同比例或单位的数据系列。这样可以更全面地比较和分析数据。

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


def area_multi_axis():
    return rx.recharts.area_chart(
        rx.recharts.area(
            data_key="uv",
            stroke="#8884d8",
            fill="#8884d8",
            x_axis_id="primary",
            y_axis_id="left",
        ),
        rx.recharts.area(
            data_key="pv",
            x_axis_id="secondary",
            y_axis_id="right",
            type_="monotone",
            stroke="#82ca9d",
            fill="#82ca9d",
        ),
        rx.recharts.x_axis(data_key="name", x_axis_id="primary"),
        rx.recharts.x_axis(data_key="name", x_axis_id="secondary", orientation="top"),
        rx.recharts.y_axis(data_key="uv", y_axis_id="left"),
        rx.recharts.y_axis(data_key="pv", y_axis_id="right", orientation="right"),
        rx.recharts.graphing_tooltip(),
        rx.recharts.legend(),
        data=data,
        width="100%",
        height=300,
    )
```

## 布局

使用 `layout` 属性将方向设置为 `"horizontal"`（默认）或 `"vertical"`。

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


def area_vertical():
    return rx.recharts.area_chart(
        rx.recharts.area(
            data_key="uv",
            stroke=rx.color("accent", 8),
            fill=rx.color("accent", 3),
        ),
        rx.recharts.x_axis(type_="number"),
        rx.recharts.y_axis(data_key="name", type_="category"),
        data=data,
        layout="vertical",
        height=300,
        width="100%",
    )
```

## 带状态的示例

下面是一个使用 `State` 的面积图示例。我们定义了一个 `randomize_data` 函数，当使用 `on_click=AreaState.randomize_data` 点击第一个定义的 `area` 时，该函数会随机更改两个图表的数据。

```python demo exec
class AreaState(rx.State):
    data = data
    curve_type = ""

    @rx.event
    def randomize_data(self):
        for i in range(len(self.data)):
            self.data[i]["uv"] = random.randint(0, 10000)
            self.data[i]["pv"] = random.randint(0, 10000)
            self.data[i]["amt"] = random.randint(0, 10000)

    def change_curve_type(self, type_input):
        self.curve_type = type_input


def area_stateful():
    return rx.vstack(
        rx.hstack(
            rx.text("Curve Type:"),
            rx.select(
                ["basis", "natural", "step"],
                on_change=AreaState.change_curve_type,
                default_value="basis",
            ),
        ),
        rx.recharts.area_chart(
            rx.recharts.area(
                data_key="uv",
                on_click=AreaState.randomize_data,
                type_=AreaState.curve_type,
            ),
            rx.recharts.area(
                data_key="pv",
                stroke="#82ca9d",
                fill="#82ca9d",
                on_click=AreaState.randomize_data,
                type_=AreaState.curve_type,
            ),
            rx.recharts.x_axis(
                data_key="name",
            ),
            rx.recharts.y_axis(),
            rx.recharts.legend(),
            rx.recharts.cartesian_grid(),
            data=AreaState.data,
            width="100%",
            height=400,
        ),
        width="100%",
    )
```

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [折线图](/docs/library/graphing/charts/linechart)
- [柱状图](/docs/library/graphing/charts/barchart)
- [组合图](/docs/library/graphing/charts/composedchart)
