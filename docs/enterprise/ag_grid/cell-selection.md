---
meta_description: "Configure cell and range selection in AG Grid with Reflex. Track selected cells and ranges in your Python data grid, all in pure Python, no JavaScript required."
title: "Cell Selection"
order: 8
---

# 单元格选择（Cell Selection）

AG Grid 提供了强大的单元格选择功能，允许用户选择单个单元格或单元格范围。此功能对于数据操作、复制以及填充手柄操作等高级交互至关重要。

## 范围选择（Range Selection）

要在 AG Grid 中启用单元格选择，将 `cell_selection` 属性设置为 `True`。这会自动启用单单元格选择和范围选择功能。

### 基本选择示例

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class CellSelectionState(rx.State):
    data: list[dict] = []

    @rx.event
    def load_data(self):
        df = pd.read_json("https://www.ag-grid.com/example-assets/olympic-winners.json")
        self.data = df.head(10).to_dict("records")

    @rx.event
    def echo_selection(self, ranges: list[dict], started: bool, finished: bool):
        if finished:
            yield rx.toast(f"Selected ranges: {ranges}")


column_defs = [
    {"field": "athlete", "width": 150},
    {"field": "age", "width": 90},
    {"field": "country", "width": 120},
    {"field": "year", "width": 90},
    {"field": "sport", "width": 120},
    {"field": "gold", "width": 100},
    {"field": "silver", "width": 100},
    {"field": "bronze", "width": 100},
]


def basic_cell_selection():
    return rx.vstack(
        rx.text(
            "Click and drag to select cells. Selection info will appear in a toast.",
            size="2",
        ),
        rxe.ag_grid(
            id="basic_cell_selection_grid",
            column_defs=column_defs,
            row_data=CellSelectionState.data,
            cell_selection=True,
            on_cell_selection_changed=CellSelectionState.echo_selection,
            width="100%",
            height="400px",
        ),
        on_mount=CellSelectionState.load_data,
        width="100%",
    )
```

### 高级选择事件处理

对于更复杂的选择处理，你可以处理选择范围来计算详细信息：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class AdvancedSelectionState(rx.State):
    data: list[dict] = []

    @rx.event
    def load_data(self):
        df = pd.DataFrame({
            "name": ["Alice", "Bob", "Charlie", "Diana", "Eve"],
            "score": [85, 92, 78, 96, 88],
            "grade": ["B", "A", "C", "A", "B"],
            "attempts": [3, 2, 4, 1, 3],
        })
        self.data = df.to_dict("records")

    @rx.event
    def handle_selection(self, ranges: list[dict], started: bool, finished: bool):
        if finished and ranges:
            total_cells = sum(
                (r.get("endRow", 0) - r.get("startRow", 0) + 1)
                * len(r.get("columns", []))
                for r in ranges
            )
            yield rx.toast(f"Selected {total_cells} cells across {len(ranges)} ranges")


editable_column_defs = [
    {"field": "name", "width": 120},
    {"field": "score", "width": 100, "editable": True},
    {"field": "grade", "width": 100, "editable": True},
    {"field": "attempts", "width": 120, "editable": True},
]


def advanced_selection_example():
    return rx.vstack(
        rx.text(
            "Select ranges of cells. Try selecting multiple ranges by holding Ctrl/Cmd.",
            size="2",
        ),
        rxe.ag_grid(
            id="advanced_selection_grid",
            column_defs=editable_column_defs,
            row_data=AdvancedSelectionState.data,
            cell_selection=True,
            on_cell_selection_changed=AdvancedSelectionState.handle_selection,
            width="100%",
            height="300px",
        ),
        on_mount=AdvancedSelectionState.load_data,
        width="100%",
    )
```

## 填充手柄（Fill Handle）

填充手柄是一个强大的功能，允许用户通过从选中的单元格或范围拖动来快速填充单元格。启用后，选择区域的右下角会出现一个小方块，用户可以拖动它来填充相邻单元格。

### 启用填充手柄

要启用填充手柄，使用包含手柄配置的字典来配置 `cell_selection` 属性：

```python
cell_selection = {
    "handle": {
        "mode": "fill",  # 启用填充手柄
    }
}
```

### 填充手柄事件

使用填充手柄时，它会为每个接收填充值的单元格触发 `on_cell_value_changed`。这允许你的后端适当处理数据更改。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class FillHandleState(rx.State):
    data: list[dict] = []
    change_log: list[str] = []

    @rx.event
    def load_data(self):
        df = pd.DataFrame({
            "item": ["Apple", "Banana", "Cherry", "Date", "Elderberry"],
            "quantity": [10, 15, 8, 12, 20],
            "price": [1.50, 0.75, 2.00, 3.00, 4.50],
            "total": [15.00, 11.25, 16.00, 36.00, 90.00],
        })
        self.data = df.to_dict("records")

    @rx.event
    def handle_cell_change(self, data: dict):
        row_index = data.get("rowIndex", 0)
        field = data.get("colId", "")
        new_value = data.get("newValue", "")
        old_value = data.get("oldValue", "")

        change_msg = f"Row {row_index + 1}, {field}: '{old_value}' → '{new_value}'"
        self.change_log = [change_msg] + self.change_log[:9]  # 保留最近 10 条更改

        # 更新数据
        if 0 <= row_index < len(self.data):
            self.data[row_index][field] = new_value


fill_column_defs = [
    {"field": "item", "width": 120},
    {"field": "quantity", "width": 100, "editable": True, "type": "numericColumn"},
    {"field": "price", "width": 100, "editable": True, "type": "numericColumn"},
    {"field": "total", "width": 100, "editable": True, "type": "numericColumn"},
]


def fill_handle_example():
    return rx.vstack(
        rx.text(
            "Select a cell and drag the fill handle (small square at bottom-right) to fill adjacent cells.",
            size="2",
        ),
        rxe.ag_grid(
            id="fill_handle_grid",
            column_defs=fill_column_defs,
            row_data=FillHandleState.data,
            cell_selection={
                "handle": {
                    "mode": "fill",  # 启用填充手柄
                }
            },
            on_cell_value_changed=FillHandleState.handle_cell_change,
            width="100%",
            height="300px",
        ),
        rx.divider(),
        rx.text("Recent Changes:", weight="bold", size="3"),
        rx.cond(
            FillHandleState.change_log,
            rx.vstack(
                rx.foreach(
                    FillHandleState.change_log,
                    lambda change: rx.text(change, size="1", color="gray"),
                ),
                spacing="1",
            ),
            rx.text("No changes yet", size="2", color="gray"),
        ),
        on_mount=FillHandleState.load_data,
        width="100%",
        spacing="4",
    )
```

## 高级配置选项

你可以使用额外的配置选项进一步自定义单元格选择行为：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class ConfigurationState(rx.State):
    data: list[dict] = []

    @rx.event
    def load_data(self):
        df = pd.DataFrame({
            "id": range(1, 8),
            "name": [
                "Product A",
                "Product B",
                "Product C",
                "Product D",
                "Product E",
                "Product F",
                "Product G",
            ],
            "category": [
                "Electronics",
                "Clothing",
                "Electronics",
                "Books",
                "Clothing",
                "Electronics",
                "Books",
            ],
            "price": [299.99, 49.99, 199.99, 24.99, 79.99, 399.99, 19.99],
            "stock": [15, 32, 8, 45, 23, 12, 67],
        })
        self.data = df.to_dict("records")


configuration_column_defs = [
    {"field": "id", "width": 80},
    {"field": "name", "width": 150, "editable": True},
    {"field": "category", "width": 120},
    {"field": "price", "width": 100, "editable": True, "type": "numericColumn"},
    {"field": "stock", "width": 100, "editable": True, "type": "numericColumn"},
]


def configuration_example():
    return rx.vstack(
        rx.text("Cell selection with additional configuration options", size="2"),
        rxe.ag_grid(
            id="configuration_grid",
            column_defs=configuration_column_defs,
            row_data=ConfigurationState.data,
            cell_selection={
                "handle": {
                    "mode": "fill",
                }
            },
            enable_cell_text_selection=True,  # 允许单元格内文本选择
            suppress_cell_focus=False,  # 允许单元格聚焦
            width="100%",
            height="350px",
        ),
        on_mount=ConfigurationState.load_data,
        width="100%",
    )
```

## 主要功能

- **单元格选择**：使用 `cell_selection=True` 启用单单元格和范围选择功能
- **填充手柄**：使用 `cell_selection={"handle": {"mode": "fill"}}` 配置拖动填充功能
- **事件处理**：使用 `on_cell_selection_changed` 响应选择变化
- **值更改**：使用 `on_cell_value_changed` 处理单个单元格编辑和填充操作
- **文本选择**：启用 `enable_cell_text_selection=True` 允许单元格内文本选择

## 最佳实践

1. **使用 cell_selection 配置**：`cell_selection=True` 会自动启用单单元格和范围选择，为填充操作提供所有必要的选择功能。

2. **处理单元格值更改**：使用填充手柄时，实现 `on_cell_value_changed` 以在后端处理数据更新。

3. **提供用户反馈**：使用 toast 或其他 UI 元素来确认选择操作和数据更改。

4. **考虑性能**：对于大型数据集，注意频繁的单元格值更改事件对性能的影响。

5. **验证填充操作**：在 `on_cell_value_changed` 处理器中实现验证逻辑以确保数据完整性。
