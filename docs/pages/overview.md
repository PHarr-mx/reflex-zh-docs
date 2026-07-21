```python exec
import reflex as rx
```

# 页面

页面将组件映射到应用中的不同 URL。本节涵盖创建页面、处理 URL 参数、访问查询参数、管理页面元数据以及处理页面加载事件。

## 添加页面

你可以通过定义一个返回组件的函数来创建页面。
默认情况下，函数名将用作路由，但你也可以指定路由。

```python
def index():
    return rx.text("Root Page")


def about():
    return rx.text("About Page")


def custom():
    return rx.text("Custom Route")


app = rx.App()

app.add_page(index)
app.add_page(about)
app.add_page(custom, route="/custom-route")
```

在这个示例中，我们创建了三个页面：

- `index` —— 根路由，可在 `/` 访问
- `about` —— 可在 `/about` 访问
- `custom` —— 可在 `/custom-route` 访问

```md alert
# Index 是一个特殊例外，它在 `/` 和 `/index` 均可访问。所有其他页面仅在其指定的路由处可访问。
```

```md video https://youtube.com/embed/ITOZkzjtjUA?start=3853&end=4083
# Video: Pages and URL Routes
```

## 页面装饰器

你也可以使用 `@rx.page` 装饰器来添加页面。

```python
@rx.page(route="/", title="My Beautiful App")
def index():
    return rx.text("A Beautiful App")
```

这等同于使用相同参数调用 `app.add_page`。

```md alert warning
# 记得导入定义装饰页面的模块。

这对于页面在应用中注册是必要的。

你可以直接导入该模块，或导入另一个导入了装饰页面的模块。
```

## 在页面之间导航

### 链接

[链接（Links）](/docs/library/typography/link)是主要用于导航的可访问元素。使用 `href` 属性指定链接导航到的位置。

```python demo
rx.link("Reflex Home Page.", href="https://reflex.dev/")
```

你也可以提供指向项目中其他页面的本地链接，无需编写完整 URL。

```python demo
rx.link("Example", href="/docs/library")
```

要在新标签页中打开链接，请将 `is_external` 属性设置为 `True`。

```python demo
rx.link("Open in new tab", href="https://reflex.dev/", is_external=True)
```

查看[链接文档](/docs/library/typography/link)了解更多。

```md video https://youtube.com/embed/ITOZkzjtjUA?start=4083&end=4423
# Video: Link-based Navigation
```

### 重定向

使用 `rx.redirect()` 将用户重定向到应用内的新路径。

- `path`：用户应被重定向到的目标路径或 URL。
- `external`：如果设置为 True，重定向将在新标签页中打开。默认为 `False`。

```python demo
rx.vstack(
    rx.button(
        "open in tab", on_click=rx.redirect("/docs/api-reference/special-events")
    ),
    rx.button(
        "open in new tab",
        on_click=rx.redirect("https://github.com/reflex-dev/reflex/", is_external=True),
    ),
)
```

重定向也可以从 State 中的事件处理器运行，这意味着可以在其后添加逻辑。必须 `return` 该 `rx.redirect()`。

```python demo exec
class Redirect2ExampleState(rx.State):
    redirect_to_org: bool = False

    @rx.event
    def change_redirect(self):
        self.redirect_to_org = not self.redirect_to_org

    @rx.var
    def url(self) -> str:
        return (
            "https://github.com/reflex-dev/"
            if self.redirect_to_org
            else "https://github.com/reflex-dev/reflex/"
        )

    @rx.event
    def change_page(self):
        return rx.redirect(self.url, is_external=True)


def redirect_example():
    return rx.vstack(
        rx.text(f"{Redirect2ExampleState.url}"),
        rx.button(
            "Change redirect location", on_click=Redirect2ExampleState.change_redirect
        ),
        rx.button(
            "Redirect to new page in State", on_click=Redirect2ExampleState.change_page
        ),
    )
```

```md video https://youtube.com/embed/ITOZkzjtjUA?start=4423&end=4903
# Video: Redirecting to a New Page
```

## 嵌套路由

页面也可以拥有嵌套路由。

```python
def nested_page():
    return rx.text("Nested Page")


app = rx.App()
app.add_page(nested_page, route="/nested/page")
```

该组件将在 `/nested/page` 可访问。

## 页面元数据

你可以添加页面元数据，例如：

- 浏览器标签页中显示的标题
- 搜索结果中显示的描述
- 页面在社交媒体上分享时显示的预览图片
- 任何其他元数据

```python
@rx.page(
    title="My Beautiful App",
    description="A beautiful app built with Reflex",
    image="https://web.reflex-assets.dev/other/logo.jpg",
    meta=meta,
)
def index():
    return rx.text("A Beautiful App")


@rx.page(title="About Page")
def about():
    return rx.text("About Page")


meta = [
    {"name": "theme_color", "content": "#FFFFFF"},
    {"char_set": "UTF-8"},
    {"property": "og:url", "content": "url"},
]

app = rx.App()
```

## 获取当前页面

你可以从任何状态的 `router` 属性访问当前页面。请参阅[路由文档](/docs/utility-methods/router-attributes)了解所有可用属性。

```python
class State(rx.State):
    def some_method(self):
        current_page_route = self.router.route_id
        current_page_url = self.router.url.path
        # ... Your logic here ...
```

`router.route_id` 属性允许你获取当前页面匹配的路由模式，
对于[动态页面](/docs/pages/dynamic-routing)，这将包含 slug 而非用于加载页面的实际值。

要获取浏览器中显示的实际 URL 路径，请使用 `router.url.path`。对于
查询参数和 URL 片段，请分别使用 `router.url.query_parameters` 和
`router.url.fragment`。

在上面的示例中，`current_page_route` 将包含路由模式（例如 `/posts/[id]`），而 `current_page_url`
将包含实际的 URL 路径（例如 `/posts/123`）。

要获取完整的 URL（协议、主机、路径、查询和片段），直接使用 `router.url` —— 它是一个包含完整 URL 的字符串子类。

示例：

```python
class State(rx.State):
    @rx.var
    def current_url(self) -> str:
        return self.router.url


def index():
    return rx.text(State.current_url)


app = rx.App()
app.add_page(index, route="/posts/[id]")
```

在此示例中，在 `localhost` 上运行应显示 `http://localhost:3000/posts/123/`
