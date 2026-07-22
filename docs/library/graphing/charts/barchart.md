---
components:
  - rx.recharts.BarChart
  - rx.recharts.Bar
title: Bar Chart
meta_description: "Create interactive bar charts in Python with Reflex. Build grouped, stacked, and horizontal Recharts bar charts with custom colors, axes, tooltips, and legends — all in pure Python, no JavaScript."
---

# 柱状图（Bar Chart）

```python exec
import reflex as rx
import random
```

Reflex 中的柱状图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，让你能够以纯 Python 可视化分类数据。柱状图使用矩形条来呈现分类数据，条的高度或长度与其所代表的值成正比。

对于柱状图，我们必须为每组要绘制的值定义一个 `rx.recharts.bar()` 组件。每个 `rx.recharts.bar()` 组件都有一个 `data_key`，用于明确指出我们正在跟踪数据中的哪个变量。在这个简单示例中，我们将 `uv` 绘制为柱形，并将 `name` 列设置为 `rx.recharts.x_axis` 中的 `data_key`。

## 简单示例

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


def bar_simple():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="uv",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=data,
        width="100%",
        height=250,
    )
```

## 多个柱形

可以在同一个 `bar_chart` 上放置多个柱形，使用多个 `rx.recharts.bar()` 组件即可。像这样并排绘制时，它们形成一个分组（或聚类）柱状图。

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


def bar_double():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="uv",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
        ),
        rx.recharts.bar(
            data_key="pv",
            stroke=rx.color("green", 9),
            fill=rx.color("green", 8),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=data,
        width="100%",
        height=250,
    )
```

## 堆叠柱状图（Stacked Bar Chart）

要构建堆叠柱状图，给每个 `rx.recharts.bar()` 相同的 `stack_id`。柱形不再并排绘制，而是堆叠在一起，非常适合展示部分与整体的构成关系（也称为分段柱状图）。在 `bar_chart` 上设置 `stack_offset="expand"` 可将其变为 100% 堆叠柱状图。

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


def bar_stacked():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="uv",
            stack_id="1",
            fill=rx.color("accent", 8),
        ),
        rx.recharts.bar(
            data_key="pv",
            stack_id="1",
            fill=rx.color("green", 8),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.legend(),
        data=data,
        width="100%",
        height=300,
    )
```

## 范围图表

你也可以通过将 `rx.recharts.bar` 中的 data_key 赋值为包含两个元素的列表来为柱形指定范围，例如下面每个日期对应两个温度值的范围。

```python demo graphing
range_data = [
    {"day": "05-01", "temperature": [-1, 10]},
    {"day": "05-02", "temperature": [2, 15]},
    {"day": "05-03", "temperature": [3, 12]},
    {"day": "05-04", "temperature": [4, 12]},
    {"day": "05-05", "temperature": [12, 16]},
    {"day": "05-06", "temperature": [5, 16]},
    {"day": "05-07", "temperature": [3, 12]},
    {"day": "05-08", "temperature": [0, 8]},
    {"day": "05-09", "temperature": [-3, 5]},
]


def bar_range():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="temperature",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(data_key="day"),
        rx.recharts.y_axis(),
        data=range_data,
        width="100%",
        height=250,
    )
```

## 带状态的图表

下面是一个使用 `State` 的柱状图示例。我们定义了一个 `randomize_data` 函数，当使用 `on_click=BarState.randomize_data` 点击第一个定义的 `bar` 时，该函数会随机更改两个图表的数据。

```python demo exec
class BarState(rx.State):
    data = data

    @rx.event
    def randomize_data(self):
        for i in range(len(self.data)):
            self.data[i]["uv"] = random.randint(0, 10000)
            self.data[i]["pv"] = random.randint(0, 10000)
            self.data[i]["amt"] = random.randint(0, 10000)


def bar_with_state():
    return rx.recharts.bar_chart(
        rx.recharts.cartesian_grid(
            stroke_dasharray="3 3",
        ),
        rx.recharts.bar(
            data_key="uv",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
        ),
        rx.recharts.bar(
            data_key="pv",
            stroke=rx.color("green", 9),
            fill=rx.color("green", 8),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.legend(),
        on_click=BarState.randomize_data,
        data=BarState.data,
        width="100%",
        height=300,
    )
```

## 属性示例

以下示例演示了如何使用 `bar_category_gap`、`bar_gap`、`bar_size` 和 `max_bar_size` 属性来自定义柱形的外观和布局。这些属性接受像素值，用于控制柱形的间距和大小。

```python demo graphing
data = [
    {"name": "Page A", "value": 2400},
    {"name": "Page B", "value": 1398},
    {"name": "Page C", "value": 9800},
    {"name": "Page D", "value": 3908},
    {"name": "Page E", "value": 4800},
    {"name": "Page F", "value": 3800},
]


def bar_features():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="value",
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=data,
        bar_category_gap="15%",
        bar_gap=6,
        bar_size=100,
        max_bar_size=40,
        width="100%",
        height=300,
    )
```

## 垂直示例

`layout` 属性允许你将图表的方向设置为垂直或水平，默认为水平。设置 `layout="vertical"` 会使柱形从左到右延伸，这就是在 Reflex 中创建水平柱状图的方式。

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


def bar_vertical():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            data_key="uv",
            stroke=rx.color("accent", 8),
            fill=rx.color("accent", 3),
        ),
        rx.recharts.x_axis(type_="number"),
        rx.recharts.y_axis(data_key="name", type_="category"),
        data=data,
        layout="vertical",
        margin={"top": 20, "right": 20, "left": 20, "bottom": 20},
        width="100%",
        height=300,
    )
```

要了解如何使用 `sync_id`、`stack_id`、`x_axis_id` 和 `y_axis_id` 属性，请查看面积图的[文档](/docs/library/graphing/charts/areachart)，其中对这些属性都有详细的示例说明。

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [折线图](/docs/library/graphing/charts/linechart)
- [面积图](/docs/library/graphing/charts/areachart)
- [组合图](/docs/library/graphing/charts/composedchart)
