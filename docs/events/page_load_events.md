```python exec
import reflex as rx
```

# 页面加载事件

你还可以指定在页面加载时运行的函数。这对于在每次渲染或状态变更时只获取一次数据很有用。
在此示例中，我们在页面加载时获取数据：

```python
class State(rx.State):
    data: Dict[str, Any]

    @rx.event
    def get_data(self):
        # Fetch data
        self.data = fetch_data()


@rx.page(on_load=State.get_data)
def index():
    return rx.text("A Beautiful App")
```

另一个示例是在页面加载时检查用户是否已通过身份验证。如果用户未通过身份验证，我们将其重定向到登录页面。如果已通过身份验证，则不执行任何操作，允许其访问页面。这个 `on_load` 事件将被放置在每个需要身份验证才能访问的页面上。

```python
class State(rx.State):
    authenticated: bool

    @rx.event
    def check_auth(self):
        # Check if user is authenticated
        self.authenticated = check_auth()
        if not self.authenticated:
            return rx.redirect("/login")


@rx.page(on_load=State.check_auth)
def index():
    return rx.text("A Beautiful App")
```
