---
meta_description: "Transform and format AG Grid cell values in Python with Reflex. Use value getters, formatters, and setters to control how data displays in your data grid."
order: 2
---

```python exec
import reflex as rx
import reflex_enterprise as rxe
```

# 值转换器（Value Transformers）

AgGrid 允许你根据网格的列应用转换器。这允许你在将数据显示在网格之前对数据执行操作，而无需在后端预处理数据，从而减少应用的负载。

目录：
- [值获取器（Value Getter）](#value-getter)
- [值格式化器（Value Formatter）](#value-formatter)

## 值获取器（Value Getter）

`value_getter` 是列定义的一个属性，允许你定义一个将被调用以获取单元格值的函数。此函数将接收行数据作为参数，并应返回要显示在单元格上的值。

如果你有两列 `col_a` 和 `col_b`，并且想在第三列 `sum` 中显示这两列的和，你可以如下定义 `sum` 的 `value_getter`：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


df = pd.DataFrame({"col_a": [1, 2, 3, 4, 5], "col_b": [10, 20, 30, 40, 50]})

column_defs = [
    {"field": "col_a", "header_name": "Column A"},
    {"field": "col_b", "header_name": "Column B"},
    {
        "field": "sum",
        "header_name": "Sum",
        "value_getter": "params.data.col_a + params.data.col_b",
    },
    rxe.ag_grid.column_def(
        field="diff",
        header_name="Difference",
        value_getter="params.data.col_b - params.data.col_a",
    ),
]


def ag_grid_value_getter():
    return rxe.ag_grid(
        id="ag_grid_value_getter",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        width="100%",
    )
```

## 值格式化器（Value Formatter）

`value_formatter` 是列定义的一个属性，允许你定义一个将被调用以格式化单元格值的函数。此函数将接收单元格的值作为参数，并应返回要显示在单元格上的格式化值。

如果你有一列 `price`，并且想显示带有货币符号的价格，你可以如下定义 `price` 的 `value_formatter`：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd

df = pd.DataFrame({
    "product_name": ["Product A", "Product B", "Product C", "Product D", "Product E"],
    "price": [100, 200, 300, 400, 500],
})
column_defs = [
    {"field": "product_name", "header_name": "Product Name"},
    {
        "field": "price",
        "header_name": "Price ($)",
        "value_formatter": "'$' + params.value",
    },
    rxe.ag_grid.column_def(
        col_id="price_eur",
        header_name="Price (€)",
        value_formatter="params.data.price + ' €'",
    ),
]


def ag_grid_value_formatter():
    return rxe.ag_grid(
        id="ag_grid_value_formatter",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        width="100%",
    )
```


