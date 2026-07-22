---
meta_description: "Enable pivot mode in AG Grid with Reflex. Build pivot tables in Python — group rows, pivot columns, and aggregate values in an interactive data grid, no JavaScript required."
---

```python exec
import reflex as rx
import reflex_enterprise as rxe
```

# 透视模式（Pivot Mode）

透视模式允许你以不同于数据源中原始结构的方式来可视化数据。当对某一列进行透视时，该列中的值将用作列标题。这允许你以更紧凑的方式查看数据，当你有大量数据需要显示时非常有用。

要启用透视模式，在网格属性中将 `pivot_mode` 属性设置为 `True`。启用透视模式后，你可以通过在列定义中设置 `pivot` 属性来定义要透视的列。除了透视列之外，至少有一个列定义必须将 `row_group` 属性设置为 `True` 来定义行分组。

你还可以通过在列定义中传递 `agg_func` 属性来定义行的聚合方式。`agg_func` 属性应设置为一个表示要使用的聚合函数的字符串。内置的聚合函数有 `sum`、`min`、`max`、`count`、`avg`、`first` 和 `last`。

你可以在此处找到一个在线示例：[透视模式示例](https://aggrid.reflex.run/pivot)。

```python demo exec
import pandas as pd
import reflex as rx

import reflex_enterprise as rxe

# Olympic winners data (originally from https://www.ag-grid.com/example-assets/olympic-winners.json)
df = pd.read_json("data/olympic-winners.json")


def pivot_page():
    return (
        rxe.ag_grid(
            id="sandbox_grid",
            column_defs=[
                {"field": "country", "row_group": True},
                {"field": "sport", "pivot": True},
                {"field": "year", "pivot": True},
                {"field": "gold", "aggFunc": "sum"},
            ],
            loading=False,
            row_data=df.to_dict("records"),
            default_col_def={
                "flex": 1,
                "min_width": 130,
                "enable_value": True,
                "enable_row_group": True,
                "enable_pivot": True,
            },
            auto_group_column_def={
                "minWidth": 200,
                "pinned": "left",
            },
            pivot_mode=True,
            side_bar="columns",
            pivot_panel_show="always",
            width="100%",
            height="500px",
        ),
    )
```

## 使用状态进行透视

```python demo exec
import pandas as pd
import reflex as rx

import reflex_enterprise as rxe

df = pd.read_csv("data/wind_dataset.csv")


class PivotState(rx.State):
    """State for the sandbox page."""

    pivot = False
    row_grouping = False

    @rx.event
    def toggle_pivot(self):
        """Toggle the pivot."""
        self.pivot = not self.pivot

    @rx.event
    def toggle_row_grouping(self):
        """Toggle the row grouping."""
        self.row_grouping = not self.row_grouping


def sandbox_page():
    """Sandbox page."""
    return rx.vstack(
        rx.hstack(
            rx.text("Toggle Pivot"),
            rx.switch(
                on_click=PivotState.toggle_pivot,
                name="pivot",
                checked=PivotState.pivot,
            ),
            rx.text("Toggle Row Grouping"),
            rx.switch(
                on_click=PivotState.toggle_row_grouping,
                name="row_grouping",
                checked=PivotState.row_grouping,
            ),
        ),
        rxe.ag_grid(
            id="sandbox_grid",
            column_defs=[
                rxe.ag_grid.column_def(
                    field="direction",
                    pivot=True,
                ),
                rxe.ag_grid.column_def(
                    field="strength",
                ),
                rxe.ag_grid.column_def(
                    field="frequency",
                    agg_func="count",
                    row_group=PivotState.row_grouping,
                ),
            ],
            row_data=df.to_dict("records"),
            pivot_mode=PivotState.pivot,
            pivot_panel_show="onlyWhenPivoting",
            width="100%",
            height="500px",
        ),
        width="100%",
    )
```

📊 **数据集来源：** [wind_dataset.csv](https://raw.githubusercontent.com/plotly/datasets/master/wind_dataset.csv)


