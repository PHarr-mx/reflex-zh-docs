---
components:
  - rx.recharts.ScatterChart
  - rx.recharts.Scatter
title: Scatter Chart
meta_description: "Create scatter plots and bubble charts in Python with Reflex. Build interactive Recharts scatter charts with multiple series, custom axes, sizing, and tooltips — all in pure Python."
---

# 散点图（Scatter Chart）

```python exec
import reflex as rx
```

Reflex 中的散点图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。散点图（或散点图表）始终有两个值轴，用于沿水平（值）轴显示一组数值数据，沿垂直（值）轴显示另一组数值。图表在 x 和 y 数值的交点处显示点，将这些值组合为单个数据点。

## 简单示例

对于散点图，我们必须为每组要绘制的值定义一个 `rx.recharts.scatter()` 组件。每个 `rx.recharts.scatter()` 组件都有一个 `data` 属性，用于明确指出我们绘制的数据源。我们还必须定义 `rx.recharts.x_axis()` 和 `rx.recharts.y_axis()`，以便图表知道在每个轴上绘制什么数据。

```python demo graphing
data01 = [
    {"x": 100, "y": 200, "z": 200},
    {"x": 120, "y": 100, "z": 260},
    {"x": 170, "y": 300, "z": 400},
    {"x": 170, "y": 250, "z": 280},
    {"x": 150, "y": 400, "z": 500},
    {"x": 110, "y": 280, "z": 200},
]


def scatter_simple():
    return rx.recharts.scatter_chart(
        rx.recharts.scatter(
            data=data01,
            fill="#8884d8",
        ),
        rx.recharts.x_axis(data_key="x", type_="number"),
        rx.recharts.y_axis(data_key="y"),
        width="100%",
        height=300,
    )
```

## 多个散点

我们还可以通过使用两个 `rx.recharts.scatter()` 组件在同一个图表上添加两个散点，并且可以定义一个 `rx.recharts.z_axis()`，它代表第三列数据，并通过散点图中点的大小来表示。

```python demo graphing
data01 = [
    {"x": 100, "y": 200, "z": 200},
    {"x": 120, "y": 100, "z": 260},
    {"x": 170, "y": 300, "z": 400},
    {"x": 170, "y": 250, "z": 280},
    {"x": 150, "y": 350, "z": 500},
    {"x": 110, "y": 280, "z": 200},
]

data02 = [
    {"x": 200, "y": 260, "z": 240},
    {"x": 240, "y": 290, "z": 220},
    {"x": 190, "y": 290, "z": 250},
    {"x": 198, "y": 250, "z": 210},
    {"x": 180, "y": 280, "z": 260},
    {"x": 210, "y": 220, "z": 230},
]


def scatter_double():
    return rx.recharts.scatter_chart(
        rx.recharts.scatter(data=data01, fill="#8884d8", name="A"),
        rx.recharts.scatter(data=data02, fill="#82ca9d", name="B"),
        rx.recharts.cartesian_grid(stroke_dasharray="3 3"),
        rx.recharts.x_axis(data_key="x", type_="number"),
        rx.recharts.y_axis(data_key="y"),
        rx.recharts.z_axis(data_key="z", range=[60, 400], name="score"),
        rx.recharts.legend(),
        rx.recharts.graphing_tooltip(),
        width="100%",
        height=300,
    )
```

要了解如何使用 `x_axis_id` 和 `y_axis_id` 属性，请查看面积图[文档](/docs/library/graphing/charts/areachart)中的多坐标轴部分。

## 动态数据

绑定到 State 变量的图表数据会在状态变化时自动更新，提供了一种根据用户界面元素可视化数据的好方法。查看 "Data" 选项卡以了解驱动此 Collatz 猜想迭代计算的子状态。在图表下方的输入框中输入一个起始数字即可重新计算。

```python demo exec
class ScatterChartState(rx.State):
    data: list[dict[str, int]] = []

    @rx.event
    def compute_collatz(self, form_data: dict) -> int:
        n = int(form_data.get("start") or 1)
        yield rx.set_value("start", "")
        self.data = []
        for ix in range(400):
            self.data.append({"x": ix, "y": n})
            if n == 1:
                break
            if n % 2 == 0:
                n = n // 2
            else:
                n = 3 * n + 1


def scatter_dynamic():
    return rx.vstack(
        rx.recharts.scatter_chart(
            rx.recharts.scatter(
                data=ScatterChartState.data,
                fill="#8884d8",
            ),
            rx.recharts.x_axis(data_key="x", type_="number"),
            rx.recharts.y_axis(data_key="y", type_="number"),
        ),
        rx.form.root(
            rx.input(placeholder="Enter a number", id="start"),
            rx.button("Compute", type="submit"),
            on_submit=ScatterChartState.compute_collatz,
        ),
        width="100%",
        height="15em",
        on_mount=ScatterChartState.compute_collatz({"start": "15"}),
    )
```

## 图例类型和形状

```python demo exec
class ScatterChartState2(rx.State):
    legend_types: list[str] = [
        "square",
        "circle",
        "cross",
        "diamond",
        "star",
        "triangle",
        "wye",
    ]

    legend_type: str = "circle"

    shapes: list[str] = [
        "square",
        "circle",
        "cross",
        "diamond",
        "star",
        "triangle",
        "wye",
    ]

    shape: str = "circle"

    data01 = [
        {"x": 100, "y": 200, "z": 200},
        {"x": 120, "y": 100, "z": 260},
        {"x": 170, "y": 300, "z": 400},
        {"x": 170, "y": 250, "z": 280},
        {"x": 150, "y": 400, "z": 500},
        {"x": 110, "y": 280, "z": 200},
    ]

    @rx.event
    def set_shape(self, shape: str):
        self.shape = shape

    @rx.event
    def set_legend_type(self, legend_type: str):
        self.legend_type = legend_type


def scatter_shape():
    return rx.vstack(
        rx.recharts.scatter_chart(
            rx.recharts.scatter(
                data=data01,
                fill="#8884d8",
                legend_type=ScatterChartState2.legend_type,
                shape=ScatterChartState2.shape,
            ),
            rx.recharts.x_axis(data_key="x", type_="number"),
            rx.recharts.y_axis(data_key="y"),
            rx.recharts.legend(),
            width="100%",
            height=300,
        ),
        rx.hstack(
            rx.text("Legend Type: "),
            rx.select(
                ScatterChartState2.legend_types,
                value=ScatterChartState2.legend_type,
                on_change=ScatterChartState2.set_legend_type,
            ),
            rx.text("Shape: "),
            rx.select(
                ScatterChartState2.shapes,
                value=ScatterChartState2.shape,
                on_change=ScatterChartState2.set_shape,
            ),
        ),
        width="100%",
    )
```

## 气泡图（Bubble Chart）

添加 `rx.recharts.z_axis()` 可将散点图变为气泡图：每个点的 `z` 值控制气泡大小，映射到像素 `range`。这让你可以在 x 和 y 位置之外编码第三维数据。

```python demo graphing
data = [
    {"x": 100, "y": 200, "z": 200},
    {"x": 120, "y": 100, "z": 260},
    {"x": 170, "y": 300, "z": 400},
    {"x": 140, "y": 250, "z": 280},
    {"x": 150, "y": 400, "z": 500},
    {"x": 110, "y": 280, "z": 200},
]


def bubble_chart():
    return rx.recharts.scatter_chart(
        rx.recharts.scatter(
            data=data,
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(data_key="x", name="x", type_="number"),
        rx.recharts.y_axis(data_key="y", name="y", type_="number"),
        rx.recharts.z_axis(data_key="z", range=[60, 400], name="size"),
        rx.recharts.graphing_tooltip(),
        width="100%",
        height=300,
    )
```

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [折线图](/docs/library/graphing/charts/linechart)
- [柱状图](/docs/library/graphing/charts/barchart)
- [误差条](/docs/library/graphing/charts/errorbar)
