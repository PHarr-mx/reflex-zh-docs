# 流程图组件（Flow Components）

本页介绍 `rxe.flow` 库提供的主要组件。

## rxe.flow.provider

`FlowProvider` 组件是一个上下文提供器（context provider），它使得在 `<ReactFlow />` 组件外部访问流程图的内部状态成为可能。我们提供的许多钩子（Hooks）都依赖此组件才能工作。

**属性（Props）：**

- `initial_nodes`：`Sequence[Node]` - 这些节点用于初始化流程图。它们不是动态的。
- `default_edges`：`Sequence[Edge]` - 这些边用于初始化流程图。它们不是动态的。
- `initial_width`：`float` - 初始宽度是在服务器端使用 fitView 所必需的。
- `initial_height`：`float` - 初始高度是在服务器端使用 fitView 所必需的。
- `fit_view`：`bool` - 当为 true 时，流程图会进行缩放和平移以适配初始提供的所有节点。
- `initial_fit_view_options`：`FitViewOptions` - 你可以提供一个选项对象来自定义初始 fitView 行为。
- `initial_min_zoom`：`float` - 初始最小缩放级别。
- `initial_max_zoom`：`float` - 初始最大缩放级别。
- `node_origin`：`NodeOrigin` - 在将节点放置到流程图中或查找其 x 和 y 位置时所使用的节点原点。
- `node_extent`：`CoordinateExtent` - 节点可移动的边界范围。

## rxe.flow

`Flow` 组件是渲染流程图的主组件。它接收节点和边，并为用户交互提供事件处理程序。

**属性（Props）：**

- `nodes`：`Sequence[Node]` - 在受控流程图中渲染的节点数组。
- `edges`：`Sequence[Edge]` - 在受控流程图中渲染的边数组。
- `default_nodes`：`Sequence[Node]` - 在非受控流程图中渲染的初始节点。
- `default_edges`：`Sequence[Edge]` - 在非受控流程图中渲染的初始边。
- `node_types`：`Mapping[str, Any]` - 自定义节点类型。
- `edge_types`：`Mapping[str, Any]` - 自定义边类型。
- `on_nodes_change`：节点发生变化时的事件处理程序。
- `on_edges_change`：边发生变化时的事件处理程序。
- `on_connect`：建立连接时的事件处理程序。
- `fit_view`：`bool` - 当为 true 时，流程图会进行缩放和平移以适配初始提供的所有节点。
- `fit_view_options`：`FitViewOptions` - `fit_view` 的选项。
- `style`：组件的样式。

## rxe.flow.background

`Background` 组件为流程图渲染背景。它可以是线条、点或十字图案。

**属性（Props）：**

- `color`：`str` - 图案的颜色。
- `bg_color`：`str` - 背景的颜色。
- `variant`：`Literal["lines", "dots", "cross"]` - 要渲染的图案类型。
- `gap`：`float | tuple[float, float]` - 图案之间的间距。
- `size`：`float` - 图案元素的尺寸。

**示例：**

```python
rxe.flow.background(variant="dots", gap=20, size=1)
```

## rxe.flow.controls

`Controls` 组件渲染一个面板，其中包含用于放大、缩小、适配视图和锁定视口的按钮。

**属性（Props）：**

- `show_zoom`：`bool` - 是否显示缩放按钮。
- `show_fit_view`：`bool` - 是否显示适配视图按钮。
- `show_interactive`：`bool` - 是否显示锁定按钮。
- `position`：`PanelPosition` - 控件在画布上的位置。

**示例：**

```python
rxe.flow.controls()
```

## rxe.flow.mini_map

`MiniMap` 组件渲染流程图的小型概览图。

**属性（Props）：**

- `node_color`：`str | Any` - 小地图上节点的颜色。
- `node_stroke_color`：`str | Any` - 小地图上节点的描边颜色。
- `pannable`：`bool` - 决定你是否可以通过在小地图内拖动来平移视口。
- `zoomable`：`bool` - 决定你是否可以通过在小地图内滚动来缩放视口。
- `position`：`PanelPosition` - 小地图在画布上的位置。

**示例：**

```python
rxe.flow.mini_map(pannable=True, zoomable=True)
```
