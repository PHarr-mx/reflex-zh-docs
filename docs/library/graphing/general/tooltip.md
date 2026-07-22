---
components:
  - rx.recharts.GraphingTooltip
---

# 工具提示（Tooltip）

```python exec
import reflex as rx
```

工具提示是当你将鼠标悬停在某个元素上时弹出的小方框。工具提示始终附着在某个元素上，例如散点图上的点或柱状图上的柱形。

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


def tooltip_simple():
    return rx.recharts.composed_chart(
        rx.recharts.area(data_key="uv", stroke="#8884d8", fill="#8884d8"),
        rx.recharts.bar(data_key="amt", bar_size=20, fill="#413ea0"),
        rx.recharts.line(data_key="pv", type_="monotone", stroke="#ff7300"),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.cartesian_grid(stroke_dasharray="3 3"),
        rx.recharts.graphing_tooltip(),
        data=data,
        width="100%",
        height=300,
    )
```

## 自定义样式

`rx.recharts.graphing_tooltip` 组件允许自定义工具提示的样式、位置和布局。`separator` 设置数据键和值之间的分隔符。`view_box` 属性定义图表视口的尺寸，而 `allow_escape_view_box` 决定工具提示是否可以在水平方向（x）或垂直方向（y）上超出 viewBox。`wrapper_style` 属性允许你设置工具提示外部容器或包装器的样式。`content_style` 属性允许你设置工具提示内部内容区域的样式。`is_animation_active` 属性决定工具提示动画是否激活。

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


def tooltip_custom_styling():
    return rx.recharts.composed_chart(
        rx.recharts.area(data_key="uv", stroke="#8884d8", fill="#8884d8"),
        rx.recharts.bar(data_key="amt", bar_size=20, fill="#413ea0"),
        rx.recharts.line(data_key="pv", type_="monotone", stroke="#ff7300"),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(),
        rx.recharts.graphing_tooltip(
            separator=" - ",
            view_box={"width": 675, " height": 300},
            allow_escape_view_box={"x": True, "y": False},
            wrapper_style={
                "backgroundColor": rx.color("accent", 3),
                "borderRadius": "8px",
                "padding": "10px",
            },
            content_style={
                "backgroundColor": rx.color("accent", 4),
                "borderRadius": "4px",
                "padding": "8px",
            },
            position={"x": 600, "y": 0},
            is_animation_active=False,
        ),
        data=data,
        height=300,
        width="100%",
    )
```
