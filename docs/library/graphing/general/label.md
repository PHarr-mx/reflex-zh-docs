---
components:
  - rx.recharts.Label
  - rx.recharts.LabelList
---

# 标签（Label）

```python exec
import reflex as rx
```

Label 是一个用于在图表或坐标轴的特定位置显示单个标签的组件，而 LabelList 是一个自动为图表系列中的每个数据点渲染标签列表的组件，提供了一种便捷的方式来显示多个标签，无需手动定位每个标签。

## 简单示例

以下是一个简单示例，演示如何使用 `rx.recharts.label` 自定义坐标轴的标签。`value` 属性表示标签的实际文本，`position` 属性指定标签在坐标轴组件中的位置，`offset` 属性用于微调标签的位置。

```python demo graphing
data = [
    {"name": "Page A", "uv": 4000, "pv": 2400, "amt": 2400},
    {"name": "Page B", "uv": 3000, "pv": 1398, "amt": 2210},
    {"name": "Page C", "uv": 2000, "pv": 5800, "amt": 2290},
    {"name": "Page D", "uv": 2780, "pv": 3908, "amt": 2000},
    {"name": "Page E", "uv": 1890, "pv": 4800, "amt": 2181},
    {"name": "Page F", "uv": 2390, "pv": 3800, "amt": 2500},
    {"name": "Page G", "uv": 3490, "pv": 4300, "amt": 2100},
]


def label_simple():
    return rx.recharts.bar_chart(
        rx.recharts.cartesian_grid(stroke_dasharray="3 3"),
        rx.recharts.bar(
            rx.recharts.label_list(data_key="uv", position="top"),
            data_key="uv",
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(
            rx.recharts.label(
                value="center",
                position="center",
                offset=30,
            ),
            rx.recharts.label(
                value="inside left",
                position="insideLeft",
                offset=10,
            ),
            rx.recharts.label(
                value="inside right",
                position="insideRight",
                offset=10,
            ),
            height=50,
        ),
        data=data,
        margin={
            "left": 20,
            "right": 20,
            "top": 20,
            "bottom": 20,
        },
        width="100%",
        height=250,
    )
```

## 标签列表示例

`rx.recharts.label_list` 接收一个 `data_key`，用于定义要绘制的数据列。

```python demo graphing
data = [
    {"name": "Page A", "uv": 4000, "pv": 2400, "amt": 2400},
    {"name": "Page B", "uv": 3000, "pv": 1398, "amt": 2210},
    {"name": "Page C", "uv": 2000, "pv": 5800, "amt": 2290},
    {"name": "Page D", "uv": 2780, "pv": 3908, "amt": 2000},
    {"name": "Page E", "uv": 1890, "pv": 4800, "amt": 2181},
    {"name": "Page F", "uv": 2390, "pv": 3800, "amt": 2500},
    {"name": "Page G", "uv": 3490, "pv": 4300, "amt": 2100},
]


def label_list():
    return rx.recharts.bar_chart(
        rx.recharts.bar(
            rx.recharts.label_list(data_key="uv", position="top"),
            data_key="uv",
            stroke="#8884d8",
            fill="#8884d8",
        ),
        rx.recharts.bar(
            rx.recharts.label_list(data_key="pv", position="top"),
            data_key="pv",
            stroke="#82ca9d",
            fill="#82ca9d",
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        margin={"left": 10, "right": 0, "top": 20, "bottom": 10},
        data=data,
        width="100%",
        height=300,
    )
```
