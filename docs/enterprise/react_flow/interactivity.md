# 为流程图添加交互性

本指南介绍如何在 Reflex 中创建交互式流程图，使你能够选择、拖动和连接节点与边。

## 定义状态

我们首先定义流程图的节点和边。`FlowState` 类将节点和边作为状态变量保存，并包含用于响应变化的事件处理程序。

```python
import reflex as rx
import reflex_enterprise as rxe
from reflex_enterprise.components.flow.types import Node, Edge


class FlowState(rx.State):
    nodes: list[Node] = [
        {
            "id": "1",
            "type": "input",
            "position": {"x": 100, "y": 100},
            "data": {"label": "Node 1"},
        },
        {
            "id": "2",
            "type": "default",
            "position": {"x": 300, "y": 200},
            "data": {"label": "Node 2"},
        },
    ]

    edges: list[Edge] = [
        {
            "id": "e1-2",
            "source": "1",
            "target": "2",
            "label": "Connection",
            "type": "step",
        }
    ]
```

## 添加事件处理程序

事件处理程序使流程图能够响应用户交互，例如拖动节点、更新边或创建新连接。

```python
@rx.event
def set_nodes(self, nodes: list[Node]):
    self.nodes = nodes


@rx.event
def set_edges(self, edges: list[Edge]):
    self.edges = edges
```

- set_nodes 在节点被移动或编辑时更新节点。

- set_edges 在边被修改或删除时更新边。


## 渲染交互式流程图

最后，我们使用 **rxe.flow** 渲染流程图，并传入状态和事件处理程序。额外的 UI 功能包括缩放/平移控件、背景网格以及用于导航的小地图。

```python
def interactive_flow():
    return rx.box(
        rxe.flow(
            rxe.flow.controls(),
            rxe.flow.background(),
            rxe.flow.mini_map(),
            nodes=FlowState.nodes,
            edges=FlowState.edges,
            on_nodes_change=lambda node_changes: FlowState.set_nodes(
                rxe.flow.util.apply_node_changes(FlowState.nodes, node_changes)
            ),
            on_edges_change=lambda edge_changes: FlowState.set_edges(
                rxe.flow.util.apply_edge_changes(FlowState.edges, edge_changes)
            ),
            fit_view=True,
            attribution_position="bottom-right",
        ),
        height="100vh",
        width="100vw",
    )
```
