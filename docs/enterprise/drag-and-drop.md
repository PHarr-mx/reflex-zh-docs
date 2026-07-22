---
title: Drag and Drop
---

```python exec
import reflex as rx
import reflex_enterprise as rxe
```

# 拖放（Drag and Drop）

Reflex Enterprise 通过 `rxe.dnd` 模块提供全面的拖放功能，用于创建交互式 UI 元素。它基于 react-dnd 构建，既提供了适用于常见用例的高级组件，也提供了适用于高级场景的底层钩子。

```md alert warning
# 重要：始终使用 `@rx.memo` 装饰定义 `rxe.dnd.draggable` 组件的函数，以避免编译错误。

参阅 [memo](/docs/library/other/memo) 了解 `@rx.memo` 组件如何处理参数。
```

## 基本用法

### 简单拖放

以下是一个基本示例，展示如何创建可拖拽项和放置目标：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class BasicDndState(rx.State):
    drop_count: int = 0

    def increment_drop_count(self):
        self.drop_count += 1


@rx.memo
def draggable_card() -> rxe.dnd.Draggable:
    return rxe.dnd.draggable(
        rx.card(
            rx.text("Drag me!", weight="bold"),
            rx.text("I can be moved around"),
            bg="blue.500",
            color="white",
            p=4,
            cursor="grab",
            width="200px",
            height="100px",
        ),
        type="BasicCard",
        item={"message": "Hello from draggable!"},
    )


def basic_drag_drop():
    return rx.vstack(
        rx.text(f"Items dropped: {BasicDndState.drop_count}"),
        rx.hstack(
            draggable_card(),
            rxe.dnd.drop_target(
                rx.box(
                    "Drop Zone",
                    bg="gray.100",
                    border="2px dashed gray",
                    min_height="150px",
                    min_width="200px",
                    display="flex",
                    align_items="center",
                    justify_content="center",
                    font_weight="bold",
                ),
                accept=["BasicCard"],
                on_drop=BasicDndState.increment_drop_count,
            ),
            spacing="4",
            align="start",
        ),
        spacing="4",
    )
```

### 多位置拖放

创建一个可以在多个放置目标之间移动的可拖拽项：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class MultiPositionState(rx.State):
    card_position: int = 0

    def set_card_position(self, position: int):
        self.card_position = position


@rx.memo
def movable_card() -> rxe.dnd.Draggable:
    return rxe.dnd.draggable(
        rx.card(
            rx.text("Movable Card", weight="bold"),
            rx.text("Position: " + MultiPositionState.card_position.to_string()),
            bg="purple.500",
            color="white",
            p=4,
            width="180px",
            height="120px",
        ),
        type="MovableCard",
        border="2px solid purple",
    )


def drop_zone(position: int):
    params = rxe.dnd.DropTarget.collected_params
    return rxe.dnd.drop_target(
        rx.cond(
            MultiPositionState.card_position == position,
            movable_card(),
            rx.box(f"Drop Zone {position}", color="gray.600", font_weight="bold"),
        ),
        width="200px",
        height="200px",
        border="2px solid red",
        border_color=rx.cond(params.is_over, "green.500", "red.500"),
        bg=rx.cond(params.is_over, "green.100", "blue.100"),
        accept=["MovableCard"],
        on_drop=lambda _: MultiPositionState.set_card_position(position),
        display="flex",
        align_items="center",
        justify_content="center",
    )


def multi_position_example():
    return rx.vstack(
        rx.text("Drag the card between positions", weight="bold"),
        rx.grid(
            drop_zone(0),
            drop_zone(1),
            drop_zone(2),
            drop_zone(3),
            columns="2",
            spacing="4",
        ),
        spacing="4",
    )
```

## 高级功能

### 使用收集参数进行状态跟踪

使用收集参数（Collected Parameters）访问拖放状态信息：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class StateTrackingState(rx.State):
    drag_info: str = "No drag activity"

    def set_drag_info(self, value: str):
        self.drag_info = value


@rx.memo
def tracked_draggable() -> rxe.dnd.Draggable:
    drag_params = rxe.dnd.Draggable.collected_params
    return rxe.dnd.draggable(
        rx.card(
            rx.text("Tracked Draggable"),
            rx.text(rx.cond(drag_params.is_dragging, "Dragging...", "Ready to drag")),
            bg=rx.cond(drag_params.is_dragging, "orange.500", "blue.500"),
            color="white",
            p=4,
            opacity=rx.cond(drag_params.is_dragging, 0.5, 1.0),
        ),
        type="TrackedItem",
        on_end=StateTrackingState.set_drag_info("Drag ended"),
    )


def tracked_drop_target():
    drop_params = rxe.dnd.DropTarget.collected_params
    return rxe.dnd.drop_target(
        rx.box(
            rx.text("Smart Drop Zone"),
            rx.text(rx.cond(drop_params.is_over, "Ready to receive!", "Waiting...")),
            bg=rx.cond(drop_params.is_over, "green.200", "gray.100"),
            border=rx.cond(drop_params.is_over, "2px solid green", "2px dashed gray"),
            p=4,
            min_height="150px",
            display="flex",
            flex_direction="column",
            align_items="center",
            justify_content="center",
        ),
        accept=["TrackedItem"],
        on_drop=StateTrackingState.set_drag_info("Item successfully dropped!"),
        on_hover=StateTrackingState.set_drag_info("Item hovering over drop zone"),
    )


def state_tracking_example():
    return rx.vstack(
        rx.text(f"Status: {StateTrackingState.drag_info}"),
        rx.hstack(tracked_draggable(), tracked_drop_target(), spacing="4"),
        spacing="4",
    )
```

### 带拖放功能的动态列表

使用 `rx.foreach` 创建动态可拖拽列表。始终将稳定的项目标识符作为 `key` 属性传递给 foreach 渲染的最外层组件，以确保在底层列表中的项目重新排列时能够跟踪项目身份。

```python demo exec
import dataclasses
import reflex as rx
import reflex_enterprise as rxe


@dataclasses.dataclass
class ListItem:
    id: str
    text: str
    list_id: str


class DynamicListState(rx.State):
    list_a: list[ListItem] = [
        ListItem(id="1", text="Item 1", list_id="A"),
        ListItem(id="2", text="Item 2", list_id="A"),
        ListItem(id="3", text="Item 3", list_id="A"),
    ]
    list_b: list[ListItem] = [
        ListItem(id="4", text="Item 4", list_id="B"),
        ListItem(id="5", text="Item 5", list_id="B"),
    ]

    def move_item(self, item_data: dict, target_list: str):
        item_id = item_data.get("id")
        source_list = item_data.get("list_id")

        if not item_id or not source_list:
            return

        # 在源列表中查找项目
        source_items = getattr(self, f"list_{source_list.lower()}")
        item_to_move = None
        for item in source_items:
            if item.id == item_id:
                item_to_move = item
                break

        if not item_to_move:
            return

        # 仅从源列表中移除
        if source_list == "A":
            self.list_a = [item for item in self.list_a if item.id != item_id]
        else:
            self.list_b = [item for item in self.list_b if item.id != item_id]

        # 为目标列表创建新项目
        new_item = ListItem(id=item_id, text=item_to_move.text, list_id=target_list)

        # 添加到目标列表
        if target_list == "A":
            self.list_a.append(new_item)
        else:
            self.list_b.append(new_item)


@rx.memo
def draggable_list_item(item: rx.Var[ListItem]) -> rx.Component:
    return rxe.dnd.draggable(
        rx.card(
            rx.text(item.text, weight="bold"),
            rx.text(f"From List {item.list_id}", size="2", color="gray.600"),
            p=3,
            cursor="grab",
            _hover={"bg": "gray.50"},
        ),
        type="ListItem",
        item={"id": item.id, "text": item.text, "list_id": item.list_id},
    )


def droppable_list(title: str, items: list[ListItem], list_id: str):
    return rxe.dnd.drop_target(
        rx.vstack(
            rx.text(title, weight="bold", size="5"),
            rx.vstack(
                rx.foreach(
                    items,
                    lambda item, index: draggable_list_item(item=item, key=item.id),
                ),
                spacing="2",
                min_height="200px",
                width="100%",
            ),
            bg="gray.50",
            p=4,
            border_radius="md",
            border="2px dashed gray",
            width="250px",
        ),
        accept=["ListItem"],
        on_drop=lambda item: DynamicListState.move_item(item, list_id),
    )


def dynamic_list_example():
    return rx.hstack(
        droppable_list("List A", DynamicListState.list_a, "A"),
        droppable_list("List B", DynamicListState.list_b, "B"),
        spacing="6",
        align="start",
    )
```

## 核心组件

### Draggable

`rxe.dnd.draggable` 组件使任何元素变为可拖拽：

**关键属性：**
- `type`：用于拖拽类型匹配的字符串标识符
- `item`：传递给放置处理器的数据对象
- `on_end`：拖拽操作结束时调用

拖拽开始时不会触发事件——`on_end` 是唯一在拖拽完成时触发的拖拽生命周期事件。

### Drop Target

`rxe.dnd.drop_target` 组件创建接受可拖拽项的区域：

**关键属性：**
- `accept`：此目标接受的拖拽类型列表
- `on_drop`：项目被放置时调用
- `on_hover`：项目悬停在目标上方时调用

当放置目标嵌套时，一次放置会为光标下的每一层触发事件，因此需要在状态处理器中去重（例如使用时间戳或标志）。要支持在根级别放置——例如文件管理器的背景——请将根容器本身包装在放置目标中。

### 收集参数（Collected Parameters）

访问实时拖放状态：

**Draggable 参数（`rxe.dnd.Draggable.collected_params`）：**
- `is_dragging`：布尔值，指示项目是否正在被拖拽

**Drop Target 参数（`rxe.dnd.DropTarget.collected_params`）：**
- `is_over`：布尔值，指示可拖拽项是否正在悬停
- `can_drop`：布尔值，指示是否允许放置

```md alert warning
# 先将收集参数赋值给变量

不要直接从类上读取字段（例如 `rxe.dnd.DropTarget.is_over`）。将 `collected_params` 赋值给一个变量，然后通过它访问字段：`params = rxe.dnd.DropTarget.collected_params`，然后 `params.is_over`。
```

当单个 `@rx.memo` 组件渲染多个 draggable 或 drop target 时，为每个组件指定不同的收集参数名称以避免 JavaScript 名称冲突。使用 `_replace(_js_expr=...)` 创建唯一命名的参数 Var，并通过 `_collected_params` 传递。注意 `_replace` 和 `_js_expr` 是 Reflex 内部 API，可能在不同版本间发生变化：

```python
@rx.memo
def dual_drop_zones() -> rx.Component:
    left_params = rxe.dnd.DropTarget.collected_params._replace(
        _js_expr="leftZoneParams"
    )
    right_params = rxe.dnd.DropTarget.collected_params._replace(
        _js_expr="rightZoneParams"
    )
    return rx.hstack(
        rxe.dnd.drop_target(
            rx.box("Left", bg=rx.cond(left_params.is_over, "green.100", "gray.100")),
            accept=["MyDraggable"],
            _collected_params=left_params,
        ),
        rxe.dnd.drop_target(
            rx.box("Right", bg=rx.cond(right_params.is_over, "green.100", "gray.100")),
            accept=["MyDraggable"],
            _collected_params=right_params,
        ),
    )
```

## API 参考

### rxe.dnd.draggable

创建一个可在界面中移动的可拖拽组件。

**参数：**

- **`type`**（str，必填）：字符串标识符，必须与放置目标的 `accept` 列表匹配
- **`item`**（dict | Callable）：传递给放置处理器的数据对象。可以是静态字典或接收 `DragSourceMonitor` 并返回数据的函数
- **`preview_options`**（dict）：拖拽预览外观的配置
- **`options`**（dict）：额外的拖拽源选项，如 `dropEffect`
- **`on_end`**（EventHandler）：拖拽操作完成时调用的事件处理器
- **`can_drag`**（Callable）：确定项目是否可被拖拽的函数
- **`is_dragging`**（Callable）：覆盖默认拖拽状态检测的函数
- **`collect`**（Callable）：从拖拽监视器收集自定义属性的函数

### rxe.dnd.drop_target

创建一个可接收可拖拽项的放置目标。

**参数：**

- **`accept`**（str | list[str]，必填）：此目标接受的拖拽类型
- **`options`**（dict）：额外的放置目标配置选项
- **`on_drop`**（EventHandler）：项目被放置时调用的事件处理器，接收 `item` 数据
- **`on_hover`**（EventHandler）：项目悬停在目标上方时调用的事件处理器
- **`can_drop`**（Callable）：确定特定项目是否可被放置的函数
- **`collect`**（Callable）：从放置监视器收集自定义属性的函数

## 监视器类

### DragSourceMonitor

提供拖拽操作状态的信息：

- **`is_dragging()`**：如果此项目当前正在被拖拽则返回 `True`
- **`can_drag()`**：如果项目可被拖拽则返回 `True`
- **`get_item()`**：返回正在被拖拽的项目数据
- **`get_item_type()`**：返回拖拽类型字符串
- **`get_drop_result()`**：返回放置结果（在 `on_end` 中可用）
- **`did_drop()`**：如果项目已成功放置则返回 `True`

### DropTargetMonitor

提供放置目标状态的信息：

- **`is_over()`**：如果可拖拽项正在悬停在此目标上方则返回 `True`
- **`can_drop()`**：如果悬停的项目可被放置则返回 `True`
- **`get_item()`**：返回悬停的可拖拽项的项目数据
- **`get_item_type()`**：返回悬停项目的拖拽类型

## 默认收集参数

### Draggable.collected_params

```python
{
    "is_dragging": bool,  # 当此项目正在被拖拽时为 True
    "can_drag": bool,  # 当此项目可被拖拽时为 True
}
```

### DropTarget.collected_params

```python
{
    "is_over": bool,  # 当可拖拽项正在悬停时为 True
    "can_drop": bool,  # 当悬停的项目可被放置时为 True
    "item": dict | None,  # 来自悬停的可拖拽项的数据
}
```

## 高级用法示例

### 使用 Item 参数传递数据

`item` 参数允许你从可拖拽组件向放置处理器传递数据：

```python demo exec toggle
import reflex as rx
import reflex_enterprise as rxe


class SimpleState(rx.State):
    message: str = "No items dropped yet"

    def set_message_from_item(self, item: dict):
        self.message = f"Dropped: {item['name']}"


def simple_draggable():
    return rxe.dnd.draggable(
        rx.box("Drag me!", p=4, bg="blue.100", border="1px solid blue", cursor="grab"),
        type="simple",
        item={"name": "test_item", "value": 42},
    )


def simple_drop_target():
    return rxe.dnd.drop_target(
        rx.box(
            rx.text(SimpleState.message),
            p=4,
            bg="gray.100",
            border="2px dashed gray",
            min_height="100px",
        ),
        accept=["simple"],
        on_drop=SimpleState.set_message_from_item,
    )


def item_data_example():
    return rx.vstack(simple_draggable(), simple_drop_target(), spacing="4")
```

### 自定义 Collect 函数

`collect` 参数允许你实时访问拖放状态信息：

```python demo exec toggle
import reflex as rx
import reflex_enterprise as rxe


class CollectState(rx.State):
    drag_info: str = "No drag activity"
    drop_info: str = "No drop activity"

    def handle_drop(self, item: dict):
        self.drop_info = f"Dropped: {item.get('name', 'Unknown')}"
        return rx.toast(f"Successfully dropped {item.get('name', 'item')}")


def collect_draggable():
    params = rxe.dnd.Draggable.collected_params
    return rxe.dnd.draggable(
        rx.box(
            rx.vstack(
                rx.text("Drag me!", weight="bold"),
                rx.text(f"Dragging: {params.is_dragging}", size="2"),
                rx.text(f"Can drag: {params.can_drag}", size="2"),
                spacing="1",
            ),
            p=4,
            bg=rx.cond(params.is_dragging, "blue.200", "blue.100"),
            border="1px solid blue",
            cursor=rx.cond(params.is_dragging, "grabbing", "grab"),
            opacity=rx.cond(params.is_dragging, 0.7, 1.0),
        ),
        type="collect_item",
        item={"id": "collect_test", "name": "Test Item"},
    )


def collect_drop_target():
    params = rxe.dnd.DropTarget.collected_params
    return rxe.dnd.drop_target(
        rx.box(
            rx.vstack(
                rx.text("Drop Zone", weight="bold"),
                rx.text(f"Is over: {params.is_over}", size="2"),
                rx.text(f"Can drop: {params.can_drop}", size="2"),
                rx.cond(
                    params.item,
                    rx.text(
                        f"Hovering item: {params.item.get('name', 'Unknown')}", size="2"
                    ),
                    rx.text("No item hovering", size="2"),
                ),
                spacing="1",
            ),
            p=4,
            bg=rx.cond(
                params.is_over & params.can_drop,
                "green.200",
                rx.cond(params.is_over, "yellow.200", "gray.100"),
            ),
            border=rx.cond(
                params.is_over & params.can_drop,
                "2px solid green",
                rx.cond(params.is_over, "2px solid yellow", "2px dashed gray"),
            ),
            min_height="120px",
        ),
        accept=["collect_item"],
        on_drop=CollectState.handle_drop,
    )


def custom_collect_example():
    return rx.vstack(
        rx.text("Real-time Monitor State", weight="bold", size="4"),
        rx.hstack(
            collect_draggable(), collect_drop_target(), spacing="6", align="start"
        ),
        rx.text(CollectState.drop_info, size="2", color="gray.600"),
        spacing="4",
    )
```

## Provider

拖放功能需要 `rxe.dnd.provider` 组件包装你的应用。使用 `draggable` 或 `drop_target` 组件时会自动添加 provider。

手动控制：

```python
def app():
    return rxe.dnd.provider(
        # 你的应用内容
        your_app_content(),
        backend="HTML5",  # 或 "Touch" 用于移动端
    )
```

```md alert warning
# 不要添加第二个 provider

由于使用 `draggable` 或 `drop_target` 组件时会自动添加 provider，将应用额外包装在 `rxe.dnd.provider` 中会导致重复的 provider 并破坏拖放功能。仅在自动 provider 不适用时才使用手动控制（例如选择 touch 后端），并确保它是树中唯一的 provider。
```

## 最佳实践

1. **始终使用 `@rx.memo`** 装饰包含 draggable 组件的函数
2. **使用描述性的类型名称**以便于调试
3. **在放置处理器中处理边界情况**（无效项目等）
4. **使用收集参数提供视觉反馈**
5. **在移动设备上使用 touch 后端进行测试**
6. **保持项目数据轻量**以获得更好的性能

---

[← 返回主文档](/docs/enterprise/overview/)
