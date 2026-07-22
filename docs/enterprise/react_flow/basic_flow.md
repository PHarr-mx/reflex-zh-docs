# 基本流程图示例

此示例演示了一个包含三个节点和两条边的简单流程图，展示了如何连接节点以及如何为边添加动画效果。

### 节点

- 输入节点（Input Node）——起始点
- 默认节点（Default Node）——标准内容节点
- 输出节点（Output Node）——流程的终点

### 边

- 输入 → 默认（带动画）
- 默认 → 输出

### 交互性

- 节点可以移动
- 边会动态更新
- 用户可以从连接点拖动以创建新边
- 提供缩放、平移和小地图控件

### 视觉布局

- 流程图自动适配视口
- 背景网格用于定位参考
- 支持浅色和深色模式

### 示例流程

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
from reflex_enterprise.components.flow.types import Node, Edge

# 所有节点的通用样式
node_style = {
    "backgroundColor": "#ffcc00",
    "color": "#000000",
    "padding": "10px",
    "borderRadius": "5px",
}


class FlowState(rx.State):
    nodes: list[Node] = [
        {
            "id": "1",
            "type": "input",
            "position": {"x": 100, "y": 100},
            "data": {"label": "Input Node"},
            "style": node_style,
        },
        {
            "id": "2",
            "type": "default",
            "position": {"x": 300, "y": 200},
            "data": {"label": "Default Node"},
            "style": node_style,
        },
        {
            "id": "3",
            "type": "output",
            "position": {"x": 500, "y": 100},
            "data": {"label": "Output Node"},
            "style": node_style,
        },
    ]

    edges: list[Edge] = [
        {"id": "e1-2", "source": "1", "target": "2", "animated": True},
        {"id": "e2-3", "source": "2", "target": "3"},
    ]


def flow_example():
    return rx.box(
        rxe.flow(
            # 核心流程组件
            rxe.flow.controls(),
            rxe.flow.background(),
            rxe.flow.mini_map(),
            # 流程配置
            default_nodes=FlowState.nodes,
            default_edges=FlowState.edges,
            nodes=FlowState.nodes,
            edges=FlowState.edges,
            # 视觉设置
            fit_view=True,
            attribution_position="bottom-right",
        ),
        height="100vh",
        width="100vw",
    )
```
