```python exec
import reflex as rx
```

# UI 概述

组件是应用用户界面（UI）的构建块。它们是构成应用的视觉元素，如按钮、文本和图片。

## 组件基础

组件由子节点（children）和属性（props）组成。

```md definition
# 子节点（Children）

- 组件内部嵌套的文本或其他 Reflex 组件。
- 作为**位置参数**传递。

# 属性（Props）

- 影响组件行为和外观的属性。
- 作为**关键字参数**传递。
```

让我们来看一下 `rx.text` 组件。

```python demo
rx.text("Hello World!", color="blue", font_size="1.5em")
```

这里的 `"Hello World!"` 是要显示的子文本，而 `color` 和 `font_size` 是修改文本外观的属性（props）。

```md alert success
# 常规 Python 数据类型可以作为子节点传递给组件。这对于传递文本、数字和其他简单数据类型非常有用。
```

## 另一个例子

现在让我们来看一个更复杂的组件，它内部嵌套了其他组件。`rx.vstack` 组件是一个容器，将其子节点在垂直方向上排列，并在它们之间添加间距。

```python demo
rx.vstack(
    rx.heading("Sample Form", as_="h2"),
    rx.input(placeholder="Name"),
    rx.checkbox("Subscribe to Newsletter"),
)
```

有些属性是组件特有的。例如，`rx.accordion.item` 组件的 `header` 和 `content` 属性分别显示手风琴的标题和内容详情。

像 `color` 这样的样式属性在许多组件之间共享。

```md alert info
# 你可以在[组件库](/docs/library)中查看组件的文档页面，找到该组件的所有属性。
```

## 页面

Reflex 应用由多个页面组织而成，每个页面对应一个不同的 URL。

页面被定义为返回一个组件的函数。默认情况下，函数名将被用作路径，但你也可以明确指定路由。

```python
def index():
    return rx.text("Root Page")


def about():
    return rx.text("About Page")


app = rx.App()
app.add_page(index, route="/")
app.add_page(about, route="/about")
```

在这个例子中，我们在根路由添加了一个名为 `index` 的页面。
如果你运行 `reflex run` 应用，你将在 `http://localhost:3000` 看到 `index` 页面。
类似地，`about` 页面将在 `http://localhost:3000/about` 访问。
