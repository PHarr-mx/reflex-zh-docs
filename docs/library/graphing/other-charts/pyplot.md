---
components:
  - pyplot
title: Pyplot
meta_description: "Render interactive Matplotlib charts in a Python web app with Reflex. The pyplot component embeds any matplotlib.pyplot figure — line, bar, scatter, or contour — directly in the browser, with light and dark mode support and no Flask or HTML boilerplate."
---

```python exec
import reflex as rx
from reflex_pyplot import pyplot
import numpy as np
import random
import matplotlib.pyplot as plt
from matplotlib.figure import Figure
from reflex.style import toggle_color_mode
```

# Pyplot：在 Python Web 应用中显示 Matplotlib 图表

Pyplot（`reflex-pyplot`）让你可以在 Reflex Web 应用中渲染任何 [Matplotlib](https://matplotlib.org/) 图形。Matplotlib 的 `pyplot` 接口是 Python 中最流行的创建图表的方式，但它本身只能渲染到静态窗口或图像文件。`pyplot` 组件接收一个 `matplotlib.pyplot` 图形并直接在浏览器中显示——无需 Flask 路由、HTML 模板或手动图像编码——让你可以将绘图脚本和笔记本转化为纯 Python 的交互式、有状态的仪表板。

## 什么是 pyplot？

`matplotlib.pyplot` 是一组函数，使 Matplotlib 的行为类似于 MATLAB：每次调用（`plt.plot`、`plt.scatter`、`plt.bar` 等）通过添加线条、柱形、标签或图例来构建图形。在普通脚本中，你会以 `plt.show()` 结束来打开一个窗口。在 Reflex 应用中，你将 `Figure` 对象传递给 `pyplot` 组件，它会将其提供给前端，并在状态变化时重新渲染。

## 安装

使用 pip 安装 `reflex-pyplot` 包。

```bash
pip install reflex-pyplot
```

## 基本示例

要在应用中显示 Matplotlib 图表，你可以使用 `pyplot` 组件。将你用 Matplotlib 创建的图形作为子组件传递给 `pyplot` 组件。

```python demo exec
import matplotlib.pyplot as plt
import reflex as rx
from reflex_pyplot import pyplot
import numpy as np


def create_contour_plot():
    X, Y = np.meshgrid(np.linspace(-3, 3, 256), np.linspace(-3, 3, 256))
    Z = (1 - X / 2 + X**5 + Y**3) * np.exp(-(X**2) - Y**2)
    levels = np.linspace(Z.min(), Z.max(), 7)

    fig, ax = plt.subplots()
    ax.contourf(X, Y, Z, levels=levels)
    plt.close(fig)
    return fig


def pyplot_simple_example():
    return rx.card(
        pyplot(create_contour_plot(), width="100%", height="400px"),
        bg_color="#ffffff",
        width="100%",
    )
```

```md alert info
# 创建图形后必须关闭它

不关闭图形可能会导致内存问题。
```

## 折线图

折线图是最常见的 Matplotlib 图表。使用 `plt.subplots()` 和 `ax.plot()` 像在脚本中一样构建它，然后将图形交给 `pyplot` 在浏览器中显示。

```python demo exec
import matplotlib.pyplot as plt
import reflex as rx
from reflex_pyplot import pyplot
import numpy as np


def create_line_plot():
    x = np.linspace(0, 10, 100)
    fig, ax = plt.subplots()
    ax.plot(x, np.sin(x), label="sin(x)")
    ax.plot(x, np.cos(x), label="cos(x)")
    ax.set_xlabel("x")
    ax.set_ylabel("y")
    ax.legend()
    plt.close(fig)
    return fig


def pyplot_line_example():
    return rx.card(
        pyplot(create_line_plot(), width="100%", height="400px"),
        bg_color="#ffffff",
        width="100%",
    )
```

## 柱状图

相同的模式也适用于 Matplotlib 柱状图——使用类别和值调用 `ax.bar()`，然后用 `pyplot` 渲染图形。

```python demo exec
import matplotlib.pyplot as plt
import reflex as rx
from reflex_pyplot import pyplot


def create_bar_chart():
    fruits = ["apples", "oranges", "bananas", "pears"]
    counts = [23, 17, 35, 29]
    fig, ax = plt.subplots()
    ax.bar(fruits, counts, color="#4e79a7")
    ax.set_ylabel("count")
    ax.set_title("Fruit inventory")
    plt.close(fig)
    return fig


def pyplot_bar_example():
    return rx.card(
        pyplot(create_bar_chart(), width="100%", height="400px"),
        bg_color="#ffffff",
        width="100%",
    )
```

## 带状态的示例

让我们创建一个随机数据的散点图。我们还将允许用户随机化数据并更改点的数量。

在这个示例中，我们使用 `color_mode_cond` 在浅色和深色模式下显示图表。我们需要在这里手动执行此操作，因为颜色由 matplotlib 图表决定，而不是由主题决定。

```python demo exec
import random
from typing import Literal
import matplotlib.pyplot as plt
import reflex as rx
from reflex_pyplot import pyplot
import numpy as np


def create_plot(theme: str, plot_data: tuple, scale: list):
    bg_color, text_color = (
        ("#1e1e1e", "white") if theme == "dark" else ("white", "black")
    )
    grid_color = "#555555" if theme == "dark" else "#cccccc"

    fig, ax = plt.subplots(facecolor=bg_color)
    ax.set_facecolor(bg_color)

    for (x, y), color in zip(plot_data, ["#4e79a7", "#f28e2b"]):
        ax.scatter(x, y, c=color, s=scale, label=color, alpha=0.6, edgecolors="none")

    ax.legend(
        loc="upper right", facecolor=bg_color, edgecolor="none", labelcolor=text_color
    )
    ax.grid(True, color=grid_color)
    ax.tick_params(colors=text_color)
    for spine in ax.spines.values():
        spine.set_edgecolor(text_color)

    for item in [ax.xaxis.label, ax.yaxis.label, ax.title]:
        item.set_color(text_color)
    plt.close(fig)

    return fig


class PyplotState(rx.State):
    num_points: int = 25
    plot_data: tuple = tuple(np.random.rand(2, 25) for _ in range(2))
    scale: list = [random.uniform(0, 100) for _ in range(25)]

    @rx.event(temporal=True, throttle=500)
    def randomize(self):
        self.plot_data = tuple(np.random.rand(2, self.num_points) for _ in range(2))
        self.scale = [random.uniform(0, 100) for _ in range(self.num_points)]

    @rx.event(temporal=True, throttle=500)
    def set_num_points(self, num_points: list[int | float]):
        self.num_points = int(num_points[0])
        yield PyplotState.randomize()

    @rx.var
    def fig_light(self) -> Figure:
        fig = create_plot("light", self.plot_data, self.scale)
        return fig

    @rx.var
    def fig_dark(self) -> Figure:
        fig = create_plot("dark", self.plot_data, self.scale)
        return fig


def pyplot_example():
    return rx.vstack(
        rx.card(
            rx.color_mode_cond(
                pyplot(PyplotState.fig_light, width="100%", height="100%"),
                pyplot(PyplotState.fig_dark, width="100%", height="100%"),
            ),
            rx.vstack(
                rx.hstack(
                    rx.button(
                        "Randomize",
                        on_click=PyplotState.randomize,
                    ),
                    rx.text("Number of Points:"),
                    rx.slider(
                        default_value=25,
                        min_=10,
                        max=100,
                        on_value_commit=PyplotState.set_num_points,
                    ),
                    width="100%",
                ),
                width="100%",
            ),
            width="100%",
        ),
        justify_content="center",
        align_items="center",
        height="100%",
        width="100%",
    )
```

## 常见问题

### 如何在网站中显示 Matplotlib 图表？

像往常一样使用 `matplotlib.pyplot` 创建图形，然后将 `Figure` 对象传递给 Reflex 的 `pyplot` 组件。Reflex 会为你在浏览器中渲染它——你不需要 Flask、REST 端点或手动 base64 图像编码。

### 我可以在 Reflex 中使 Matplotlib 具有交互性吗？

可以。在依赖于状态的 `rx.var` 中计算图形，然后从按钮、滑块或其他事件更新状态。图表会自动重新渲染，如上面的带状态示例所示。

### 我需要调用 `plt.show()` 吗？

不需要。`plt.show()` 会打开一个桌面窗口，在 Web 应用中不使用。相反，将图形返回给 `pyplot` 组件，并在创建后调用 `plt.close(fig)` 来释放内存。
