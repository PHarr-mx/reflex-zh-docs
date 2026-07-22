---
components:
  - rx.recharts.Legend
---

# 图例（Legend）

```python exec
import reflex as rx
```

图例说明每个图形代表什么。就像地图上的图例一样，它帮助读者理解所看到的内容。例如，对于折线图，图例告诉我们每条线代表什么。

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


def legend_simple():
    return rx.recharts.composed_chart(
        rx.recharts.area(data_key="uv", stroke="#8884d8", fill="#8884d8"),
        rx.recharts.bar(data_key="amt", bar_size=20, fill="#413ea0"),
        rx.recharts.line(data_key="pv", type_="monotone", stroke="#ff7300"),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.legend(),
        data=data,
        width="100%",
        height=300,
    )
```

## 属性示例

图例的样式和布局可以使用一组属性进行自定义。`width` 和 `height` 设置包裹图例的容器的尺寸，`layout` 可以将图例设置为垂直或水平显示。`align` 和 `vertical_align` 设置相对于图表容器的位置。图标的类型和大小可以使用 `icon_size` 和 `icon_type` 来设置。

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


def legend_props():
    return rx.recharts.composed_chart(
        rx.recharts.line(
            data_key="pv",
            type_="monotone",
            stroke=rx.color("accent", 7),
        ),
        rx.recharts.line(
            data_key="amt",
            type_="monotone",
            stroke=rx.color("green", 7),
        ),
        rx.recharts.line(
            data_key="uv",
            type_="monotone",
            stroke=rx.color("red", 7),
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.legend(
            width=60,
            height=100,
            layout="vertical",
            align="right",
            vertical_align="top",
            icon_size=15,
            icon_type="square",
        ),
        data=data,
        width="100%",
        height=300,
    )
```
