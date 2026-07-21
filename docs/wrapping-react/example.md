```python exec
import reflex as rx
from typing import Any
```

# 复杂示例

在这个更复杂的示例中，我们将封装 `reactflow`——一个用于构建基于节点的应用（如流程图、图表、图形等）的库。

## 导入

首先从导入库 [reactflow](https://www.npmjs.com/package/reactflow) 开始。让我们创建一个名为 `reactflow.py` 的单独文件，并添加以下代码：

```python
import reflex as rx
from typing import Any, Dict, List, Union


class ReactFlowLib(rx.Component):
    """A component that wraps a react flow lib."""

    library = "reactflow"

    def _get_custom_code(self) -> str:
        return """import 'reactflow/dist/style.css';
        """
```

注意我们还使用了 `_get_custom_code` 方法来导入库样式所需的 CSS 文件。

## 组件

在本教程中，我们将封装 Reactflow 中的三个组件：`ReactFlow`、`Background` 和 `Controls`。让我们从 `ReactFlow` 组件开始。

在这里，我们将定义 `tag` 以及使用该组件所需的 `vars`。

在本教程中，我们将定义 `EventHandler` 类型的 props `on_nodes_change` 和 `on_connect`，但你可以在 [reactflow 文档](https://reactflow.dev/docs/api/react-flow-props/#onnodeschange) 中找到该组件触发的所有事件。

```python
import reflex as rx
from typing import Any, Dict, List, Union


class ReactFlowLib(rx.Component): ...


class ReactFlow(ReactFlowLib):
    tag = "ReactFlow"

    nodes: rx.Var[List[Dict[str, Any]]]

    edges: rx.Var[List[Dict[str, Any]]]

    fit_view: rx.Var[bool]

    nodes_draggable: rx.Var[bool]

    nodes_connectable: rx.Var[bool]

    nodes_focusable: rx.Var[bool]

    on_nodes_change: rx.EventHandler[lambda e0: [e0]]

    on_connect: rx.EventHandler[lambda e0: [e0]]
```

现在让我们添加 `Background` 和 `Controls` 组件。我们还将通过 `create` 方法创建这些组件，以便在应用中使用它们。

```python
import reflex as rx
from typing import Any, Dict, List, Union


class ReactFlowLib(rx.Component): ...


class ReactFlow(ReactFlowLib): ...


class Background(ReactFlowLib):
    tag = "Background"

    color: rx.Var[str]

    gap: rx.Var[int]

    size: rx.Var[int]

    variant: rx.Var[str]


class Controls(ReactFlowLib):
    tag = "Controls"


react_flow = ReactFlow.create
background = Background.create
controls = Controls.create
```

## 构建应用

现在我们已经有了组件，让我们来构建应用。

首先定义将在应用中使用的初始节点和边。

```python
import reflex as rx
from .react_flow import react_flow, background, controls
import random
from collections import defaultdict
from typing import Any, Dict, List


initial_nodes = [
    {
        "id": "1",
        "type": "input",
        "data": {"label": "150"},
        "position": {"x": 250, "y": 25},
    },
    {
        "id": "2",
        "data": {"label": "25"},
        "position": {"x": 100, "y": 125},
    },
    {
        "id": "3",
        "type": "output",
        "data": {"label": "5"},
        "position": {"x": 250, "y": 250},
    },
]

initial_edges = [
    {"id": "e1-2", "source": "1", "target": "2", "label": "*", "animated": True},
    {"id": "e2-3", "source": "2", "target": "3", "label": "+", "animated": True},
]
```

接下来，我们将定义应用的状态。我们有四个事件处理器：`add_random_node`、`clear_graph`、`on_connect` 和 `on_nodes_change`。

`on_nodes_change` 事件处理器在节点被选中并拖拽时触发。该函数用于在拖拽过程中更新节点的位置。它接受一个参数 `node_changes`，这是一个包含各种元数据类型的字典列表。对于更新位置，该函数专门处理类型为 `position` 的变更。

```python
class State(rx.State):
    """The app state."""

    nodes: List[Dict[str, Any]] = initial_nodes
    edges: List[Dict[str, Any]] = initial_edges

    @rx.event
    def add_random_node(self):
        new_node_id = f"{len(self.nodes) + 1}"
        node_type = random.choice(["default"])
        # Label is random number
        label = new_node_id
        x = random.randint(0, 500)
        y = random.randint(0, 500)

        new_node = {
            "id": new_node_id,
            "type": node_type,
            "data": {"label": label},
            "position": {"x": x, "y": y},
            "draggable": True,
        }
        self.nodes.append(new_node)

    @rx.event
    def clear_graph(self):
        self.nodes = []  # Clear the nodes list
        self.edges = []  # Clear the edges list

    @rx.event
    def on_connect(self, new_edge):
        # Iterate over the existing edges
        for i, edge in enumerate(self.edges):
            # If we find an edge with the same ID as the new edge
            if edge["id"] == f"e{new_edge['source']}-{new_edge['target']}":
                # Delete the existing edge
                del self.edges[i]
                break

        # Add the new edge
        self.edges.append({
            "id": f"e{new_edge['source']}-{new_edge['target']}",
            "source": new_edge["source"],
            "target": new_edge["target"],
            "label": random.choice(["+", "-", "*", "/"]),
            "animated": True,
        })

    @rx.event
    def on_nodes_change(self, node_changes: List[Dict[str, Any]]):
        # Receives a list of Nodes in case of events like dragging
        map_id_to_new_position = defaultdict(dict)

        # Loop over the changes and store the new position
        for change in node_changes:
            if change["type"] == "position" and change.get("dragging") == True:
                map_id_to_new_position[change["id"]] = change["position"]

        # Loop over the nodes and update the position
        for i, node in enumerate(self.nodes):
            if node["id"] in map_id_to_new_position:
                new_position = map_id_to_new_position[node["id"]]
                self.nodes[i]["position"] = new_position
```

现在让我们定义应用的 UI。我们将使用 `react_flow` 组件，并从状态传入 `nodes` 和 `edges`。我们还将向 `react_flow` 组件添加 `on_connect` 事件处理器，以处理边连接时的逻辑。

```python
def index() -> rx.Component:
    return rx.vstack(
        react_flow(
            background(),
            controls(),
            nodes_draggable=True,
            nodes_connectable=True,
            on_connect=lambda e0: State.on_connect(e0),
            on_nodes_change=lambda e0: State.on_nodes_change(e0),
            nodes=State.nodes,
            edges=State.edges,
            fit_view=True,
        ),
        rx.hstack(
            rx.button("Clear graph", on_click=State.clear_graph, width="100%"),
            rx.button("Add node", on_click=State.add_random_node, width="100%"),
            width="100%",
        ),
        height="30em",
        width="100%",
    )


# Add state and page to the app.
app = rx.App()
app.add_page(index)
```

```python exec
import reflex as rx
from typing import Any, Dict, List, Union
from collections import defaultdict
import random


class ReactFlowLib(rx.Component):
    """A component that wraps a react flow lib."""

    library = "reactflow"

    def _get_custom_code(self) -> str:
        return """import 'reactflow/dist/style.css';
        """


class ReactFlow(ReactFlowLib):
    tag = "ReactFlow"

    nodes: rx.Var[List[Dict[str, Any]]]

    edges: rx.Var[List[Dict[str, Any]]]

    fit_view: rx.Var[bool]

    nodes_draggable: rx.Var[bool]

    nodes_connectable: rx.Var[bool]

    nodes_focusable: rx.Var[bool]

    on_nodes_change: rx.EventHandler[lambda e0: [e0]]

    on_connect: rx.EventHandler[lambda e0: [e0]]


class Background(ReactFlowLib):
    tag = "Background"

    color: rx.Var[str]

    gap: rx.Var[int]

    size: rx.Var[int]

    variant: rx.Var[str]


class Controls(ReactFlowLib):
    tag = "Controls"


react_flow = ReactFlow.create
background = Background.create
controls = Controls.create

initial_nodes = [
    {
        "id": "1",
        "type": "input",
        "data": {"label": "150"},
        "position": {"x": 250, "y": 25},
    },
    {
        "id": "2",
        "data": {"label": "25"},
        "position": {"x": 100, "y": 125},
    },
    {
        "id": "3",
        "type": "output",
        "data": {"label": "5"},
        "position": {"x": 250, "y": 250},
    },
]

initial_edges = [
    {"id": "e1-2", "source": "1", "target": "2", "label": "*", "animated": True},
    {"id": "e2-3", "source": "2", "target": "3", "label": "+", "animated": True},
]


class ReactFlowState(rx.State):
    """The app state."""

    nodes: List[Dict[str, Any]] = initial_nodes
    edges: List[Dict[str, Any]] = initial_edges

    @rx.event
    def add_random_node(self):
        new_node_id = f"{len(self.nodes) + 1}"
        node_type = random.choice(["default"])
        # Label is random number
        label = new_node_id
        x = random.randint(0, 250)
        y = random.randint(0, 250)

        new_node = {
            "id": new_node_id,
            "type": node_type,
            "data": {"label": label},
            "position": {"x": x, "y": y},
            "draggable": True,
        }
        self.nodes.append(new_node)

    @rx.event
    def clear_graph(self):
        self.nodes = []  # Clear the nodes list
        self.edges = []  # Clear the edges list

    @rx.event
    def on_connect(self, new_edge):
        # Iterate over the existing edges
        for i, edge in enumerate(self.edges):
            # If we find an edge with the same ID as the new edge
            if edge["id"] == f"e{new_edge['source']}-{new_edge['target']}":
                # Delete the existing edge
                del self.edges[i]
                break

        # Add the new edge
        self.edges.append({
            "id": f"e{new_edge['source']}-{new_edge['target']}",
            "source": new_edge["source"],
            "target": new_edge["target"],
            "label": random.choice(["+", "-", "*", "/"]),
            "animated": True,
        })

    @rx.event
    def on_nodes_change(self, node_changes: List[Dict[str, Any]]):
        # Receives a list of Nodes in case of events like dragging
        map_id_to_new_position = defaultdict(dict)

        # Loop over the changes and store the new position
        for change in node_changes:
            if change["type"] == "position" and change.get("dragging") == True:
                map_id_to_new_position[change["id"]] = change["position"]

        # Loop over the nodes and update the position
        for i, node in enumerate(self.nodes):
            if node["id"] in map_id_to_new_position:
                new_position = map_id_to_new_position[node["id"]]
                self.nodes[i]["position"] = new_position
```

以下是应用运行的示例：

```python eval
rx.vstack(
    react_flow(
        background(),
        controls(),
        nodes_draggable=True,
        nodes_connectable=True,
        on_connect=lambda e0: ReactFlowState.on_connect(e0),
        on_nodes_change=lambda e0: ReactFlowState.on_nodes_change(e0),
        nodes=ReactFlowState.nodes,
        edges=ReactFlowState.edges,
        fit_view=True,
    ),
    rx.hstack(
        rx.button("Clear graph", on_click=ReactFlowState.clear_graph, width="50%"),
        rx.button("Add node", on_click=ReactFlowState.add_random_node, width="50%"),
        width="100%",
    ),
    height="30em",
    width="100%",
)
```
