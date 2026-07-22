---
components:
  - rx.recharts.FunnelChart
  - rx.recharts.Funnel
title: Funnel Chart
meta_description: "Create funnel charts in Python with Reflex. Build interactive Recharts funnel charts to visualize conversion stages and user flow with custom colors, labels, and tooltips."
---

```python exec
import reflex as rx
import random

rx.toast.provider()
```

# 漏斗图（Funnel Chart）

Reflex 中的漏斗图基于 [Recharts](https://recharts.org/)（一个 React 图表库）构建，使用纯 Python 创建。漏斗图是一种用于可视化数据在流程中如何移动的图形表示。在漏斗图中，因变量的值在后续阶段逐渐减小。它可以用来展示用户在业务或销售流程中的流转情况。

## 简单示例

```python demo graphing
data = [
    {"value": 100, "name": "Sent", "fill": "#8884d8"},
    {"value": 80, "name": "Viewed", "fill": "#83a6ed"},
    {"value": 50, "name": "Clicked", "fill": "#8dd1e1"},
    {"value": 40, "name": "Add to Cart", "fill": "#82ca9d"},
    {"value": 26, "name": "Purchased", "fill": "#a4de6c"},
]


def funnel_simple():
    return rx.recharts.funnel_chart(
        rx.recharts.funnel(
            rx.recharts.label_list(
                position="right",
                data_key="name",
                fill="#000",
                stroke="none",
            ),
            data_key="value",
            data=data,
        ),
        width="100%",
        height=250,
    )
```

## 事件触发器

漏斗图支持 `on_click`、`on_mouse_enter`、`on_mouse_leave` 和 `on_mouse_move` 事件触发器，允许你与漏斗图交互并根据用户操作执行特定动作。

```python demo graphing
data = [
    {"value": 100, "name": "Sent", "fill": "#8884d8"},
    {"value": 80, "name": "Viewed", "fill": "#83a6ed"},
    {"value": 50, "name": "Clicked", "fill": "#8dd1e1"},
    {"value": 40, "name": "Add to Cart", "fill": "#82ca9d"},
    {"value": 26, "name": "Purchased", "fill": "#a4de6c"},
]


def funnel_events():
    return rx.recharts.funnel_chart(
        rx.recharts.funnel(
            rx.recharts.label_list(
                position="right",
                data_key="name",
                fill="#000",
                stroke="none",
            ),
            data_key="value",
            data=data,
        ),
        on_click=rx.toast("Clicked on funnel chart"),
        on_mouse_enter=rx.toast("Mouse entered"),
        on_mouse_leave=rx.toast("Mouse left"),
        width="100%",
        height=250,
    )
```

## 动态数据

下面是一个使用 `State` 的漏斗图示例。我们定义了一个 `randomize_data` 函数，当使用 `on_click=FunnelState.randomize_data` 点击图表时，该函数会随机更改数据。

```python exec
data = [
    {"value": 100, "name": "Sent", "fill": "#8884d8"},
    {"value": 80, "name": "Viewed", "fill": "#83a6ed"},
    {"value": 50, "name": "Clicked", "fill": "#8dd1e1"},
    {"value": 40, "name": "Add to Cart", "fill": "#82ca9d"},
    {"value": 26, "name": "Purchased", "fill": "#a4de6c"},
]
```

```python demo exec
class FunnelState(rx.State):
    data = data

    @rx.event
    def randomize_data(self):
        self.data[0]["value"] = 100
        for i in range(len(self.data) - 1):
            self.data[i + 1]["value"] = self.data[i]["value"] - random.randint(0, 20)


def funnel_state():
    return rx.recharts.funnel_chart(
        rx.recharts.funnel(
            rx.recharts.label_list(
                position="right",
                data_key="name",
                fill="#000",
                stroke="none",
            ),
            data_key="value",
            data=FunnelState.data,
            on_click=FunnelState.randomize_data,
        ),
        rx.recharts.graphing_tooltip(),
        width="100%",
        height=250,
    )
```

## 更改图表动画

`is_animation_active` 属性可用于关闭动画，但默认为 `True`。`animation_begin` 设置动画开始前的延迟，`animation_duration` 决定动画持续时间，`animation_easing` 定义动画的速度曲线以实现平滑过渡。

```python demo graphing
data = [
    {"name": "Visits", "value": 5000, "fill": "#8884d8"},
    {"name": "Cart", "value": 3000, "fill": "#83a6ed"},
    {"name": "Checkout", "value": 2500, "fill": "#8dd1e1"},
    {"name": "Purchase", "value": 1000, "fill": "#82ca9d"},
]


def funnel_animation():
    return rx.recharts.funnel_chart(
        rx.recharts.funnel(
            data_key="value",
            data=data,
            animation_begin=300,
            animation_duration=9000,
            animation_easing="ease-in-out",
        ),
        rx.recharts.graphing_tooltip(),
        rx.recharts.legend(),
        width="100%",
        height=300,
    )
```

## 相关图表

探索更多可以使用 Reflex 和 Recharts 以纯 Python 构建的图表类型：

- [饼图](/docs/library/graphing/charts/piechart)
- [柱状图](/docs/library/graphing/charts/barchart)
- [组合图](/docs/library/graphing/charts/composedchart)
