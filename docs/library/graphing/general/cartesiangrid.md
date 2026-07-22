---
components:
  - rx.recharts.CartesianGrid
  # - rx.recharts.CartesianAxis
---

```python exec
import reflex as rx
```

# 笛卡尔网格（Cartesian Grid）

笛卡尔网格是 Recharts 中的一个组件，为图表中的数据点提供视觉参考。它通过在图表区域添加水平线和垂直线，帮助用户更好地解读数据。

## 简单示例

Recharts 中的 `stroke_dasharray` 属性用于为折线、坐标轴或网格等各种图表元素创建虚线或点线。它基于 SVG 的 stroke-dasharray 属性。`stroke_dasharray` 属性接受一个以逗号分隔的数字字符串，定义沿描边长度重复的虚线和间隔模式。

- `stroke_dasharray="5,5"`：创建 5 像素虚线和 5 像素间隔的线条
- `stroke_dasharray="10,5,5,5"`：创建更复杂的模式，包含 10 像素虚线、5 像素间隔、5 像素虚线和 5 像素间隔

以下是在 Line 组件上使用它的简单示例：

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


def cgrid_simple():
    return rx.recharts.line_chart(
        rx.recharts.line(
            data_key="pv",
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.cartesian_grid(stroke_dasharray="4 4"),
        data=data,
        width="100%",
        height=300,
    )
```

## 隐藏网格线

通过将 `cartesian_grid` 组件的 `horizontal` 和 `vertical` 属性设置为 `False`，可以隐藏图表中的水平和垂直网格线。当你只想在一个轴上显示网格线，或者想为图表创建更简洁的外观时，这会很有用。

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


def cgrid_hidden():
    return rx.recharts.area_chart(
        rx.recharts.area(data_key="uv", stroke="#8884d8", fill="#8884d8"),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.cartesian_grid(
            stroke_dasharray="2 4",
            vertical=False,
            horizontal=True,
        ),
        data=data,
        width="100%",
        height=300,
    )
```

## 自定义网格线

`horizontal_points` 和 `vertical_points` 属性允许你在图表上指定自定义网格线，提供对网格外观的精细控制。

这些属性接受数字数组，其中每个数字代表一个像素偏移量：

- 对于 `horizontal_points`，偏移量从图表顶部边缘开始测量
- 对于 `vertical_points`，偏移量从图表左侧边缘开始测量

```md alert info
# **重要**：提供给这些属性的值与轴值没有直接关系。它们代表图表渲染区域内的像素偏移量。
```

以下是在散点图中演示自定义网格线的示例：

```python demo graphing
data2 = [
    {"x": 100, "y": 200, "z": 200},
    {"x": 120, "y": 100, "z": 260},
    {"x": 170, "y": 300, "z": 400},
    {"x": 170, "y": 250, "z": 280},
    {"x": 150, "y": 400, "z": 500},
    {"x": 110, "y": 280, "z": 200},
    {"x": 200, "y": 150, "z": 300},
    {"x": 130, "y": 350, "z": 450},
    {"x": 90, "y": 220, "z": 180},
    {"x": 180, "y": 320, "z": 350},
    {"x": 140, "y": 230, "z": 320},
    {"x": 160, "y": 180, "z": 240},
]


def cgrid_custom():
    return rx.recharts.scatter_chart(
        rx.recharts.scatter(
            data=data2,
            fill="#8884d8",
        ),
        rx.recharts.x_axis(data_key="x", type_="number"),
        rx.recharts.y_axis(data_key="y"),
        rx.recharts.cartesian_grid(
            stroke_dasharray="3 3",
            horizontal_points=[0, 25, 50],
            vertical_points=[65, 90, 115],
        ),
        width="100%",
        height=200,
    )
```

请谨慎使用这些属性来增强数据可视化效果，同时避免使图表过于杂乱。它们对于突出显示特定数据范围或创建视觉参考点特别有用。
