---
components:
  - rx.recharts.Treemap
title: Treemap
meta_description: "Create treemap charts in Python with Reflex. Build interactive Recharts treemaps to visualize hierarchical, part-to-whole data as nested rectangles — all in pure Python, no JavaScript."
---

# 矩形树图（Treemap）

```python exec
import reflex as rx
```

Reflex 中的矩形树图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。矩形树图将层次化的部分与整体数据以一组嵌套矩形的方式展示，每个矩形的面积与其值成正比。矩形树图是一种空间高效的方式，可以同时比较多个类别。

## 简单示例

`rx.recharts.treemap()` 接收一个字典列表作为其 `data`，以及一个 `data_key` 来命名决定每个矩形大小的字段。使用 `name_key` 设置显示在每个图块上的标签。

```python demo graphing
data = [
    {"name": "Category A", "size": 2400, "fill": rx.color("accent", 8)},
    {"name": "Category B", "size": 4567, "fill": rx.color("blue", 8)},
    {"name": "Category C", "size": 1398, "fill": rx.color("green", 8)},
    {"name": "Category D", "size": 9800, "fill": rx.color("orange", 8)},
    {"name": "Category E", "size": 3908, "fill": rx.color("red", 8)},
    {"name": "Category F", "size": 4800, "fill": rx.color("purple", 8)},
]


def treemap_simple():
    return rx.recharts.treemap(
        data=data,
        data_key="size",
        name_key="name",
        width="100%",
        height=300,
    )
```

## 宽高比

`aspect_ratio` 属性控制矩形树图在布局每个矩形时使用的目标宽高比。较低的值产生较高的图块；较高的值产生较宽的图块。

```python demo graphing
data = [
    {"name": "Group A", "size": 2400, "fill": rx.color("accent", 8)},
    {"name": "Group B", "size": 4567, "fill": rx.color("cyan", 8)},
    {"name": "Group C", "size": 1398, "fill": rx.color("grass", 8)},
    {"name": "Group D", "size": 6800, "fill": rx.color("amber", 8)},
    {"name": "Group E", "size": 3908, "fill": rx.color("plum", 8)},
]


def treemap_aspect():
    return rx.recharts.treemap(
        data=data,
        data_key="size",
        name_key="name",
        aspect_ratio=1,
        width="100%",
        height=300,
    )
```

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [饼图](/docs/library/graphing/charts/piechart)
- [柱状图](/docs/library/graphing/charts/barchart)
- [漏斗图](/docs/library/graphing/charts/funnelchart)
