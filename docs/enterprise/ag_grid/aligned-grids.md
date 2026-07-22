---
meta_description: "Build aligned grids with AG Grid in Reflex. Synchronize columns and horizontal scrolling across multiple data grids in Python, no JavaScript required."
title: Aligned Grids
---

# 对齐网格（Aligned Grids）

AgGrid 提供了一种将多个网格对齐在一起的方式。当你想以同步的方式显示相关数据时，这非常有用。

你可以通过 `aligned_grids` 属性来实现。此属性接受一个你想要对齐的网格 ID 列表。

```python demo exec
import pandas as pd
import reflex as rx
import reflex_enterprise as rxe

# Olympic winners data (originally from https://www.ag-grid.com/example-assets/olympic-winners.json)
df = pd.read_json("data/olympic-winners.json")

row_data = df.to_dict("records")

column_defs = [
    {"field": "athlete"},
    {"field": "age"},
    {"field": "country"},
    {"field": "year"},
    {"field": "sport"},
    {
        "header_name": "Medals",
        "children": [
            {
                "field": "total",
                "column_group_show": "closed",
                "col_id": "total",
                "value_getter": "params.data.gold + params.data.silver + params.data.bronze",
                "width": 100,
            },
            {"field": "gold", "column_group_show": "open", "width": 100},
            {"field": "silver", "column_group_show": "open", "width": 100},
            {"field": "bronze", "column_group_show": "open", "width": 100},
        ],
    },
]


def aligned_grids_page():
    """Aligned grids demo."""
    return rx.el.div(
        rxe.ag_grid(
            id="grid1",
            column_defs=column_defs,
            row_data=row_data,
            aligned_grids=["grid2"],
            width="100%",
        ),
        rxe.ag_grid(
            id="grid2",
            column_defs=column_defs,
            row_data=row_data,
            aligned_grids=["grid1"],
            width="100%",
        ),
        class_name="flex flex-col gap-y-6 w-full",
    )
```

```md alert warning
# 透视功能不适用于对齐网格。这是因为透视数据会改变列，这会使对齐网格不兼容，因为它们不再共享相同的列集合。
```
