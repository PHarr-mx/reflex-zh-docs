---
components:
  - rx.recharts.ReferenceLine
  - rx.recharts.ReferenceDot
  - rx.recharts.ReferenceArea
---

# 参考线（Reference）

```python exec
import reflex as rx
```

Recharts 中的参考组件，包括 ReferenceLine、ReferenceArea 和 ReferenceDot，用于向图表添加视觉辅助和注释，帮助突出显示特定的数据点、范围或阈值，以便更好地解读和分析数据。

## 参考区域（Reference Area）

Recharts 中的 `rx.recharts.reference_area` 组件用于通过绘制矩形区域来突出显示图表上的特定区域或范围。它通过指定坐标（x1、x2、y1、y2）来定义，可用于强调图表上重要的数据范围或区间。

```python demo graphing
data = [
    {"x": 45, "y": 100, "z": 150, "errorY": [30, 20], "errorX": 5},
    {"x": 100, "y": 200, "z": 200, "errorY": [20, 30], "errorX": 3},
    {"x": 120, "y": 100, "z": 260, "errorY": 20, "errorX": [10, 3]},
    {"x": 170, "y": 300, "z": 400, "errorY": [15, 18], "errorX": 4},
    {"x": 140, "y": 250, "z": 280, "errorY": 23, "errorX": [6, 7]},
    {"x": 150, "y": 400, "z": 500, "errorY": [21, 10], "errorX": 4},
    {"x": 110, "y": 280, "z": 200, "errorY": 21, "errorX": [1, 8]},
]


def reference():
    return rx.recharts.scatter_chart(
        rx.recharts.scatter(data=data, fill="#8884d8", name="A"),
        rx.recharts.reference_area(
            x1=150, x2=180, y1=150, y2=300, fill="#8884d8", fill_opacity=0.3
        ),
        rx.recharts.x_axis(data_key="x", name="x", type_="number"),
        rx.recharts.y_axis(data_key="y", name="y", type_="number"),
        rx.recharts.graphing_tooltip(),
        width="100%",
        height=300,
    )
```

## 参考线（Reference Line）

`rx.recharts.reference_line` 组件用于在图表的指定位置绘制一条水平线或垂直线。它有助于突出显示轴上的重要值、阈值或范围，提供视觉参考点以便更好地解读数据。

```python demo graphing
data_2 = [
    {"name": "Page A", "uv": 4000, "pv": 2400, "amt": 2400},
    {"name": "Page B", "uv": 3000, "pv": 1398, "amt": 2210},
    {"name": "Page C", "uv": 2000, "pv": 9800, "amt": 2290},
    {"name": "Page D", "uv": 2780, "pv": 3908, "amt": 2000},
    {"name": "Page E", "uv": 1890, "pv": 4800, "amt": 2181},
    {"name": "Page F", "uv": 2390, "pv": 3800, "amt": 2500},
    {"name": "Page G", "uv": 3490, "pv": 4300, "amt": 2100},
]


def reference_line():
    return rx.recharts.area_chart(
        rx.recharts.area(
            data_key="pv",
            stroke=rx.color("accent", 8),
            fill=rx.color("accent", 7),
        ),
        rx.recharts.reference_line(
            x="Page C",
            stroke=rx.color("accent", 10),
            label="Max PV PAGE",
        ),
        rx.recharts.reference_line(y=9800, stroke=rx.color("green", 10), label="Max"),
        rx.recharts.reference_line(
            y=4343, stroke=rx.color("green", 10), label="Average"
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        data=data_2,
        height=300,
        width="100%",
    )
```

## 参考点（Reference Dot）

Recharts 中的 `rx.recharts.reference_dot` 组件用于在图表上用可自定义的圆点标记特定数据点。它允许你通过在图表上指定的坐标（x、y）处提供视觉参考标记来突出显示重要值、异常值或阈值。

```python demo graphing
data_3 = [
    {
        "x": 45,
        "y": 100,
        "z": 150,
    },
    {
        "x": 100,
        "y": 200,
        "z": 200,
    },
    {
        "x": 120,
        "y": 100,
        "z": 260,
    },
    {
        "x": 170,
        "y": 300,
        "z": 400,
    },
    {
        "x": 140,
        "y": 250,
        "z": 280,
    },
    {
        "x": 150,
        "y": 400,
        "z": 500,
    },
    {
        "x": 110,
        "y": 280,
        "z": 200,
    },
    {
        "x": 80,
        "y": 150,
        "z": 180,
    },
    {
        "x": 200,
        "y": 350,
        "z": 450,
    },
    {
        "x": 90,
        "y": 220,
        "z": 240,
    },
    {
        "x": 130,
        "y": 320,
        "z": 380,
    },
    {
        "x": 180,
        "y": 120,
        "z": 300,
    },
]


def reference_dot():
    return rx.recharts.scatter_chart(
        rx.recharts.scatter(
            data=data_3,
            fill=rx.color("accent", 9),
            name="A",
        ),
        rx.recharts.x_axis(data_key="x", name="x", type_="number"),
        rx.recharts.y_axis(data_key="y", name="y", type_="number"),
        rx.recharts.reference_dot(
            x=160,
            y=350,
            r=15,
            fill=rx.color("accent", 5),
            stroke=rx.color("accent", 10),
        ),
        rx.recharts.reference_dot(
            x=170,
            y=300,
            r=20,
            fill=rx.color("accent", 7),
        ),
        rx.recharts.reference_dot(
            x=90,
            y=220,
            r=18,
            fill=rx.color("green", 7),
        ),
        height=200,
        width="100%",
    )
```
