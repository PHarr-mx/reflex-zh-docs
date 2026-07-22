---
components:
  - rx.recharts.RadialBarChart
title: Radial Bar Chart
meta_description: "Create radial bar and gauge charts in Python with Reflex. Build circular Recharts radial bar charts with custom colors, start and end angles, and legends — all in pure Python."
---

# 径向柱状图（Radial Bar Chart）

```python exec
import reflex as rx
```

Reflex 中的径向柱状图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。径向柱状图是一种圆形可视化图表，数据类别由从中心点向外延伸的条形表示，每个条形的长度与其值成正比。

## 简单示例

此示例演示了如何将 `radial_bar_chart` 与 `radial_bar` 一起使用。`radial_bar_chart` 接收 `data`，然后 `radial_bar` 接收一个 `data_key` 来命名每个条形所代表的值。`min_angle` 属性为每个条形设置最小扫描角度，使得即使较小的值在圆环上也保持可见。

```md alert info
# 填充颜色支持 `rx.color()`，它会自动适应深色/浅色模式的变化。
```

```python demo graphing
data = [
    {"name": "C", "x": 3, "fill": rx.color("cyan", 9)},
    {"name": "D", "x": 4, "fill": rx.color("blue", 9)},
    {"name": "E", "x": 5, "fill": rx.color("orange", 9)},
    {"name": "F", "x": 6, "fill": rx.color("red", 9)},
    {"name": "G", "x": 7, "fill": rx.color("gray", 9)},
    {"name": "H", "x": 8, "fill": rx.color("green", 9)},
    {"name": "I", "x": 9, "fill": rx.color("accent", 6)},
]


def radial_bar_simple():
    return rx.recharts.radial_bar_chart(
        rx.recharts.radial_bar(
            data_key="x",
            min_angle=15,
        ),
        data=data,
        width="100%",
        height=500,
    )
```

## 高级示例

`start_angle` 和 `end_angle` 定义了条形分布的圆弧范围，而 `inner_radius` 和 `outer_radius` 决定了条形从中心向外延伸的径向范围。扫描半圆（`start_angle=180`、`end_angle=0`）是构建仪表盘（Gauge Chart）的常见方式。

```python demo graphing
data_radial_bar = [
    {"name": "18-24", "uv": 31.47, "pv": 2400, "fill": "#8884d8"},
    {"name": "25-29", "uv": 26.69, "pv": 4567, "fill": "#83a6ed"},
    {"name": "30-34", "uv": -15.69, "pv": 1398, "fill": "#8dd1e1"},
    {"name": "35-39", "uv": 8.22, "pv": 9800, "fill": "#82ca9d"},
    {"name": "40-49", "uv": -8.63, "pv": 3908, "fill": "#a4de6c"},
    {"name": "50+", "uv": -2.63, "pv": 4800, "fill": "#d0ed57"},
    {"name": "unknown", "uv": 6.67, "pv": 4800, "fill": "#ffc658"},
]


def radial_bar_advanced():
    return rx.recharts.radial_bar_chart(
        rx.recharts.radial_bar(
            data_key="uv",
            min_angle=90,
            background=True,
            label={"fill": "#666", "position": "insideStart"},
        ),
        data=data_radial_bar,
        inner_radius="10%",
        outer_radius="80%",
        start_angle=180,
        end_angle=0,
        width="100%",
        height=300,
    )
```

## 何时使用径向柱状图

径向柱状图是标准柱状图的紧凑且醒目的替代方案，最适合比较少量类别或展示目标完成进度。由于条形围绕圆形排列，它非常适合空间有限的仪表板。塑造图表的关键属性包括 `inner_radius` 和 `outer_radius`（条形距中心的距离）、`start_angle` 和 `end_angle`（条形跨越的圆弧——使用 `180` 到 `0` 可创建半圆仪表盘）、`min_angle`（最小条形长度）以及 `background`（绘制在每个条形后面的轨道）。对于大量类别或精确的数值比较，标准[柱状图](/docs/library/graphing/charts/barchart)通常更容易阅读。

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [雷达图](/docs/library/graphing/charts/radarchart)
- [饼图](/docs/library/graphing/charts/piechart)
- [柱状图](/docs/library/graphing/charts/barchart)
