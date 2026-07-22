---
components:
  - rx.recharts.ErrorBar
title: Error Bar
meta_description: "Add error bars to your charts in Python with Reflex. Use Recharts error bars to visualize uncertainty and variance on scatter and line charts — all in pure Python, no JavaScript."
---

```python exec
import reflex as rx
```

# 误差条（Error Bar）

Reflex 中的误差条基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。误差条是图表中数据点不确定性或变异性的图形表示，表现为从数据点沿某一坐标轴方向延伸的线段。`data_key`、`width`、`stroke_width`、`stroke` 和 `direction` 属性可用于自定义误差条的外观和行为，分别指定数据源、尺寸、颜色和方向。

```python demo graphing
data = [
    {"x": 45, "y": 100, "z": 150, "errorY": [30, 20], "errorX": 5},
    {"x": 100, "y": 200, "z": 200, "errorY": [20, 30], "errorX": 3},
    {"x": 120, "y": 100, "z": 260, "errorY": 20, "errorX": [5, 3]},
    {"x": 170, "y": 300, "z": 400, "errorY": [15, 18], "errorX": 4},
    {"x": 140, "y": 250, "z": 280, "errorY": 23, "errorX": [6, 7]},
    {"x": 150, "y": 400, "z": 500, "errorY": [21, 10], "errorX": 4},
    {"x": 110, "y": 280, "z": 200, "errorY": 21, "errorX": [5, 6]},
]


def error():
    return rx.recharts.scatter_chart(
        rx.recharts.scatter(
            rx.recharts.error_bar(
                data_key="errorY", direction="y", width=4, stroke_width=2, stroke="red"
            ),
            rx.recharts.error_bar(
                data_key="errorX", direction="x", width=4, stroke_width=2
            ),
            data=data,
            fill="#8884d8",
            name="A",
        ),
        rx.recharts.x_axis(data_key="x", name="x", type_="number"),
        rx.recharts.y_axis(data_key="y", name="y", type_="number"),
        width="100%",
        height=300,
    )
```

## 对称与非对称误差

`rx.recharts.error_bar()` 的 `data_key` 指向每行数据中的一个字段，该字段控制须线的延伸距离。使用单个数字表示**对称**误差——数据点上下延伸相同的距离——或使用包含两个元素的 `[low, high]` 列表表示**非对称**误差。在上面的示例中，某些行使用 `"errorY": 20`（对称），而其他行使用 `"errorY": [30, 20]`（非对称）。`direction` 属性（`"x"` 或 `"y"`）选择须线沿哪个轴延伸，因此你可以同时在一个或两个轴上显示误差。

## 折线图上的误差条

误差条不仅限于散点图。将 `rx.recharts.error_bar()` 作为 `rx.recharts.line()` 的子组件添加，可以在折线图上可视化方差或测量不确定性——适用于平均温度或基准测试结果等时间序列数据。

```python demo graphing
data = [
    {"month": "Jan", "temp": 7, "error": [2, 3]},
    {"month": "Feb", "temp": 9, "error": 2},
    {"month": "Mar", "temp": 12, "error": [3, 2]},
    {"month": "Apr", "temp": 16, "error": 3},
    {"month": "May", "temp": 20, "error": [2, 4]},
    {"month": "Jun", "temp": 24, "error": 3},
]


def line_error():
    return rx.recharts.line_chart(
        rx.recharts.line(
            rx.recharts.error_bar(
                data_key="error",
                direction="y",
                width=4,
                stroke_width=2,
                stroke=rx.color("accent", 9),
            ),
            data_key="temp",
            stroke=rx.color("accent", 9),
        ),
        rx.recharts.x_axis(data_key="month"),
        rx.recharts.y_axis(),
        data=data,
        width="100%",
        height=300,
    )
```

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [散点图](/docs/library/graphing/charts/scatterchart)
- [折线图](/docs/library/graphing/charts/linechart)
- [柱状图](/docs/library/graphing/charts/barchart)
