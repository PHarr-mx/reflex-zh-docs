# 边（Edges）

边（Edges）将流程图中的节点连接在一起。本页介绍如何在 Reflex Flow 中定义、自定义边并与之交互。

## 边类型（Edge Type）

边以 Python 字典表示，包含以下字段：

- `id`（`str`）—— 边的唯一标识符。
- `source`（`str`）—— 源节点的 ID。
- `target`（`str`）—— 目标节点的 ID。
- `type`（`str`）—— 在 `edge_types` 中定义的边类型。
- `sourceHandle`（`str | None`）—— 可选的源连接点 ID。
- `targetHandle`（`str | None`）—— 可选的目标连接点 ID。
- `animated`（`bool`）—— 边是否应有动画效果。
- `hidden`（`bool`）—— 边是否隐藏。
- `deletable`（`bool`）—— 边是否可被移除。
- `selectable`（`bool`）—— 边是否可被选中。
- `data`（`dict`）—— 任意元数据。
- `label`（`Any`）—— 沿边渲染的标签。
- `style`（`dict`）—— 自定义样式。
- `className`（`str`）—— 边的 CSS 类。

## 基本边类型

Reflex Flow 自带几种内置边类型：

### 默认边类型

```python
edges: list[Edge] = [
    {"id": "e1", "source": "1", "target": "2", "type": "default"},
    {"id": "e2", "source": "2", "target": "3", "type": "straight"},
    {"id": "e3", "source": "3", "target": "4", "type": "step"},
    {"id": "e4", "source": "4", "target": "5", "type": "smoothstep"},
    {"id": "e5", "source": "5", "target": "6", "type": "bezier"},
]
```

- **default** —— 标准曲线边
- **straight** —— 节点之间的直线
- **step** —— 带阶梯的直角路径
- **smoothstep** —— 平滑的直角路径
- **bezier** —— 弯曲的贝塞尔路径

## 边的样式

### 基本样式

使用 `style` 属性为边添加视觉样式：

```python
edges: list[Edge] = [
    {
        "id": "styled-edge",
        "source": "1",
        "target": "2",
        "style": {
            "stroke": "#ff6b6b",
            "strokeWidth": 3,
        },
    }
]
```

### 动画边

让边以流动的点呈现动画效果：

```python
edges: list[Edge] = [
    {
        "id": "animated-edge",
        "source": "1",
        "target": "2",
        "animated": True,
        "style": {"stroke": "#4dabf7"},
    }
]
```

### 边标签

为边添加文本标签：

```python
edges: list[Edge] = [
    {
        "id": "labeled-edge",
        "source": "1",
        "target": "2",
        "label": "Connection",
        "style": {"stroke": "#51cf66"},
    }
]
```

## 自定义边

Reflex 中的 React Flow 还允许你定义自定义边类型。当你希望边承载超出默认直线或贝塞尔连接器的额外功能（例如按钮、标签或动态样式）时，这非常有用。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
from reflex_enterprise.components.flow.types import (
    ConnectionInProgress,
    Edge,
    NoConnection,
    Node,
    Position,
)


class SimpleEdgeDemoState(rx.State):
    nodes: list[Node] = [
        {
            "id": "1",
            "position": {"x": 0, "y": 0},
            "data": {"label": "Node A"},
            "style": {
                "color": "#000000",
            },
        },
        {
            "id": "2",
            "position": {"x": 250, "y": 150},
            "data": {"label": "Node B"},
            "style": {
                "color": "#000000",
            },
        },
    ]
    edges: list[Edge] = [{"id": "e1-2", "source": "1", "target": "2", "type": "button"}]

    @rx.event
    def set_nodes(self, nodes: list[Node]):
        self.nodes = nodes

    @rx.event
    def set_edges(self, edges: list[Edge]):
        self.edges = edges

    @rx.event
    def handle_connect_end(
        self,
        connection_status: NoConnection | ConnectionInProgress,
    ):
        if not connection_status["isValid"]:
            new_edge = {
                "id": f"{connection_status['fromNode']['id']}-{connection_status['toNode']['id']}",
                "source": connection_status["fromNode"]["id"],
                "target": connection_status["toNode"]["id"],
                "type": "button",
            }
            self.edges.append(new_edge)


@rx.memo
def button_edge(
    id: rx.Var[str],
    sourceX: rx.Var[float],
    sourceY: rx.Var[float],
    targetX: rx.Var[float],
    targetY: rx.Var[float],
    sourcePosition: rx.Var[Position],
    targetPosition: rx.Var[Position],
    markerEnd: rx.Var[str],
) -> rx.Fragment:
    bezier_path = rxe.components.flow.util.get_bezier_path(
        source_x=sourceX,
        source_y=sourceY,
        target_x=targetX,
        target_y=targetY,
        source_position=sourcePosition,
        target_position=targetPosition,
    )

    mid_x = bezier_path.label_x
    mid_y = bezier_path.label_y

    return rx.fragment(
        rxe.flow.base_edge(path=bezier_path.path, markerEnd=markerEnd),
        rxe.flow.edge_label_renderer(
            rx.el.div(
                rx.el.button(
                    "×",
                    class_name=(
                        "w-[30px] h-[30px] border-2 border-gray-200 bg-gray-200 text-black rounded-full text-[12px] pt-0 cursor-pointer hover:bg-gray-400 hover:text-white"
                    ),
                    on_click=rx.run_script(
                        rxe.flow.api.set_edges(
                            rx.vars.FunctionStringVar.create(
                                "Array.prototype.filter.call"
                            ).call(
                                rxe.flow.api.get_edges(),
                                rx.Var(f"((edge) => edge.id !== {id})"),
                            ),
                        )
                    ),
                    style={
                        "position": "absolute",
                        "left": f"{mid_x}px",
                        "top": f"{mid_y}px",
                        "transform": "translate(-50%, -50%)",
                        "pointerEvents": "all",
                    },
                ),
            )
        ),
    )


def very_simple_custom_edge_example():
    return rx.box(
        rxe.flow(
            rxe.flow.background(),
            default_nodes=SimpleEdgeDemoState.nodes,
            default_edges=SimpleEdgeDemoState.edges,
            nodes=SimpleEdgeDemoState.nodes,
            edges=SimpleEdgeDemoState.edges,
            on_connect=lambda connection: SimpleEdgeDemoState.set_edges(
                rxe.flow.util.add_edge(connection, SimpleEdgeDemoState.edges)
            ),
            on_connect_end=lambda status, event: SimpleEdgeDemoState.handle_connect_end(
                status
            ),
            edge_types={"button": button_edge},
            fit_view=True,
        ),
        height="100vh",
        width="100%",
    )
```
