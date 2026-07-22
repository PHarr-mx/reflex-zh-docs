# 工具函数（Utility Functions）

`rxe.flow.util` 模块提供了用于操作流程图（Flow）组件的工具函数。

## 路径工具（Path Utilities）

这些函数用于计算边的路径。

- `get_simple_bezier_path(source_x, source_y, target_x, target_y, source_position="bottom", target_position="top")`：返回在两个节点之间渲染简单贝塞尔边所需的全部内容。
- `get_bezier_path(source_x, source_y, target_x, target_y, source_position="bottom", target_position="top", curvature=0.5)`：返回在两个节点之间渲染贝塞尔边所需的全部内容。
- `get_straight_path(source_x, source_y, target_x, target_y)`：计算两点之间的直线路径。
- `get_smooth_step_path(source_x, source_y, target_x, target_y, source_position="bottom", target_position="top", border_radius=5, center_x=None, center_y=None, offset=20, step_position=0.5)`：返回在两个节点之间渲染阶梯路径所需的全部内容。

## 变更处理程序（Change Handlers）

这些函数用于在 `on_nodes_change` 和 `on_edges_change` 事件处理程序中对节点和边应用变更。

- `apply_node_changes(nodes, changes)`：对流程图中的节点应用变更。
- `apply_edge_changes(edges, changes)`：对流程图中的边应用变更。

## 边与连接工具

- `add_edge(params, edges)`：在流程图中创建一条新边。

## 图工具（Graph Utilities）

- `get_incomers(node_id, nodes, edges)`：返回所有连接到给定节点的入向节点。
- `get_outgoers(node_id, nodes, edges)`：返回所有连接到给定节点的出向节点。
- `get_connected_edges(nodes, edges)`：返回所有连接到给定节点的边。
