# 钩子（Hooks）（API）

`rxe.flow.api` 模块提供了与流程图（Flow）实例进行交互的钩子。这些钩子是 React Flow 中 `useReactFlow` 钩子的封装。

## 节点钩子（Node Hooks）

- `get_nodes()`：返回流程图中所有节点的数组。
- `set_nodes(nodes)`：设置流程图中的节点。
- `add_nodes(nodes)`：向流程图添加节点。
- `get_node(id)`：根据 ID 返回节点。
- `update_node(id, node_update, replace=False)`：更新流程图中的某个节点。
- `update_node_data(id, data_update, replace=False)`：更新流程图中某个节点的数据。

## 边钩子（Edge Hooks）

- `get_edges()`：返回流程图中所有边的数组。
- `set_edges(edges)`：设置流程图中的边。
- `add_edges(edges)`：向流程图添加边。
- `get_edge(id)`：根据 ID 返回边。
- `update_edge(id, edge_update, replace=False)`：更新流程图中的某条边。
- `update_edge_data(id, data_update, replace=False)`：更新流程图中某条边的数据。

## 视口钩子（Viewport Hooks）

- `screen_to_flow_position(x, y, snap_to_grid=False)`：将屏幕像素位置转换为流程图内的位置。
- `flow_to_screen_position(x, y)`：将流程图画布内的位置转换为屏幕像素位置。

## 其他钩子

- `to_object()`：将 React Flow 状态转换为 JSON 对象。
- `get_intersecting_nodes(node, partially=True, nodes=None)`：查找当前与给定节点或矩形相交的所有节点。
- `get_node_connections(id=None, handle_type=None, handle_id=None)`：此钩子返回特定节点、连接点类型（"source"、"target"）或连接点 ID 上的连接数组。
- `get_connection()`：当存在活动的连接交互时，返回当前的连接状态。
