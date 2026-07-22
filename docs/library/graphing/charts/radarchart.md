---
components:
  - rx.recharts.RadarChart
  - rx.recharts.Radar
title: Radar Chart
meta_description: "Build radar (spider) charts in Python with Reflex. Create interactive Recharts radar charts with multiple series, custom polar axes, legends, and tooltips — all in pure Python."
---

# 雷达图（Radar Chart）

```python exec
import reflex as rx
from typing import Any
```

Reflex 中的雷达图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。雷达图（也称为蜘蛛图）展示三个或更多定量变量的多变量数据，将它们映射到坐标轴上。

## 简单示例

对于雷达图，我们必须为每组要绘制的值定义一个 `rx.recharts.radar()` 组件。每个 `rx.recharts.radar()` 组件都有一个 `data_key`，用于明确指出我们正在绘制数据中的哪个变量。在这个简单示例中，我们将数据的 `A` 列绘制出来，并将 `subject` 列设置为 `rx.recharts.polar_angle_axis` 中的 `data_key`。

```python demo graphing
data = [
    {"subject": "Math", "A": 120, "B": 110, "fullMark": 150},
    {"subject": "Chinese", "A": 98, "B": 130, "fullMark": 150},
    {"subject": "English", "A": 86, "B": 130, "fullMark": 150},
    {"subject": "Geography", "A": 99, "B": 100, "fullMark": 150},
    {"subject": "Physics", "A": 85, "B": 90, "fullMark": 150},
    {"subject": "History", "A": 65, "B": 85, "fullMark": 150},
]


def radar_simple():
    return rx.recharts.radar_chart(
        rx.recharts.radar(
            data_key="A",
            stroke="#8884d8",
            fill="#8884d8",
        ),
        rx.recharts.polar_grid(),
        rx.recharts.polar_angle_axis(data_key="subject"),
        rx.recharts.polar_radius_axis(angle=90, domain=[0, 150]),
        data=data,
        width="100%",
        height=300,
    )
```

## 多个雷达

我们还可以通过使用两个 `rx.recharts.radar` 组件在同一个图表上添加两个雷达。

在这个图中设置了 `inner_radius` 和 `outer_radius`，它们决定了图表的大小和形状。`inner_radius` 设置从中心到图表最内层的距离（如果大于零则创建空心中心），而 `outer_radius` 通过设置从中心到雷达图最外边缘的距离来定义图表的整体大小。

```python demo graphing
data = [
    {"subject": "Math", "A": 120, "B": 110, "fullMark": 150},
    {"subject": "Chinese", "A": 98, "B": 130, "fullMark": 150},
    {"subject": "English", "A": 86, "B": 130, "fullMark": 150},
    {"subject": "Geography", "A": 99, "B": 100, "fullMark": 150},
    {"subject": "Physics", "A": 85, "B": 90, "fullMark": 150},
    {"subject": "History", "A": 65, "B": 85, "fullMark": 150},
]


def radar_multiple():
    return rx.recharts.radar_chart(
        rx.recharts.radar(
            data_key="A",
            stroke="#8884d8",
            fill="#8884d8",
        ),
        rx.recharts.radar(
            data_key="B",
            stroke="#82ca9d",
            fill="#82ca9d",
            fill_opacity=0.6,
        ),
        rx.recharts.polar_grid(),
        rx.recharts.polar_angle_axis(data_key="subject"),
        rx.recharts.polar_radius_axis(angle=90, domain=[0, 150]),
        rx.recharts.legend(),
        data=data,
        inner_radius="15%",
        outer_radius="80%",
        width="100%",
        height=300,
    )
```

## 使用更多属性

`dot` 属性为 true 时在每个数据顶点显示点。`legend_type="line"` 在图表图例中显示一条线。`animation_begin=0` 立即开始动画，`animation_duration=8000` 设置 8 秒的动画时长，`animation_easing="ease-in"` 使动画从慢到快。这些属性控制图表的外观和动画行为。

```python demo graphing
data = [
    {"subject": "Math", "A": 120, "B": 110, "fullMark": 150},
    {"subject": "Chinese", "A": 98, "B": 130, "fullMark": 150},
    {"subject": "English", "A": 86, "B": 130, "fullMark": 150},
    {"subject": "Geography", "A": 99, "B": 100, "fullMark": 150},
    {"subject": "Physics", "A": 85, "B": 90, "fullMark": 150},
    {"subject": "History", "A": 65, "B": 85, "fullMark": 150},
]


def radar_start_end():
    return rx.recharts.radar_chart(
        rx.recharts.radar(
            data_key="A",
            dot=True,
            stroke="#8884d8",
            fill="#8884d8",
            fill_opacity=0.6,
            legend_type="line",
            animation_begin=0,
            animation_duration=8000,
            animation_easing="ease-in",
        ),
        rx.recharts.polar_grid(),
        rx.recharts.polar_angle_axis(data_key="subject"),
        rx.recharts.polar_radius_axis(angle=90, domain=[0, 150]),
        rx.recharts.legend(),
        data=data,
        width="100%",
        height=300,
    )
```

## 动态数据

绑定到 State 变量的图表数据会在状态变化时自动更新，提供了一种根据用户界面元素可视化数据的好方法。查看 "Data" 选项卡以了解驱动此角色特质雷达图的子状态。

```python demo exec
class RadarChartState(rx.State):
    total_points: int = 100
    traits: list[dict[str, Any]] = [
        dict(trait="Strength", value=15),
        dict(trait="Dexterity", value=15),
        dict(trait="Constitution", value=15),
        dict(trait="Intelligence", value=15),
        dict(trait="Wisdom", value=15),
        dict(trait="Charisma", value=15),
    ]

    @rx.var
    def remaining_points(self) -> int:
        return self.total_points - sum(t["value"] for t in self.traits)

    @rx.var(cache=True)
    def trait_names(self) -> list[str]:
        return [t["trait"] for t in self.traits]

    @rx.event
    def set_trait(self, trait: str, value: int):
        for t in self.traits:
            if t["trait"] == trait:
                available_points = self.remaining_points + t["value"]
                value = min(value, available_points)
                t["value"] = value
                break


def radar_dynamic():
    return rx.hstack(
        rx.recharts.radar_chart(
            rx.recharts.radar(
                data_key="value",
                stroke="#8884d8",
                fill="#8884d8",
            ),
            rx.recharts.polar_grid(),
            rx.recharts.polar_angle_axis(data_key="trait"),
            data=RadarChartState.traits,
        ),
        rx.vstack(
            rx.foreach(
                RadarChartState.trait_names,
                lambda trait_name, i: rx.hstack(
                    rx.text(trait_name, width="7em"),
                    rx.slider(
                        default_value=RadarChartState.traits[i]["value"].to(int),
                        on_change=lambda value: RadarChartState.set_trait(
                            trait_name, value[0]
                        ),
                        width="25vw",
                    ),
                    rx.text(RadarChartState.traits[i]["value"]),
                ),
            ),
            rx.text("Remaining points: ", RadarChartState.remaining_points),
        ),
        width="100%",
        height="15em",
    )
```

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [径向柱状图](/docs/library/graphing/charts/radialbarchart)
- [饼图](/docs/library/graphing/charts/piechart)
- [折线图](/docs/library/graphing/charts/linechart)
