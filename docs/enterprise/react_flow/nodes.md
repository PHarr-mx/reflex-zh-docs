# 节点（Nodes）

节点（Nodes）是流程图的基本构建块。本页介绍如何在 Reflex Flow 中定义和自定义节点。

## 节点类型（Node Type）

节点以 Python 字典表示，包含以下字段：

- `id`（`str`）—— 节点的唯一标识符。
- `position`（`dict`）—— 节点的位置，包含 `x` 和 `y` 坐标。
- `data`（`dict`）—— 传递给节点组件的任意数据。
- `type`（`str`）—— 在 `node_types` 中定义的节点类型。
- `sourcePosition`（`str`）—— 控制源连接点的位置（"top"、"right"、"bottom"、"left"）。
- `targetPosition`（`str`）—— 控制目标连接点的位置（"top"、"right"、"bottom"、"left"）。
- `hidden`（`bool`）—— 节点是否在画布上可见。
- `selected`（`bool`）—— 节点当前是否被选中。
- `draggable`（`bool`）—— 节点是否可被拖动。
- `selectable`（`bool`）—— 节点是否可被选中。
- `connectable`（`bool`）—— 节点是否可与其他节点连接。
- `deletable`（`bool`）—— 节点是否可被删除。
- `width`（`float`）—— 节点的宽度。
- `height`（`float`）—— 节点的高度。
- `parentId`（`str`）—— 用于创建子流程图的父节点 ID。
- `style`（`dict`）—— 节点的自定义样式。
- `className`（`str`）—— 节点的 CSS 类名。

## 内置节点类型

Reflex Flow 包含几种内置节点类型：

```python
nodes: list[Node] = [
    {
        "id": "1",
        "type": "input",
        "position": {"x": 100, "y": 100},
        "data": {"label": "Start"},
    },
    {
        "id": "2",
        "type": "default",
        "position": {"x": 300, "y": 100},
        "data": {"label": "Process"},
    },
    {
        "id": "3",
        "type": "output",
        "position": {"x": 500, "y": 100},
        "data": {"label": "End"},
    },
]
```

- **input** —— 仅具有源连接点的入口点
- **default** —— 同时具有源连接点和目标连接点的标准节点
- **output** —— 仅具有目标连接点的出口点

## 基本节点配置

### 节点定位

```python
node = {
    "id": "positioned-node",
    "type": "default",
    "position": {"x": 250, "y": 150},
    "data": {"label": "Positioned Node"},
}
```

### 节点样式

```python
styled_node = {
    "id": "styled-node",
    "type": "default",
    "position": {"x": 100, "y": 200},
    "data": {"label": "Custom Style"},
    "style": {
        "background": "#ff6b6b",
        "color": "white",
        "border": "2px solid #ff5252",
        "borderRadius": "8px",
        "padding": "10px",
    },
}
```

### 连接点定位

```python
node_with_handles = {
    "id": "handle-node",
    "type": "default",
    "position": {"x": 300, "y": 300},
    "data": {"label": "Custom Handles"},
    "sourcePosition": "right",
    "targetPosition": "left",
}
```

## 自定义节点

创建自定义节点就像构建一个普通的 React 组件并将其传递给 `node_types` 一样简单。由于它们是标准的 React 组件，你可以显示任何内容并实现所需的任何功能。此外，你还可以访问一系列属性，从而扩展和自定义默认的节点行为。

下面是一个使用 `color picker` 组件的自定义节点示例。

```python demo exec
from typing import Any

import reflex as rx

import reflex_enterprise as rxe
from reflex_enterprise.components.flow.types import Connection, Edge, Node


class CustomNodeState(rx.State):
    bg_color: rx.Field[str] = rx.field(default="#c9f1dd")
    nodes: rx.Field[list[Node]] = rx.field(
        default_factory=lambda: [
            {
                "id": "1",
                "type": "input",
                "data": {"label": "An input node"},
                "position": {"x": 0, "y": 50},
                "sourcePosition": "right",
                "style": {
                    "color": "#000000",
                },
            },
            {
                "id": "2",
                "type": "selectorNode",
                "data": {
                    "color": "#c9f1dd",
                },
                "position": {"x": 300, "y": 50},
            },
            {
                "id": "3",
                "type": "output",
                "data": {"label": "Output A"},
                "position": {"x": 650, "y": 25},
                "targetPosition": "left",
                "style": {
                    "color": "#000000",
                },
            },
            {
                "id": "4",
                "type": "output",
                "data": {"label": "Output B"},
                "position": {"x": 650, "y": 100},
                "targetPosition": "left",
                "style": {
                    "color": "#000000",
                },
            },
        ]
    )
    edges: rx.Field[list[Edge]] = rx.field(
        default_factory=lambda: [
            {
                "id": "e1-2",
                "source": "1",
                "target": "2",
                "animated": True,
            },
            {
                "id": "e2a-3",
                "source": "2",
                "target": "3",
                "animated": True,
            },
            {
                "id": "e2b-4",
                "source": "2",
                "target": "4",
                "animated": True,
            },
        ]
    )

    @rx.event
    def on_change_color(self, color: str):
        self.nodes = [
            node
            if node["id"] != "2" or "data" not in node
            else {**node, "data": {**node["data"], "color": color}}
            for node in self.nodes
        ]
        self.bg_color = color

    @rx.event
    def set_nodes(self, nodes: list[Node]):
        self.nodes = nodes

    @rx.event
    def set_edges(self, edges: list[Edge]):
        self.edges = edges


@rx.memo
def color_selector_node(
    data: rx.Var[dict], isConnectable: rx.Var[bool]
) -> rx.Component:
    data = data.to(dict)
    return rx.el.div(
        rxe.flow.handle(
            type="target",
            position="left",
            is_connectable=isConnectable,
        ),
        rx.el.div(
            "Custom Color Picker Node: ",
            rx.el.strong(data["color"]),
        ),
        rx.el.input(
            class_name="nodrag",
            type="color",
            on_change=CustomNodeState.on_change_color,
            default_value=data["color"],
        ),
        rxe.flow.handle(
            type="source",
            position="right",
            is_connectable=isConnectable,
        ),
        class_name="border border-1 p-2 rounded-sm",
        border_color=rx.color_mode_cond("black", ""),
        color="black",
        bg="white",
    )


def node_stroke_color(node: rx.vars.ObjectVar[Node]):
    return rx.match(
        node["type"],
        ("input", "#0041d0"),
        (
            "selectorNode",
            CustomNodeState.bg_color,
        ),
        ("output", "#ff0072"),
        None,
    )


def node_color(node: rx.vars.ObjectVar[Node]):
    return rx.match(
        node["type"],
        (
            "selectorNode",
            CustomNodeState.bg_color,
        ),
        "#fff",
    )


def custom_node():
    return rx.box(
        rxe.flow(
            rxe.flow.background(bg_color=CustomNodeState.bg_color),
            rxe.flow.mini_map(
                node_stroke_color=rx.vars.function.ArgsFunctionOperation.create(
                    ("node",), node_stroke_color(rx.Var("node").to(Node))
                ),
                node_color=rx.vars.function.ArgsFunctionOperation.create(
                    ("node",), node_color(rx.Var("node").to(Node))
                ),
            ),
            rxe.flow.controls(),
            default_nodes=CustomNodeState.nodes,
            default_edges=CustomNodeState.edges,
            nodes=CustomNodeState.nodes,
            edges=CustomNodeState.edges,
            on_nodes_change=lambda changes: CustomNodeState.set_nodes(
                rxe.flow.util.apply_node_changes(CustomNodeState.nodes, changes)
            ),
            on_edges_change=lambda changes: CustomNodeState.set_edges(
                rxe.flow.util.apply_edge_changes(CustomNodeState.edges, changes)
            ),
            on_connect=lambda connection: CustomNodeState.set_edges(
                rxe.flow.util.add_edge(
                    connection.to(dict).merge({"animated": True}).to(Connection),
                    CustomNodeState.edges,
                )
            ),
            node_types={"selectorNode": color_selector_node},
            color_mode="light",
            snap_grid=(20, 20),
            default_viewport={"x": 0, "y": 0, "zoom": 1.5},
            snap_to_grid=True,
            attribution_position="bottom-left",
            fit_view=True,
        ),
        height="100vh",
        width="100vw",
    )
```
