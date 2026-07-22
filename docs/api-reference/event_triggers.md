```python exec
from datetime import datetime

import reflex as rx
```

# 事件触发器（Event Triggers）

组件可以根据用户事件（如点击按钮或在字段中输入文本）来修改状态。
这些事件由事件触发器触发。

事件触发器是组件特定的，并在每个组件的文档中列出。

## 组件生命周期事件

Reflex 组件具有生命周期事件，如 `on_mount` 和 `on_unmount`，允许你在组件存在的特定时刻执行代码。这些事件对于初始化数据、清理资源和创建动态用户界面至关重要。

### 生命周期事件何时激活

- **on_mount**：此事件在组件渲染并附加到 DOM 后立即触发。它在以下情况触发：
  - 包含该组件的页面首次加载时
  - 组件条件渲染时（隐藏后出现）
  - 使用内部导航导航到包含该组件的页面时
  - 页面刷新或跟随外部链接时不触发

- **on_unmount**：此事件在组件从 DOM 中移除之前触发。它在以下情况触发：
  - 使用内部导航从包含该组件的页面导航离开时
  - 组件从 DOM 中条件移除时（例如，通过隐藏它的条件）
  - 刷新页面、关闭浏览器选项卡或跟随外部链接时不触发

## 页面加载事件

除了组件生命周期事件外，Reflex 还提供页面级事件，如 `on_load`，在页面加载时触发。`on_load` 事件适用于：

- 页面首次加载时获取数据
- 检查认证状态
- 初始化页面特定状态
- 设置 cookie 或浏览器存储的默认值

你可以使用 `@rx.page` 装饰器或 `app.add_page()` 方法中的 `on_load` 参数指定在页面加载时运行的事件处理程序：

```python
class State(rx.State):
    data: dict = dict()

    @rx.event
    def get_data(self):
        # 页面加载时获取数据
        self.data = fetch_data()


@rx.page(on_load=State.get_data)
def index():
    return rx.text("Data loaded on page load")
```

这对于认证检查特别有用：

```python
class State(rx.State):
    authenticated: bool = False

    @rx.event
    def check_auth(self):
        # 检查用户是否已认证
        self.authenticated = check_auth()
        if not self.authenticated:
            return rx.redirect("/login")


@rx.page(on_load=State.check_auth)
def protected_page():
    return rx.text("Protected content")
```

有关页面加载事件的更多详情，请参阅[页面加载事件文档](/docs/events/page-load-events)。

## 事件参考

---

## on_focus

on_focus 事件处理程序在元素（或其中的某个元素）获得焦点时调用。例如，当用户点击文本输入时调用。

```python demo exec
class FocusState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self, text):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def focus_example():
    return rx.input(value=FocusState.text, on_focus=FocusState.change_text)
```

## on_blur

on_blur 事件处理程序在焦点离开元素（或离开其中的某个元素）时调用。例如，当用户点击聚焦的文本输入之外时调用。

```python demo exec
class BlurState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self, text):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def blur_example():
    return rx.input(value=BlurState.text, on_blur=BlurState.change_text)
```

## on_change

on_change 事件处理程序在元素的值更改时调用。例如，当用户在文本输入中输入时，每次按键都会触发 on change。

```python demo exec
class ChangeState(rx.State):
    checked: bool = False

    @rx.event
    def set_checked(self):
        self.checked = not self.checked


def change_example():
    return rx.switch(on_change=ChangeState.set_checked)
```

## on_click

on_click 事件处理程序在用户点击元素时调用。例如，当用户点击按钮时调用。

```python demo exec
class ClickState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def click_example():
    return rx.button(ClickState.text, on_click=ClickState.change_text)
```

## on_context_menu

on_context_menu 事件处理程序在用户右键点击元素时调用。例如，当用户右键点击按钮时调用。

```python demo exec
class ContextState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def context_menu_example():
    return rx.button(ContextState.text, on_context_menu=ContextState.change_text)
```

## on_double_click

on_double_click 事件处理程序在用户双击元素时调用。例如，当用户双击按钮时调用。

```python demo exec
class DoubleClickState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def double_click_example():
    return rx.button(
        DoubleClickState.text, on_double_click=DoubleClickState.change_text
    )
```

## on_mount

on_mount 事件处理程序在组件在页面上渲染后调用。它类似于页面 on_load 事件，尽管在页面之间导航时不一定触发。此事件对于在组件首次出现时初始化数据、进行 API 调用或设置组件特定状态特别有用。

```python demo exec
class MountState(rx.State):
    events: list[str] = []
    data: list[dict] = []
    loading: bool = False

    @rx.event
    def on_mount(self):
        self.events = self.events[-4:] + ["on_mount @ " + str(datetime.now())]

    @rx.event
    async def load_data(self):
        self.loading = True
        yield
        import asyncio

        await asyncio.sleep(1)
        self.data = [dict(id=1, name="Item 1"), dict(id=2, name="Item 2")]
        self.loading = False


def mount_example():
    return rx.vstack(
        rx.heading("Component Lifecycle Demo", as_="h2"),
        rx.foreach(MountState.events, rx.text),
        rx.cond(
            MountState.loading,
            rx.spinner(),
            rx.foreach(
                MountState.data,
                lambda item: rx.text(f"ID: {item['id']} - {item['name']}"),
            ),
        ),
        on_mount=MountState.on_mount,
    )
```

## on_unmount

on_unmount 事件处理程序在从页面中移除组件后调用。但是，on_unmount 仅在内部导航时调用，而在跟随外部链接或刷新页面时不调用。此事件对于在组件从 DOM 中移除之前清理资源、保存状态或执行清理操作非常有用。

```python demo exec
class UnmountState(rx.State):
    events: list[str] = []
    resource_id: str = "resource-12345"
    status: str = "Resource active"

    @rx.event
    def on_unmount(self):
        self.events = self.events[-4:] + ["on_unmount @ " + str(datetime.now())]
        self.status = f"Resource {self.resource_id} cleaned up"

    @rx.event
    def initialize_resource(self):
        self.status = f"Resource {self.resource_id} initialized"


def unmount_example():
    return rx.vstack(
        rx.heading("Unmount Demo", as_="h2"),
        rx.foreach(UnmountState.events, rx.text),
        rx.text(UnmountState.status),
        rx.link(
            rx.button("Navigate Away (Triggers Unmount)"),
            href="/",
        ),
        on_mount=UnmountState.initialize_resource,
        on_unmount=UnmountState.on_unmount,
    )
```

## on_mouse_up

on_mouse_up 事件处理程序在用户在元素上释放鼠标按钮时调用。例如，当用户在按钮上释放左鼠标按钮时调用。

```python demo exec
class MouseUpState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def mouse_up_example():
    return rx.button(MouseUpState.text, on_mouse_up=MouseUpState.change_text)
```

## on_mouse_down

on_mouse_down 事件处理程序在用户在元素上按下鼠标按钮时调用。例如，当用户在按钮上按下左鼠标按钮时调用。

```python demo exec
class MouseDownState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def mouse_down_example():
    return rx.button(MouseDownState.text, on_mouse_down=MouseDownState.change_text)
```

## on_mouse_enter

on_mouse_enter 事件处理程序在用户的鼠标进入元素时调用。例如，当用户的鼠标进入按钮时调用。

```python demo exec
class MouseEnterState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def mouse_enter_example():
    return rx.button(MouseEnterState.text, on_mouse_enter=MouseEnterState.change_text)
```

## on_mouse_leave

on_mouse_leave 事件处理程序在用户的鼠标离开元素时调用。例如，当用户的鼠标离开按钮时调用。

```python demo exec
class MouseLeaveState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def mouse_leave_example():
    return rx.button(MouseLeaveState.text, on_mouse_leave=MouseLeaveState.change_text)
```

## on_mouse_move

on_mouse_move 事件处理程序在用户在元素上移动鼠标时调用。例如，当用户在按钮上移动鼠标时调用。

```python demo exec
class MouseMoveState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def mouse_move_example():
    return rx.button(MouseMoveState.text, on_mouse_move=MouseMoveState.change_text)
```

## on_mouse_out

on_mouse_out 事件处理程序在用户的鼠标离开元素时调用。例如，当用户的鼠标离开按钮时调用。

```python demo exec
class MouseOutState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def mouse_out_example():
    return rx.button(MouseOutState.text, on_mouse_out=MouseOutState.change_text)
```

## on_mouse_over

on_mouse_over 事件处理程序在用户的鼠标进入元素时调用。例如，当用户的鼠标进入按钮时调用。

```python demo exec
class MouseOverState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def mouse_over_example():
    return rx.button(MouseOverState.text, on_mouse_over=MouseOverState.change_text)
```

## on_scroll

on_scroll 事件处理程序在用户滚动页面时调用。例如，当用户向下滚动页面时调用。

```python demo exec
class ScrollState(rx.State):
    text: str = "Change Me!"

    @rx.event
    def change_text(self):
        if self.text == "Change Me!":
            self.text = "Changed!"
        else:
            self.text = "Change Me!"


def scroll_example():
    return rx.vstack(
        rx.text("Scroll to make the text below change."),
        rx.text(ScrollState.text),
        rx.text("Scroll to make the text above change."),
        on_scroll=ScrollState.change_text,
        overflow="auto",
        height="3em",
        width="100%",
    )
```
