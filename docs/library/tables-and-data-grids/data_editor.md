---
components:
  - rx.data_editor
meta_description: "Build an editable data grid in Python with Reflex. The rx.data_editor component is a fast, spreadsheet-like data grid based on Glide Data Grid for editing tabular data — all in pure Python."
---

# Data Editor（数据编辑器）

Reflex 数据编辑器（`rx.data_editor`）是一个基于 [Glide Data Grid](https://grid.glideapps.com/) 的快速、可编辑数据网格。用它可以在纯 Python 中进行类似电子表格的表格数据编辑——跨多行多列添加、编辑和格式化单元格。

```python exec
import reflex as rx
from typing import Any

columns: list[dict[str, str]] = [
    {
        "title": "Code",
        "type": "str",
    },
    {
        "title": "Value",
        "type": "int",
    },
    {
        "title": "Activated",
        "type": "bool",
    },
]
data: list[list[Any]] = [
    ["A", 1, True],
    ["B", 2, False],
    ["C", 3, False],
    ["D", 4, True],
    ["E", 5, True],
    ["F", 6, False],
]
```

此组件作为 [datatable](/docs/library/tables-and-data-grids/data-table) 的替代方案引入，以支持编辑显示的数据。

## 列定义

列定义应该是一个 `dict` 的 `list`，每个 `dict` 描述对应的列。
列字典的属性：

- `title`：列标题中显示的文本。
- `id`：列的 id，如果未定义，默认为 `title` 的小写形式。
- `width`：列的宽度。
- `type`：列的类型，默认为 `"str"`。

## 数据

`rx.data_editor` 的 `data` prop 接受一个 `list` 的 `list`，其中每个 `list` 代表表格中要显示的一行数据。

## 简单示例

下面是使用 data_editor 表示数据的基本示例，没有交互也没有样式。下面我们定义了 `columns` 和 `data`，它们由 `rx.data_editor` 组件接收。定义 `columns` 时，必须为创建的每一列定义 `title` 和 `type`。`data` 中的列必须与定义的 `type` 匹配，否则会抛出错误。

```python demo box
rx.data_editor(
    columns=columns,
    data=data,
)
```

```python
columns: list[dict[str, str]] = [
    {
        "title": "Code",
        "type": "str",
    },
    {
        "title": "Value",
        "type": "int",
    },
    {
        "title": "Activated",
        "type": "bool",
    },
]
data: list[list[Any]] = [
    ["A", 1, True],
    ["B", 2, False],
    ["C", 3, False],
    ["D", 4, True],
    ["E", 5, True],
    ["F", 6, False],
]
```

```python
rx.data_editor(
    columns=columns,
    data=data,
)
```

## 交互示例

```python exec
class DataEditorState_HP(rx.State):
    clicked_data: str = "Cell clicked: "
    cols: list[Any] = [
        {"title": "Title", "type": "str"},
        {
            "title": "Name",
            "type": "str",
            "group": "Data",
            "width": 300,
        },
        {
            "title": "Birth",
            "type": "str",
            "id": "date",
            "group": "Data",
            "width": 150,
        },
        {
            "title": "Human",
            "type": "bool",
            "group": "Data",
            "width": 80,
        },
        {
            "title": "House",
            "type": "str",
            "id": "date",
            "group": "Data",
        },
        {
            "title": "Wand",
            "type": "str",
            "id": "date",
            "group": "Data",
            "width": 250,
        },
        {
            "title": "Patronus",
            "type": "str",
            "id": "date",
            "group": "Data",
        },
        {
            "title": "Blood status",
            "type": "str",
            "id": "date",
            "group": "Data",
            "width": 200,
        },
    ]

    data = [
        [
            "1",
            "Harry James Potter",
            "31 July 1980",
            True,
            "Gryffindor",
            "11'  Holly  phoenix feather",
            "Stag",
            "Half-blood",
        ],
        [
            "2",
            "Ronald Bilius Weasley",
            "1 March 1980",
            True,
            "Gryffindor",
            "12' Ash unicorn tail hair",
            "Jack Russell terrier",
            "Pure-blood",
        ],
        [
            "3",
            "Hermione Jean Granger",
            "19 September, 1979",
            True,
            "Gryffindor",
            "10¾'  vine wood dragon heartstring",
            "Otter",
            "Muggle-born",
        ],
        [
            "4",
            "Albus Percival Wulfric Brian Dumbledore",
            "Late August 1881",
            True,
            "Gryffindor",
            "15' Elder Thestral tail hair core",
            "Phoenix",
            "Half-blood",
        ],
        [
            "5",
            "Rubeus Hagrid",
            "6 December 1928",
            False,
            "Gryffindor",
            "16'  Oak unknown core",
            "None",
            "Part-Human (Half-giant)",
        ],
        [
            "6",
            "Fred Weasley",
            "1 April, 1978",
            True,
            "Gryffindor",
            "Unknown",
            "Unknown",
            "Pure-blood",
        ],
    ]

    def click_cell(self, pos):
        col, row = pos
        yield self.get_clicked_data(pos)

    def get_clicked_data(self, pos) -> str:
        self.clicked_data = f"Cell clicked: {pos}"
```

这里我们定义了一个 State，如下所示，它允许我们在点击单元格时使用 `on_cell_clicked` 事件触发器将单元格位置打印为标题。查看本页底部可与 datatable 一起使用的所有其他事件触发器。我们还定义了一个标签为 `Data` 的 `group`。这会将所有带有此 `group` 标签的列归入一个更大的组 `Data` 下，如下表所示。

```python demo box
rx.heading(DataEditorState_HP.clicked_data, as_="h2")
```

```python demo box
rx.data_editor(
    columns=DataEditorState_HP.cols,
    data=DataEditorState_HP.data,
    on_cell_clicked=DataEditorState_HP.click_cell,
)
```

```python
class DataEditorState_HP(rx.State):
    clicked_data: str = "Cell clicked: "

    cols: list[Any] = [
        {"title": "Title", "type": "str"},
        {
            "title": "Name",
            "type": "str",
            "group": "Data",
            "width": 300,
        },
        {
            "title": "Birth",
            "type": "str",
            "group": "Data",
            "width": 150,
        },
        {
            "title": "Human",
            "type": "bool",
            "group": "Data",
            "width": 80,
        },
        {
            "title": "House",
            "type": "str",
            "group": "Data",
        },
        {
            "title": "Wand",
            "type": "str",
            "group": "Data",
            "width": 250,
        },
        {
            "title": "Patronus",
            "type": "str",
            "group": "Data",
        },
        {
            "title": "Blood status",
            "type": "str",
            "group": "Data",
            "width": 200,
        },
    ]

    data = [
        [
            "1",
            "Harry James Potter",
            "31 July 1980",
            True,
            "Gryffindor",
            "11'  Holly  phoenix feather",
            "Stag",
            "Half-blood",
        ],
        [
            "2",
            "Ronald Bilius Weasley",
            "1 March 1980",
            True,
            "Gryffindor",
            "12' Ash unicorn tail hair",
            "Jack Russell terrier",
            "Pure-blood",
        ],
        [
            "3",
            "Hermione Jean Granger",
            "19 September, 1979",
            True,
            "Gryffindor",
            "10¾'  vine wood dragon heartstring",
            "Otter",
            "Muggle-born",
        ],
        [
            "4",
            "Albus Percival Wulfric Brian Dumbledore",
            "Late August 1881",
            True,
            "Gryffindor",
            "15' Elder Thestral tail hair core",
            "Phoenix",
            "Half-blood",
        ],
        [
            "5",
            "Rubeus Hagrid",
            "6 December 1928",
            False,
            "Gryffindor",
            "16'  Oak unknown core",
            "None",
            "Part-Human (Half-giant)",
        ],
        [
            "6",
            "Fred Weasley",
            "1 April, 1978",
            True,
            "Gryffindor",
            "Unknown",
            "Unknown",
            "Pure-blood",
        ],
    ]

    def click_cell(self, pos):
        col, row = pos
        yield self.get_clicked_data(pos)

    def get_clicked_data(self, pos) -> str:
        self.clicked_data = f"Cell clicked: {pos}"
```

```python
rx.data_editor(
    columns=DataEditorState_HP.cols,
    data=DataEditorState_HP.data,
    on_cell_clicked=DataEditorState_HP.click_cell,
)
```

## 样式示例

现在让我们为数据表格添加样式，使其更美观、更易用。我们必须先导入 `DataEditorTheme`，然后就可以开始设置样式 prop，如下面的 `dark_theme` 所示。

然后我们使用 `theme=DataEditorTheme(**dark_theme)` 设置这些主题。除了样式之外，我们还可以设置一些 `props` 来对数据表格进行其他美观调整。我们将 `row_height` 设置为 `50`，使内容更易阅读。我们还将 `smooth_scroll_x` 和 `smooth_scroll_y` 设置为 `True`，以便沿列和行平滑滚动。最后，我们添加了 `column_select=single`，其中 column select 可以取以下值：`none`、`single` 或 `multiple`。

```python exec
from reflex.components.datadisplay.dataeditor import DataEditorTheme

dark_theme = {
    "accentColor": "#8c96ff",
    "accentLight": "rgba(202, 206, 255, 0.253)",
    "textDark": "#ffffff",
    "textMedium": "#b8b8b8",
    "textLight": "#a0a0a0",
    "textBubble": "#ffffff",
    "bgIconHeader": "#b8b8b8",
    "fgIconHeader": "#000000",
    "textHeader": "#a1a1a1",
    "textHeaderSelected": "#000000",
    "bgCell": "#16161b",
    "bgCellMedium": "#202027",
    "bgHeader": "#212121",
    "bgHeaderHasFocus": "#474747",
    "bgHeaderHovered": "#404040",
    "bgBubble": "#212121",
    "bgBubbleSelected": "#000000",
    "bgSearchResult": "#423c24",
    "borderColor": "rgba(225,225,225,0.2)",
    "drilldownBorder": "rgba(225,225,225,0.4)",
    "linkColor": "#4F5DFF",
    "headerFontStyle": "bold 14px",
    "baseFontStyle": "13px",
    "fontFamily": "Inter, Roboto, -apple-system, BlinkMacSystemFont, avenir next, avenir, segoe ui, helvetica neue, helvetica, Ubuntu, noto, arial, sans-serif",
}
```

```python demo box
rx.data_editor(
    columns=DataEditorState_HP.cols,
    data=DataEditorState_HP.data,
    row_height=80,
    smooth_scroll_x=True,
    smooth_scroll_y=True,
    column_select="single",
    theme=DataEditorTheme(**dark_theme),
    height="30vh",
)
```

```python
from reflex.components.datadisplay.dataeditor import DataEditorTheme

dark_theme_snake_case = {
    "accent_color": "#8c96ff",
    "accent_light": "rgba(202, 206, 255, 0.253)",
    "text_dark": "#ffffff",
    "text_medium": "#b8b8b8",
    "text_light": "#a0a0a0",
    "text_bubble": "#ffffff",
    "bg_icon_header": "#b8b8b8",
    "fg_icon_header": "#000000",
    "text_header": "#a1a1a1",
    "text_header_selected": "#000000",
    "bg_cell": "#16161b",
    "bg_cell_medium": "#202027",
    "bg_header": "#212121",
    "bg_header_has_focus": "#474747",
    "bg_header_hovered": "#404040",
    "bg_bubble": "#212121",
    "bg_bubble_selected": "#000000",
    "bg_search_result": "#423c24",
    "border_color": "rgba(225,225,225,0.2)",
    "drilldown_border": "rgba(225,225,225,0.4)",
    "link_color": "#4F5DFF",
    "header_font_style": "bold 14px",
    "base_font_style": "13px",
    "font_family": "Inter, Roboto, -apple-system, BlinkMacSystemFont, avenir next, avenir, segoe ui, helvetica neue, helvetica, Ubuntu, noto, arial, sans-serif",
}
```

```python
rx.data_editor(
    columns=DataEditorState_HP.cols,
    data=DataEditorState_HP.data,
    row_height=80,
    smooth_scroll_x=True,
    smooth_scroll_y=True,
    column_select="single",
    theme=DataEditorTheme(**dark_theme),
    height="30vh",
)
```

## 相关内容

探索在 Reflex 中处理表格数据的其他方法，全部使用纯 Python：

- [Data Table](/docs/library/tables-and-data-grids/data-table)
- [Table](/docs/library/tables-and-data-grids/table)
- [表格和数据网格](/docs/library/tables-and-data-grids/)
