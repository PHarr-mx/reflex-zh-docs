---
components:
  - rx.recharts.XAxis
  - rx.recharts.YAxis
  - rx.recharts.ZAxis
---

```python exec
import reflex as rx
```

# 坐标轴（Axis）

Recharts 中的坐标轴组件是一个强大的工具，用于自定义和配置图表的坐标轴。它提供了丰富的属性，允许你控制坐标轴的外观、行为和格式。无论你使用的是 AreaChart、LineChart 还是任何其他图表类型，坐标轴组件都能帮助你创建精确且信息丰富的可视化效果。

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


def axis_simple():
    return rx.recharts.area_chart(
        rx.recharts.area(
            data_key="uv",
            stroke=rx.color("accent", 9),
            fill=rx.color("accent", 8),
        ),
        rx.recharts.x_axis(
            data_key="name",
            label={"value": "Pages", "position": "bottom"},
        ),
        rx.recharts.y_axis(
            data_key="uv",
            label={"value": "Views", "angle": -90, "position": "left"},
        ),
        data=data,
        width="100%",
        height=300,
        margin={
            "bottom": 40,
            "left": 40,
            "right": 40,
        },
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


def multi_axis():
    return rx.recharts.area_chart(
        rx.recharts.area(
            data_key="uv",
            stroke="#8884d8",
            fill="#8884d8",
            y_axis_id="left",
        ),
        rx.recharts.area(
            data_key="pv",
            y_axis_id="right",
            type_="monotone",
            stroke="#82ca9d",
            fill="#82ca9d",
        ),
        rx.recharts.x_axis(data_key="name"),
        rx.recharts.y_axis(data_key="uv", y_axis_id="left"),
        rx.recharts.y_axis(data_key="pv", y_axis_id="right", orientation="right"),
        rx.recharts.graphing_tooltip(),
        rx.recharts.legend(),
        data=data,
        width="100%",
        height=300,
    )
```

## 选择坐标轴标签的位置

坐标轴的 `label` 可以设置多个位置。下面的示例允许你尝试 x 轴和 y 轴标签的不同位置。

```python demo graphing
class AxisState(rx.State):
    label_positions: list[str] = [
        "center",
        "insideTopLeft",
        "insideTopRight",
        "insideBottomRight",
        "insideBottomLeft",
        "insideTop",
        "insideBottom",
        "insideLeft",
        "insideRight",
        "outside",
        "top",
        "bottom",
        "left",
        "right",
    ]

    label_offsets: list[str] = ["-30", "-20", "-10", "0", "10", "20", "30"]

    x_axis_position: str = "bottom"

    x_axis_offset: int

    y_axis_position: str = "left"

    y_axis_offset: int

    @rx.event
    def set_y_axis_position(self, position: str):
        self.y_axis_position = position

    @rx.event
    def set_x_axis_position(self, position: str):
        self.x_axis_position = position

    @rx.event
    def set_x_axis_offset(self, offset: str):
        self.x_axis_offset = int(offset)

    @rx.event
    def set_y_axis_offset(self, offset: str):
        self.y_axis_offset = int(offset)


data = [
    {"name": "Page A", "uv": 4000, "pv": 2400, "amt": 2400},
    {"name": "Page B", "uv": 3000, "pv": 1398, "amt": 2210},
    {"name": "Page C", "uv": 2000, "pv": 9800, "amt": 2290},
    {"name": "Page D", "uv": 2780, "pv": 3908, "amt": 2000},
    {"name": "Page E", "uv": 1890, "pv": 4800, "amt": 2181},
    {"name": "Page F", "uv": 2390, "pv": 3800, "amt": 2500},
    {"name": "Page G", "uv": 3490, "pv": 4300, "amt": 2100},
]


def axis_labels():
    return rx.vstack(
        rx.recharts.area_chart(
            rx.recharts.area(
                data_key="uv",
                stroke=rx.color("accent", 9),
                fill=rx.color("accent", 8),
            ),
            rx.recharts.x_axis(
                data_key="name",
                label={
                    "value": "Pages",
                    "position": AxisState.x_axis_position,
                    "offset": AxisState.x_axis_offset,
                },
            ),
            rx.recharts.y_axis(
                data_key="uv",
                label={
                    "value": "Views",
                    "angle": -90,
                    "position": AxisState.y_axis_position,
                    "offset": AxisState.y_axis_offset,
                },
            ),
            data=data,
            width="100%",
            height=300,
            margin={
                "bottom": 40,
                "left": 40,
                "right": 40,
            },
        ),
        rx.hstack(
            rx.text("X Label Position: "),
            rx.select(
                AxisState.label_positions,
                value=AxisState.x_axis_position,
                on_change=AxisState.set_x_axis_position,
            ),
            rx.text("X Label Offset: "),
            rx.select(
                AxisState.label_offsets,
                value=AxisState.x_axis_offset.to_string(),
                on_change=AxisState.set_x_axis_offset,
            ),
            rx.text("Y Label Position: "),
            rx.select(
                AxisState.label_positions,
                value=AxisState.y_axis_position,
                on_change=AxisState.set_y_axis_position,
            ),
            rx.text("Y Label Offset: "),
            rx.select(
                AxisState.label_offsets,
                value=AxisState.y_axis_offset.to_string(),
                on_change=AxisState.set_y_axis_offset,
            ),
        ),
        width="100%",
    )
```
