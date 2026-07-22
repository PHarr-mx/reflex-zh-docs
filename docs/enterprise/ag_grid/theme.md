---
meta_description: "Theme and style AG Grid in Python with Reflex. Customize your data grid with built-in AG Grid themes and custom styling — all in pure Python, no JavaScript required."
order: 3
---

```python exec
import reflex as rx
import reflex_enterprise as rxe
```

# 主题（Themes）

```md alert warning
# 目前仅支持 AG Grid 的旧主题 API。新的主题 API 尚不支持。
```

你可以使用主题来设置网格的样式。AG Grid 包含以下主题：

1. `quartz`
2. `alpine`
3. `balham`
4. `material`

网格默认使用 `quartz`。要使用其他主题，使用 `theme` 属性设置，例如 `theme="alpine"`。

```python
import reflex as rx
import reflex_enterprise as rxe
import pandas as pd


class AGGridThemeState(rx.State):
    """The app state."""

    theme: str = "quartz"
    themes: list[str] = ["quartz", "balham", "alpine", "material"]

    @rx.event
    def set_theme(self, value: str):
        self.theme = value


df = pd.read_csv("data/gapminder2007.csv")

column_defs = [
    {"field": "country"},
    {"field": "pop", "headerName": "Population"},
    {"field": "lifeExp", "headerName": "Life Expectancy"},
]


def ag_grid_simple_themes():
    return rx.vstack(
        rx.hstack(
            rx.text("Theme:"),
            rx.select(
                AGGridThemeState.themes,
                value=AGGridThemeState.theme,
                on_change=AGGridThemeState.set_theme,
            ),
        ),
        rxe.ag_grid(
            id="ag_grid_basic_themes",
            row_data=df.to_dict("records"),
            column_defs=column_defs,
            theme=AGGridThemeState.theme,
            width="100%",
            height="40vh",
        ),
        width="100%",
    )
```

📊 **数据集来源：** [gapminder2007.csv](https://raw.githubusercontent.com/plotly/datasets/master/gapminder2007.csv)

## 使用 CSS 自定义主题

`theme` 属性直接映射到 AG Grid 的内置主题类之一，`reflex-enterprise` 将其应用于网格根元素：

- `theme="quartz"` → `.ag-theme-quartz`
- `theme="balham"` → `.ag-theme-balham`
- `theme="alpine"` → `.ag-theme-alpine`
- `theme="material"` → `.ag-theme-material`

每个主题还有一个暗色变体（`.ag-theme-quartz-dark`、`.ag-theme-balham-dark` 等）。暗色变体仅在选择相应主题名称时应用；在 CSS 中同时列出亮色和暗色类选择器可以让相同的覆盖在任一模式下生效。

你可以通过覆盖 AG Grid CSS 变量（`--ag-*` 自定义属性）并编写针对 AG Grid 内置类的规则来自定义任何主题。可用变量的完整列表记录在 [AG Grid 主题参考](https://www.ag-grid.com/javascript-data-grid/global-style-customisation-variables/)中。

### CSS 变量

AG Grid 将大多数视觉属性公开为 CSS 变量。常用的自定义项：

- 排版：`--ag-font-family`、`--ag-font-size`
- 密度和形状：`--ag-grid-size`（基本单位）、`--ag-border-radius`、`--ag-wrapper-border-radius`、`--ag-cell-horizontal-padding`
- 边框：`--ag-border-color`、`--ag-row-border-color`
- 背景：`--ag-background-color`、`--ag-odd-row-background-color`、`--ag-row-hover-color`
- 表头：`--ag-header-background-color`、`--ag-header-foreground-color`、`--ag-header-column-separator-color`
- 强调色（选择/聚焦）：`--ag-accent-color`、`--ag-selected-row-background-color`、`--ag-range-selection-background-color`、`--ag-input-focus-border-color`、`--ag-checkbox-checked-color`

### 使用包装类限定覆盖范围

如果页面上有多个网格，将覆盖限定到父类，以免泄漏到其他网格。将网格包装在一个带有类名（如 `custom-ag-grid`）的 `rx.box`（或任何元素）中，然后在 CSS 中针对该包装器_内部_的主题类：

```python
rx.box(
    rxe.ag_grid(
        id="my_themed_grid",
        row_data=df.to_dict("records"),
        column_defs=column_defs,
        theme="quartz",
        width="100%",
        height="40vh",
    ),
    class_name="custom-ag-grid",
)
```

```css
/* assets/ag_grid_theme.css */

/*
 * 自定义 ag-grid 主题覆盖。
 *
 * reflex-enterprise 将内置主题作为类应用于网格根元素。
 * 将覆盖限定到父级 `.custom-ag-grid` 容器，以免泄漏到页面上的其他网格。
 */

.custom-ag-grid .ag-theme-quartz,
.custom-ag-grid .ag-theme-quartz-dark,
.custom-ag-grid .ag-theme-balham,
.custom-ag-grid .ag-theme-balham-dark,
.custom-ag-grid .ag-theme-alpine,
.custom-ag-grid .ag-theme-alpine-dark,
.custom-ag-grid .ag-theme-material,
.custom-ag-grid .ag-theme-material-dark {
    --ag-font-family: "Inter", ui-sans-serif, system-ui, sans-serif;
    --ag-font-size: 13px;

    --ag-grid-size: 6px;
    --ag-border-radius: 12px;
    --ag-wrapper-border-radius: 12px;

    --ag-background-color: #ffffff;
    --ag-odd-row-background-color: #fafbfd;
    --ag-row-hover-color: #f1f5f9;

    --ag-header-background-color: #eef2ff;
    --ag-header-foreground-color: #1e293b;

    --ag-accent-color: #3b82f6;
    --ag-selected-row-background-color: #dbeafe;
    --ag-input-focus-border-color: #3b82f6;
    --ag-checkbox-checked-color: #3b82f6;

    --ag-cell-horizontal-padding: 16px;
}

/* 更强的表头排版。 */
.custom-ag-grid .ag-header-cell-text {
    font-weight: 600;
    letter-spacing: 0.04em;
    text-transform: uppercase;
    font-size: 11px;
}
```

在选择器中列出所有主题变体可以确保无论运行时选择哪个内置主题（包括其暗色变体），覆盖都会生效。

### 加载样式表

将你的 CSS 文件放在 `assets/` 下，并将其添加到 `rxe.App`（或 `rx.App`）的 `stylesheets` 列表中：

```python
app = rxe.App(
    stylesheets=["/ag_grid_theme.css"],
)
```

路径相对于 `assets/`，因此 `assets/ag_grid_theme.css` 引用为 `/ag_grid_theme.css`。

### 通过 `style` 内联覆盖 CSS 变量

对于一次性调整，你不需要单独的样式表——你可以直接在网格的 `style` 字典中设置 `--ag-*` 变量：

```python
rxe.ag_grid(
    id="comic_grid",
    row_data=df.to_dict("records"),
    column_defs=column_defs,
    theme="quartz",
    style={
        "--ag-font-family": "Comic Sans MS !important",
        "--ag-font-size": "14px !important",
    },
    width="100%",
    height="40vh",
)
```

```md alert warning
# CSS 变量继承和 `!important`
内联 `style` 仅在网格根元素上设置变量。内置主题将其样式绑定到多个 CSS 类（`.ag-theme-quartz`、`.ag-theme-quartz .ag-cell` 等），这些规则通常比内联定义的自定义属性具有更高的优先级。因此，通过 `style={"--ag-font-family": "..."}` 设置的值可能会被忽略，除非你附加 `!important`。如果你的内联覆盖没有生效，添加 `!important` 或将覆盖移到以相当优先级针对主题类的样式表中。

内联 `style` 覆盖仅应用于设置它们的网格。要一致地自定义多个网格，优先使用限定到包装类的样式表（见上文）。
```

## 每单元格自定义类

内置主题将网格作为整体进行样式设置。要根据单元格的值高亮显示单个单元格，通过 `cellClass` 和 `cellClassRules` 列定义键附加自定义 CSS 类，然后在样式表中设置这些类的样式。

- `cellClass` 始终将列出的类应用于该列中的每个单元格。
- `cellClassRules` 是一个 `类名 -> JS 表达式` 的映射；当表达式求值为真时应用该类。单元格的值可通过 `params.value` 获取。

```python
column_defs = [
    {"field": "project"},
    {
        "field": "status",
        "cellClass": "status-pill",
        "cellClassRules": {
            "status-active": "params.value === 'Active'",
            "status-blocked": "params.value === 'Blocked'",
            "status-complete": "params.value === 'Complete'",
            "status-planning": "params.value === 'Planning'",
        },
    },
]
```

```css
/* assets/ag_grid_theme.css */

.custom-ag-grid .status-pill {
    display: inline-flex;
    align-items: center;
    justify-content: center;
}

.custom-ag-grid .status-active   { color: #047857; }
.custom-ag-grid .status-blocked  { color: #b91c1c; }
.custom-ag-grid .status-complete { color: #1d4ed8; }
.custom-ag-grid .status-planning { color: #a16207; }
```

相同的方法也适用于表头单元格（`headerClass`）和行（网格本身的 `rowClass` / `rowClassRules`）。
