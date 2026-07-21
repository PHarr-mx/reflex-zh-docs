```python exec
import reflex as rx
```

# 动态路由

Reflex 中的动态路由允许你处理多变的 URL 结构，从而创建灵活且适应性强的 Web 应用程序。本节涵盖常规动态路由、全匹配路由（catch-all routes）和可选全匹配路由（optional catch-all routes），每个都带有详细示例。

## 常规动态路由

Reflex 中的常规动态路由允许你动态匹配 URL 中的特定段。常规动态路由通过路由字符串/URL 模式中的方括号定义。例如 `/users/[id]` 或 `/products/[category]`。这些动态路由参数可以通过状态变量（state var）访问。对于上面的示例，它们分别是 `rx.State.id` 和 `rx.State.category`。

```md alert info
# 为什么状态变量以 `rx.State.id` 方式访问？

动态路由参数以 `rx.State.id` 和 `rx.State.category` 的形式可访问，因为该变量被添加到根状态（root state），因此可以从任何状态访问。
```

示例：

```python
@rx.page(route="/post/[pid]")
def post():
    """A page that updates based on the route."""
    # Displays the dynamic part of the URL, the post ID
    return rx.heading(rx.State.pid, as_="h2")


app = rx.App()
```

路由中的 [pid] 部分是一个动态段，意味着它可以匹配 URL 中提供的任何值。例如，`/post/5`、`/post/10` 或 `/post/abc` 都将匹配此路由。

如果用户导航到 `/post/5`，`State.post_id` 将返回 `5`，页面将显示 `5` 作为标题。如果 URL 是 `/post/xyz`，它将显示 `xyz`。如果 URL 是 `/post/`（没有任何额外参数），它将显示 `""`。

### 添加动态路由

添加动态路由与其他页面一样使用 `add_page` 方法。唯一的区别在于路由字符串包含方括号括起来的动态段。

如果你使用 `app.add_page` 方法定义页面，则需要先添加动态路由，特别是在它们与非动态路由使用相同函数的情况下。

例如，下面的代码片段将正常工作：

```python
app.add_page(index, route="/page/[page_id]", on_load=DynamicState.on_load)
app.add_page(index, route="/static/x", on_load=DynamicState.on_load)
app.add_page(index)
```

但如果像下面的示例这样更改添加页面的顺序，它将无法正常工作：

```python
app.add_page(index, route="/static/x", on_load=DynamicState.on_load)
app.add_page(index)
app.add_page(index, route="/page/[page_id]", on_load=DynamicState.on_load)
```

## 全匹配路由

Reflex 中的全匹配路由允许你动态匹配 URL 中任意数量的段。

示例：

```python
class State(rx.State):
    @rx.var
    def user_post(self) -> str:
        args = self.router.page.params
        usernames = args.get("splat", [])
        return f"Posts by {', '.join(usernames)}"


@rx.page(route="/users/[id]/posts/[[...splat]]")
def post():
    return rx.center(rx.text(State.user_post))


app = rx.App()
```

在这个例子中，`...splat` 全匹配模式捕获 `/users/` 之后的任意数量的段，允许像 `/users/2/posts/john/` 和 `/users/1/posts/john/doe/` 这样的 URL 匹配该路由。

```md alert
# 全匹配路由必须命名为 `splat`，并且放置在 URL 模式的末尾，以确保正确的路由匹配。
```

### 路由验证表

| 路由模式                                       | 示例 URL                                          | 是否有效 |
| :--------------------------------------------- | :------------------------------------------------ | ------: |
| `/users/posts`                                 | `/users/posts`                                    |   有效 |
| `/products/[category]`                         | `/products/electronics`                           |   有效 |
| `/users/[username]/posts/[id]`                 | `/users/john/posts/5`                             |   有效 |
| `/users/[[...splat]]/posts`                    | `/users/john/posts`                               |   无效 |
|                                                | `/users/john/doe/posts`                           |   无效 |
| `/users/[[...splat]]`                          | `/users/john/`                                    |   有效 |
|                                                | `/users/john/doe`                                 |   有效 |
| `/products/[category]/[[...splat]]`            | `/products/electronics/laptops`                   |   有效 |
|                                                | `/products/electronics/laptops/lenovo`            |   有效 |
| `/products/[category]/[[...splat]]`            | `/products/electronics`                           |   有效 |
|                                                | `/products/electronics/laptops`                   |   有效 |
|                                                | `/products/electronics/laptops/lenovo`            |   有效 |
|                                                | `/products/electronics/laptops/lenovo/thinkpad`   |   有效 |
| `/products/[category]/[[...splat]]/[[...splat]]` | `/products/electronics/laptops`                   |   无效 |
|                                                | `/products/electronics/laptops/lenovo`            |   无效 |
|                                                | `/products/electronics/laptops/lenovo/thinkpad`   |   无效 |
