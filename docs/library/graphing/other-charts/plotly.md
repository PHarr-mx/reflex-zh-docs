---
components:
  - rx.plotly
title: Plotly
meta_description: "Use Plotly in Python with Reflex. The rx.plotly component renders interactive Plotly and Plotly Express figures — line charts, scatter plots, heatmaps, and histograms — in your web app, all in pure Python."
---

# 在 Python 中使用 Plotly：用 Reflex 创建交互式图表

```python exec
import reflex as rx
import pandas as pd
import plotly.express as px
import plotly.graph_objects as go
```

[Plotly](https://plotly.com/graphing-libraries/) 是一个流行的 Python 图表库，用于创建交互式、出版级质量的图表。Reflex 通过 `rx.plotly` 组件对其进行封装，让你可以将任何 Plotly 或 Plotly Express 图形——折线图、散点图、直方图、热力图或 3D 曲面图——直接嵌入到 Python Web 应用中，无需 JavaScript。由于 Reflex 编译为全栈 Web 应用，这些图表在浏览器中保持交互性，并且可以从应用状态实时更新。

```md alert info
# 在将 Plotly 图形集成到 UI 代码中时，请注意显示图形的方法与普通 Python 脚本不同。不要使用 `fig.show()`，而是在 UI 代码中使用 `rx.plotly(data=fig)` 以确保图形在用户界面中正确渲染和显示。
```

## 基本示例

让我们创建一个加拿大预期寿命的折线图。

```python demo exec
import plotly.express as px

df = px.data.gapminder().query("country=='Canada'")
fig = px.line(df, x="year", y="lifeExp", title="Life expectancy in Canada")


def line_chart():
    return rx.center(
        rx.plotly(data=fig),
    )
```

## Plotly Express 图表类型

[Plotly Express](https://plotly.com/python/plotly-express/)（`plotly.express`，导入为 `px`）可以用一行 Python 代码构建常见的图表类型，每个图形都可以通过 `rx.plotly` 在 Reflex 中渲染。

### 柱状图（Bar Chart）

使用 `px.bar` 创建 Plotly Express 柱状图：

```python demo exec
oceania = px.data.gapminder().query("continent == 'Oceania'")
bar_fig = px.bar(
    oceania, x="year", y="pop", color="country", title="Population of Oceania"
)


def plotly_bar_chart():
    return rx.center(rx.plotly(data=bar_fig))
```

### 散点图（Scatter Plot）

使用 `px.scatter` 创建 Plotly 散点图：

```python demo exec
iris = px.data.iris()
scatter_fig = px.scatter(
    iris,
    x="sepal_width",
    y="sepal_length",
    color="species",
    title="Iris sepal dimensions",
)


def plotly_scatter_plot():
    return rx.center(rx.plotly(data=scatter_fig))
```

### 饼图（Pie Chart）

使用 `px.pie` 创建 Plotly 饼图：

```python demo exec
tips = px.data.tips()
pie_fig = px.pie(tips, values="tip", names="day", title="Tips by day")


def plotly_pie_chart():
    return rx.center(rx.plotly(data=pie_fig))
```

### 热力图（Heatmap）

使用 `px.density_heatmap` 创建 Plotly 热力图：

```python demo exec
tips_data = px.data.tips()
heatmap_fig = px.density_heatmap(
    tips_data, x="total_bill", y="tip", title="Bill vs tip density heatmap"
)


def plotly_heatmap():
    return rx.center(rx.plotly(data=heatmap_fig))
```

### 直方图（Histogram）

使用 `px.histogram` 创建 Plotly 直方图：

```python demo exec
hist_data = px.data.tips()
histogram_fig = px.histogram(
    hist_data, x="total_bill", nbins=20, title="Distribution of total bills"
)


def plotly_histogram():
    return rx.center(rx.plotly(data=histogram_fig))
```

### 箱线图（Box Plot）

使用 `px.box` 创建 Plotly 箱线图：

```python demo exec
box_data = px.data.tips()
box_fig = px.box(box_data, x="day", y="total_bill", title="Total bill by day")


def plotly_box_plot():
    return rx.center(rx.plotly(data=box_fig))
```

## 区域设置

使用 `locale` 来本地化 Plotly 的数字/日期格式和模式栏标签：

```python demo exec
df = px.data.gapminder().query("country=='Canada'")
fig = px.line(df, x="year", y="lifeExp", title="Life expectancy in Canada")


def localized_line_chart():
    return rx.center(
        rx.plotly(
            data=fig,
            locale="de",
        ),
    )
```

你仍然可以传递 `config`；当两者都提供时，`locale=` 将作为最终的 locale 值应用。

## 3D 图表示例

让我们创建一个 Mount Bruno 的 3D 曲面图。这是一个稍微复杂一些的示例，但它使用相同的方法封装在 Reflex 中。实际上，你可以使用相同的方法封装任何图形。

```python demo exec
import plotly.graph_objects as go
import pandas as pd

# 从 csv 读取数据
z_data = pd.read_csv("data/mt_bruno_elevation.csv")

fig = go.Figure(data=[go.Surface(z=z_data.values)])
fig.update_traces(
    contours_z=dict(
        show=True, usecolormap=True, highlightcolor="limegreen", project_z=True
    )
)
fig.update_layout(
    scene_camera_eye=dict(x=1.87, y=0.88, z=-0.64), margin=dict(l=65, r=50, b=65, t=90)
)


def mountain_surface():
    return rx.center(
        rx.plotly(data=fig),
    )
```

📊 **数据集来源：** [mt_bruno_elevation.csv](https://raw.githubusercontent.com/plotly/datasets/master/api_docs/mt_bruno_elevation.csv)

## 将图形作为 State 变量

如果将图形设置为 state 变量，则可以在运行时更新它。

```python demo exec
import plotly.express as px
import plotly.graph_objects as go
import pandas as pd


class PlotlyState(rx.State):
    df: pd.DataFrame
    figure: go.Figure = px.line()

    @rx.event
    def create_figure(self):
        self.df = px.data.gapminder().query("country=='Canada'")
        self.figure = px.line(
            self.df,
            x="year",
            y="lifeExp",
            title="Life expectancy in Canada",
        )

    @rx.event
    def set_selected_country(self, country):
        self.df = px.data.gapminder().query(f"country=='{country}'")
        self.figure = px.line(
            self.df,
            x="year",
            y="lifeExp",
            title=f"Life expectancy in {country}",
        )


def line_chart_with_state():
    return rx.vstack(
        rx.select(
            ["China", "France", "United Kingdom", "United States", "Canada"],
            default_value="Canada",
            on_change=PlotlyState.set_selected_country,
        ),
        rx.plotly(
            data=PlotlyState.figure,
            on_mount=PlotlyState.create_figure,
        ),
    )
```

## 添加样式和布局

使用 `update_layout()` 方法来更新图表的布局。查看 [Plotly Layouts](https://plotly.com/python/reference/layout/) 了解所有布局属性。

```md alert info
注意，不建议设置 width 和 height 属性，以确保图表对其容器保持尺寸响应。图表的大小将由其外部容器决定。
```

```python demo exec
df = px.data.gapminder().query("country=='Canada'")
fig_1 = px.line(
    df,
    x="year",
    y="lifeExp",
    title="Life expectancy in Canada",
)
fig_1.update_layout(
    title_x=0.5,
    plot_bgcolor="#c3d7f7",
    paper_bgcolor="rgba(128, 128, 128, 0.1)",
    showlegend=True,
    title_font_family="Open Sans",
    title_font_size=25,
)


def add_styles():
    return rx.center(
        rx.plotly(data=fig_1),
        width="100%",
        height="100%",
    )
```
