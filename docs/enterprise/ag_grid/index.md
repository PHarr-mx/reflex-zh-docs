---
title: "AgGrid Overview"
order: 3
meta_description: "Use AG Grid in Python with Reflex. Build interactive, enterprise-grade data grids with sorting, filtering, pagination, and pivoting — all in pure Python."
---

# AG Grid

AG Grid（也写作 **ag-grid** 或 **aggrid**）是一个功能强大的 React 数据网格（Data Grid），而 Reflex 让你可以完全用 Python 来使用它——无需 JavaScript。它将企业级表格功能引入你的 Reflex 应用：支持排序、筛选、分页、行选择、透视等功能，AG Grid 彻底改变了你展示和操作表格数据的方式，无论数据来自 pandas DataFrame 还是应用的状态（State）。

[查看完整的 AG Grid 展示和示例](https://aggrid.reflex.run/)

## 你的第一个 Reflex AG Grid

一个基本的 Reflex AG Grid 包含列定义（Column Definitions）`column_defs`（定义网格中要显示的列）和 `row_data`（包含要在网格中显示的数据）。

每个网格还需要一个唯一的 `id`，用于唯一标识页面上的 Ag-Grid 实例。如果同一页面上有多个网格，每个网格必须有一个唯一的 `id`，以便正确渲染和管理。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


df = pd.read_csv("data/wind_dataset.csv")

column_defs = [
    {"field": "direction"},
    {"field": "strength"},
    {"field": "frequency"},
]


def ag_grid_simple():
    return rxe.ag_grid(
        id="ag_grid_basic_1",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        width="100%",
    )
```

📊 **数据集来源：** [wind_dataset.csv](https://raw.githubusercontent.com/plotly/datasets/master/wind_dataset.csv)

传递给 `row_data` 属性的数据格式是一个字典列表。每个字典代表网格中的一行，如下所示。

```python
[
    {"direction": "N", "strength": "0-1", "frequency": 0.5},
    {"direction": "NNE", "strength": "0-1", "frequency": 0.6},
    {"direction": "NE", "strength": "0-1", "frequency": 0.5},
]
```

前面的示例展示了完整写出的 `column_defs`。你也可以从 DataFrame 的列名中提取所需信息：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd

df = pd.read_csv("data/wind_dataset.csv")


def ag_grid_simple_2():
    return rxe.ag_grid(
        id="ag_grid_basic_2",
        row_data=df.to_dict("records"),
        column_defs=[{"field": i} for i in df.columns],
        width="100%",
        height="40vh",
    )
```

📊 **数据集来源：** [wind_dataset.csv](https://raw.githubusercontent.com/plotly/datasets/master/wind_dataset.csv)

## 表头（Headers）

在上面的示例中，提供的字段名的首字母在显示表头名称时会被大写。你可以通过在列定义中提供 `header_name` 键来自定义表头名称。在这个示例中，第二列和第三列的 `header_name` 被自定义了。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country"},
    {"field": "pop", "headerName": "Population"},
    {"field": "lifeExp", "headerName": "Life Expectancy"},
]


def ag_grid_simple_headers():
    return rxe.ag_grid(
        id="ag_grid_basic_headers",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        width="100%",
        height="40vh",
    )
```

📊 **数据集来源：** [gapminder2007.csv](https://raw.githubusercontent.com/plotly/datasets/master/gapminder2007.csv)

## 列筛选（Column Filtering）

通过在列定义中将 `filter` 键设置为 `True`，允许用户筛选某一列。在这个示例中，我们为第一列和最后一列启用了筛选。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country", "headerName": "Country", "filter": True},
    {"field": "pop", "headerName": "Population"},
    {"field": "lifeExp", "headerName": "Life Expectancy", "filter": True},
]


def ag_grid_simple_column_filtering():
    return rxe.ag_grid(
        id="ag_grid_basic_column_filtering",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        width="100%",
        height="40vh",
    )
```

### 筛选类型（Filter Types）

你可以设置 `filter=True` 来为某列启用默认筛选器。

你也可以使用 `filter` 键来设置筛选器类型。以下筛选器类型可用：`ag_grid.filters.date`、`ag_grid.filters.number` 和 `ag_grid.filters.text`。这些确保你输入到筛选器的内容是正确的类型。

（`ag_grid.filters.set` 和 `ag_grid.filters.multi` 在 AG Grid Enterprise 中可用）

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


df = pd.read_csv("data/GanttChart-updated.csv")

column_defs = [
    {"field": "Task", "filter": True},
    {"field": "Start", "filter": rxe.ag_grid.filters.date},
    {"field": "Duration", "filter": rxe.ag_grid.filters.number},
    {"field": "Resource", "filter": rxe.ag_grid.filters.text},
]


def ag_grid_column_filter_types():
    return rxe.ag_grid(
        id="ag_grid_basic_column_filtering",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        width="100%",
        height="40vh",
    )
```

📊 **数据集来源：** [GanttChart-updated.csv](https://raw.githubusercontent.com/plotly/datasets/master/GanttChart-updated.csv)

## 行排序（Row Sorting）

默认情况下，可以通过点击列标题按任意列对行进行排序。你可以通过在列定义中将 `sortable` 键设置为 `False` 来禁用某列的行排序。

在这个示例中，我们禁用了第一列的排序。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country", "sortable": False},
    {"field": "pop", "headerName": "Population"},
    {"field": "lifeExp", "headerName": "Life Expectancy"},
]


def ag_grid_simple_row_sorting():
    return rxe.ag_grid(
        id="ag_grid_basic_row_sorting",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        width="100%",
        height="40vh",
    )
```

📊 **数据集来源：** [gapminder2007.csv](https://raw.githubusercontent.com/plotly/datasets/master/gapminder2007.csv)

## 行选择（Row Selection）

行选择通过 `row_selection` 属性启用。将其设置为 `multiple` 允许用户一次选择多行。你可以使用 `checkbox_selection` 列定义属性来渲染用于选择的复选框。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country", "checkboxSelection": True},
    {"field": "pop", "headerName": "Population"},
    {"field": "continent"},
]


def ag_grid_simple_row_selection():
    return rxe.ag_grid(
        id="ag_grid_basic_row_selection",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        row_selection={"mode": "multiple"},
        width="100%",
        height="40vh",
    )
```

📊 **数据集来源：** [gapminder2007.csv](https://raw.githubusercontent.com/plotly/datasets/master/gapminder2007.csv)

## 编辑（Editing）

通过将 `editable` 属性设置为 `True` 来启用编辑。单元格编辑器会根据单元格数据类型自动推断。使用 `cell_editor` 属性设置单元格编辑器类型。

AG Grid 提供了 7 种单元格编辑器：

1. `ag_grid.editors.text`
2. `ag_grid.editors.large_text`
3. `ag_grid.editors.select`
4. `ag_grid.editors.rich_select`
5. `ag_grid.editors.number`
6. `ag_grid.editors.date`
7. `ag_grid.editors.checkbox`

在这个示例中，我们为第二列和第三列启用了编辑。第二列使用 `number` 单元格编辑器，第三列使用 `select` 单元格编辑器。

`on_cell_value_changed` 事件触发器（Event Trigger）连接到状态中的 `cell_value_changed` 事件处理器（Event Handler）。每当单元格值被更改时，这个事件处理器就会被调用，并更改后端变量 `_data_df` 和状态变量 `data` 的值。

```python
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class AGGridEditingState(rx.State):
    data: list[dict] = []
    _data_df: pd.DataFrame

    @rx.event
    def load_data(self):
        self._data_df = pd.read_csv("data/gapminder2007.csv")
        self.data = self._data_df.to_dict("records")

    @rx.event
    def cell_value_changed(self, row, col_field, new_value):
        self._data_df.at[row, col_field] = new_value
        self.data = self._data_df.to_dict("records")
        yield rx.toast(
            f"Cell value changed, Row: {row}, Column: {col_field}, New Value: {new_value}"
        )


column_defs = [
    {"field": "country"},
    {
        "field": "pop",
        "headerName": "Population",
        "editable": True,
        "cellEditor": rxe.ag_grid.editors.number,
    },
    {
        "field": "continent",
        "editable": True,
        "cellEditor": rxe.ag_grid.editors.select,
        "cellEditorParams": {
            "values": ["Asia", "Europe", "Africa", "Americas", "Oceania"]
        },
    },
]


def ag_grid_simple_editing():
    return rxe.ag_grid(
        id="ag_grid_basic_editing",
        row_data=AGGridEditingState.data,
        column_defs=column_defs,
        on_cell_value_changed=AGGridEditingState.cell_value_changed,
        on_mount=AGGridEditingState.load_data,
        width="100%",
        height="40vh",
    )
```

## 分页（Pagination）

默认情况下，网格使用垂直滚动。你可以通过添加分页来减少所需的滚动量。要添加分页，设置 `pagination=True`。你可以将 `pagination_page_size` 设置为每页的行数，将 `pagination_page_size_selector` 设置为供用户选择的选项列表。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd

df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country"},
    {"field": "pop", "headerName": "Population"},
    {"field": "lifeExp", "headerName": "Life Expectancy"},
]


def ag_grid_simple_pagination():
    return rxe.ag_grid(
        id="ag_grid_basic_pagination",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        pagination=True,
        pagination_page_size=10,
        pagination_page_size_selector=[10, 40, 100],
        width="100%",
        height="40vh",
    )
```

📊 **数据集来源：** [gapminder2007.csv](https://raw.githubusercontent.com/plotly/datasets/master/gapminder2007.csv)

## AG Grid 与状态（State）

### 将数据放入状态

假设你想对数据进行任何编辑，你可以将数据放入状态中。这允许你根据用户输入更新网格。每当 `data` 变量被更新时，网格将使用新数据重新渲染。

```python
from typing import Any
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class AGGridState2(rx.State):
    data: list[dict] = []

    @rx.event
    def load_data(self):
        _df = pd.read_csv("data/gapminder2007.csv")
        self.data = _df.to_dict("records")


column_defs = [
    {"field": "country"},
    {"field": "pop", "headerName": "Population"},
    {"field": "continent"},
]


def ag_grid_state_2():
    return rxe.ag_grid(
        id="ag_grid_state_2",
        row_data=AGGridState2.data,
        column_defs=column_defs,
        on_mount=AGGridState2.load_data,
        width="100%",
        height="40vh",
    )
```

### 使用状态更新网格

你可以使用状态根据用户输入来更新网格。在这个示例中，当用户点击按钮时，我们更新网格的 `column_defs`。

```python
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class AgGridState(rx.State):
    """The app state."""

    all_columns: list = []

    two_columns: list = []
    column_defs: list = all_columns
    n_clicks = 0

    @rx.event
    def init_columns(self):
        self.all_columns = [
            {"field": "country"},
            {"field": "pop"},
            {"field": "continent"},
            {"field": "lifeExp"},
            {"field": "gdpPercap"},
        ]
        self.two_columns = [
            {"field": "country"},
            {"field": "pop"},
        ]
        self.column_defs = self.all_columns

    @rx.event
    def update_columns(self):
        self.n_clicks += 1
        if self.n_clicks % 2 != 0:
            self.column_defs = self.two_columns
        else:
            self.column_defs = self.all_columns


df = pd.read_csv("data/gapminder2007.csv")


def ag_grid_simple_with_state():
    return rx.box(
        rx.button("Toggle Columns", on_click=AgGridState.update_columns),
        rxe.ag_grid(
            id="ag_grid_basic_with_state",
            row_data=df.to_dict("records"),
            column_defs=AgGridState.column_defs,
            on_mount=AgGridState.init_columns,
            width="100%",
            height="40vh",
        ),
        width="100%",
    )
```

📊 **数据集来源：** [gapminder2007.csv](https://raw.githubusercontent.com/plotly/datasets/master/gapminder2007.csv)

## AG Grid 与数据库数据

在这个示例中，我们将使用数据库来存储数据。数据从 csv 文件加载，并在页面加载时使用 `insert_dataframe_to_db` 事件处理器插入到数据库中。

然后使用 `data` 计算变量（Computed Var）从数据库中获取数据并显示在网格中。

当单元格值被更改时，使用 `cell_value_changed` 事件处理器在数据库中更新数据。

```python
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd
from sqlmodel import select


class Country(rx.Model, table=True):
    country: str
    population: int
    continent: str


class AGGridDatabaseState(rx.State):
    countries: list[Country]

    # 从 csv 加载的 DataFrame 中将数据插入数据库（在页面加载时执行）
    @rx.event
    def insert_dataframe_to_db(self):
        data = pd.read_csv("data/gapminder2007.csv")
        with rx.session() as session:
            for _, row in data.iterrows():
                db_record = Country(
                    country=row["country"],
                    population=row["pop"],
                    continent=row["continent"],
                )
                session.add(db_record)
            session.commit()

    # 使用计算变量从数据库中获取数据
    @rx.var
    def data(self) -> list[dict]:
        with rx.session() as session:
            results = session.exec(select(Country)).all()
            self.countries = [result.dict() for result in results]
        return self.countries

    # 当单元格值被更改时更新数据库
    @rx.event
    def cell_value_changed(self, row, col_field, new_value):
        self.countries[row][col_field] = new_value
        with rx.session() as session:
            country = Country(**self.countries[row])
            session.merge(country)
            session.commit()
        yield rx.toast(
            f"Cell value changed, Row: {row}, Column: {col_field}, New Value: {new_value}"
        )


column_defs = [
    {"field": "country"},
    {
        "field": "population",
        "headerName": "Population",
        "editable": True,
        "cellEditor": rxe.ag_grid.editors.number,
    },
    {
        "field": "continent",
        "editable": True,
        "cellEditor": rxe.ag_grid.editors.select,
        "cellEditorParams": {
            "values": ["Asia", "Europe", "Africa", "Americas", "Oceania"]
        },
    },
]


def index():
    return rxe.ag_grid(
        id="ag_grid_basic_editing",
        row_data=AGGridDatabaseState.data,
        column_defs=column_defs,
        on_cell_value_changed=AGGridDatabaseState.cell_value_changed,
        width="100%",
        height="40vh",
    )


# 将状态和页面添加到应用。
app = rx.App()
app.add_page(index, on_load=AGGridDatabaseState.insert_dataframe_to_db)
```

## 使用 AG Grid Enterprise

AG Grid 提供社区版和企业版。有关购买许可证密钥的详细信息，请参阅 [AG Grid 文档](https://www.ag-grid.com/archive/31.2.0/react-data-grid/licensing/)。

要在 Reflex AG Grid 中使用 AG Grid Enterprise 许可证密钥，请设置环境变量 `AG_GRID_LICENSE_KEY`：

```bash
export AG_GRID_LICENSE_KEY="your_license_key"
```

## column_def 属性

以下属性可用于 `column_defs`，还有许多其他属性可以在此处找到：[AG Grid 列定义文档](https://www.ag-grid.com/react-data-grid/column-properties/)。（在 Reflex 中需要使用 snake_case 作为键名，这与 AG Grid 文档中使用 camelCase 不同）

- `field`: `str`：行对象中用于获取单元格数据的字段。
- `col_id`: `str | None`：给列的唯一 ID。这是可选的。如果缺少，ID 将默认为字段名。
- `type`: `str | None`：列的类型。
- `cell_data_type`: `bool | str | None`：此列单元格值的数据类型。可以从行数据推断数据类型（true - 默认行为），定义特定数据类型（字符串），或没有数据类型（false）。
- `hide`: `bool`：设置为 true 则隐藏此列。
- `editable`: `bool | None`：如果此列可编辑则设置为 true，否则为 false。
- `filter`: `AGFilters | str | None`：用于此列的筛选器组件。设置为 true 使用默认筛选器。设置为提供的筛选器名称以使用该筛选器。（查看本页的筛选类型部分了解更多信息）
- `floating_filter`: `bool`：是否为此列显示浮动筛选器。
- `header_name`: `str | None`：在列标题中渲染的名称。如果未指定且指定了字段，则字段名将用作标题名称。
- `header_tooltip`: `str | None`：列标题的工具提示。
- `checkbox_selection`: `bool | None`：设置为 true 以渲染用于行选择的复选框。
- `cell_editor`: `AGEditors | str | None`：为此列的单元格提供自定义单元格编辑器组件。（查看本页的编辑部分了解更多信息）
- `cell_editor_params`: `dict[str, list[Any]] | None`：传递给 cellEditor 组件的参数。



## 你需要的功能在 Reflex 中不可用/无法正常工作

此[文档](https://www.ag-grid.com/react-data-grid/reference/)中找到的所有 AGGrid 选项都已映射到 rxe.ag_grid 中，但由于底层 AG Grid 库中功能数量庞大，某些功能可能尚未经过完全测试。

如果某个 ag_grid 属性没有导入预期的模块，你可以通过 `community_modules` 或 `enterprise_modules` 属性手动传递，这些属性期望一个模块名称的 `set[str]`。如果缺少模块，你会在浏览器控制台中收到警告，因此如果某个功能未按预期工作，你可以在那里检查。

你也可以在我们的 Discord 或主 Reflex 仓库的 GitHub issues 页面上报告缺失的模块。

最佳实践是创建一个 `ag_grid.api()` 的单例，使用与要引用的 `ag_grid` 组件相同的 `id`，在第一个示例中为 `"ag_grid_basic_row_selection"`。

下面的示例使用 AG Grid API 的 `select_all()` 和 `deselect_all()` 方法来选中和取消选中网格中的所有行。此方法在 Reflex 中不直接可用。查看此[文档](https://www.ag-grid.com/react-data-grid/grid-api/#reference-selection-selectAll)了解这些方法在 AG Grid 文档中的样子。

```md alert info
# 确保文档在 AG Grid 中设置为 React 标签页
```

```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd

df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country", "checkboxSelection": True},
    {"field": "pop"},
    {"field": "continent"},
]


def ag_grid_api_simple():
    my_api = rxe.ag_grid.api(id="ag_grid_basic_row_selection")
    return rx.vstack(
        rxe.ag_grid(
            id="ag_grid_basic_row_selection",
            row_data=df.to_dict("records"),
            column_defs=column_defs,
            row_selection="single",
            width="100%",
            height="40vh",
        ),
        rx.button("Select All", on_click=my_api.select_all()),
        rx.button("Deselect All", on_click=my_api.deselect_all()),
        spacing="4",
        width="100%",
    )
```

📊 **数据集来源：** [gapminder2007.csv](https://raw.githubusercontent.com/plotly/datasets/master/gapminder2007.csv)

`select_all()` 事件处理器的 React 代码是 `selectAll = (source?: SelectionEventSourceType) => void;`。

如你所见，要在 Reflex 中使用它，应该用 snake_case 而不是 camelCase 来调用。`void` 意味着它不返回任何内容。`source?` 表示它接受一个可选的 `source` 参数。


```md alert info
# 使用 AG Grid API 的另一种方式
也可以直接在组件的事件触发器（`on_click`）中使用 AG Grid API。这消除了创建变量 `my_api` 的需要。如下面的示例所示。需要使用要引用的 `ag_grid` 组件的 `id`。

```python
rx.button("Select all", on_click=rxe.ag_grid.api(id="ag_grid_basic_row_selection").select_all()),
```
```

### 更多示例

下面的示例让用户[将数据导出为 csv](https://www.ag-grid.com/javascript-data-grid/grid-api/#reference-export-exportDataAsCsv) 并[调整列的大小以适应可用的水平空间](https://www.ag-grid.com/javascript-data-grid/grid-api/#reference-columnSizing-sizeColumnsToFit)。（尝试调整屏幕大小，然后点击调整列大小按钮）


```python demo exec
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd

df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country", "checkboxSelection": True},
    {"field": "pop"},
    {"field": "continent"},
]


def ag_grid_api_simple2():
    my_api = rxe.ag_grid.api(id="ag_grid_export_and_resize")
    return rx.vstack(
        rxe.ag_grid(
            id="ag_grid_export_and_resize",
            row_data=df.to_dict("records"),
            column_defs=column_defs,
            width="100%",
            height="40vh",
        ),
        rx.button("Export", on_click=my_api.export_data_as_csv()),
        rx.button("Resize Columns", on_click=my_api.size_columns_to_fit()),
        spacing="4",
        width="100%",
    )
```

📊 **数据集来源：** [gapminder2007.csv](https://raw.githubusercontent.com/plotly/datasets/master/gapminder2007.csv)

这两个函数的 React 代码如下所示。需要注意的关键点是这两个函数都返回 `void`，因此不返回任何内容。

`exportDataAsCsv = (params?: CsvExportParams) => void;`

`sizeColumnsToFit = (paramsOrGridWidth?: ISizeColumnsToFitParams  |  number) => void;`


### 带返回值的示例

这个示例展示了如何在[后端以 csv 形式获取 `ag_grid` 的数据](https://www.ag-grid.com/javascript-data-grid/grid-api/#reference-export-getDataAsCsv)。传递给后端的数据随后以 toast 的形式显示。

```python
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class AGGridStateAPI(rx.State):
    def handle_get_data(self, data: str):
        yield rx.toast(f"Got CSV data: {data}")


df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country", "checkboxSelection": True},
    {"field": "pop"},
    {"field": "continent"},
]


def ag_grid_api_argument():
    my_api = rxe.ag_grid.api(id="ag_grid_get_data_as_csv")
    return rx.vstack(
        rxe.ag_grid(
            id="ag_grid_get_data_as_csv",
            row_data=df.to_dict("records"),
            column_defs=column_defs,
            width="100%",
            height="40vh",
        ),
        rx.button(
            "Get CSV data on backend",
            on_click=my_api.get_data_as_csv(callback=AGGridStateAPI.handle_get_data),
        ),
        spacing="4",
        width="100%",
    )
```

AG Grid API 的 `get_data_as_csv` 方法的 React 代码是 `getDataAsCsv = (params?: CsvExportParams) => string  |  undefined;`。这里函数返回一个 `string`（或 undefined）。

在 Reflex 中，要处理这个返回值，需要将 `callback` 作为参数传递给 `get_data_as_csv` 方法来获取返回值。在这个示例中，`handle_get_data` 事件处理器作为回调传递。这个事件处理器将使用 `get_data_as_csv` 方法的返回值被调用。
