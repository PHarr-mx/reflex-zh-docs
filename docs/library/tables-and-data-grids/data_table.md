---
components:
  - rx.data_table
meta_description: "Display data in an interactive table in Python with Reflex. The rx.data_table component turns a pandas DataFrame into a searchable, sortable, paginated data table — all in pure Python."
---

```python exec
import reflex as rx
```

# Data Table（数据表格）

Reflex 的数据表格组件（`rx.data_table`）是在纯 Python 中将静态数据（如 pandas DataFrame）显示为交互式表格的好方法。
你可以将 pandas dataframe 传递给 data prop 来创建表格，内置搜索、排序和分页功能。

在此示例中，我们将从 csv 文件读取数据，将其转换为 pandas dataframe，并在 data_table 中显示。

我们还将为 data_table 添加搜索、分页和排序功能，使其更易于使用。

如果你想在应用中[添加、编辑或删除数据](/docs/library/tables-and-data-grids/table)，或者处理的不是静态数据，那么 [`rx.table`](/docs/library/tables-and-data-grids/table) 可能更适合你的用例。

```python demo box
rx.data_table(
    data=[
        ["Avery Bradley", "6-2", 25.0],
        ["Jae Crowder", "6-6", 25.0],
        ["John Holland", "6-5", 27.0],
        ["R.J. Hunter", "6-5", 22.0],
        ["Jonas Jerebko", "6-10", 29.0],
        ["Amir Johnson", "6-9", 29.0],
        ["Jordan Mickey", "6-8", 21.0],
        ["Kelly Olynyk", "7-0", 25.0],
        ["Terry Rozier", "6-2", 22.0],
        ["Marcus Smart", "6-4", 22.0],
    ],
    columns=["Name", "Height", "Age"],
    pagination=True,
    search=True,
    sort=True,
)
```

```python
import pandas as pd

nba_data = pd.read_csv("data/nba.csv")
...
rx.data_table(
    data=nba_data[["Name", "Height", "Age"]],
    pagination=True,
    search=True,
    sort=True,
)
```

📊 **数据集来源：** [nba.csv](https://media.geeksforgeeks.org/wp-content/uploads/nba.csv)

下面的示例展示了如何从列表创建数据表格。

```python
class State(rx.State):
    data: List = [["Lionel", "Messi", "PSG"], ["Christiano", "Ronaldo", "Al-Nasir"]]
    columns: List[str] = ["First Name", "Last Name"]


def index():
    return rx.data_table(
        data=State.data,
        columns=State.columns,
    )
```

## 相关内容

探索在 Reflex 中处理表格数据的其他方法，全部使用纯 Python：

- [Table](/docs/library/tables-and-data-grids/table)
- [Data Editor](/docs/library/tables-and-data-grids/data-editor)
- [表格和数据网格](/docs/library/tables-and-data-grids/)
