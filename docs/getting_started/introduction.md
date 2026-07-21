```python exec
import reflex as rx
```

# 简介

**约 5 分钟** · **Reflex** 让你能够用**纯 Python** 构建并部署全栈 Web 应用——前端、后端和数据库一站式搞定。无需 JavaScript，无需单独的 API，无需上下文切换。

## 目标

```md section
### 纯 Python

用 Python 编写你的整个应用——前端、后端、数据库。无需学习其他语言。

### 易于上手

几分钟内即可发布你的第一个应用。无需任何 Web 开发经验。

### 完全灵活

从小型数据应用到大型多页面网站，无所不能。**本网站正是用 Reflex 构建并部署的。**

### 开箱即用

一个工具涵盖一切：UI、服务端逻辑和部署。
```

## 构建一个计数器

我们将构建一个计数器应用，让用户可以向上或向下计数。仅需约 20 行 Python，你就能接触到每个 Reflex 应用的三个核心部分：**状态（state）**、**事件处理器（event handlers）** 和 **组件（components）**。

```python exec
class CounterExampleState(rx.State):
    count: int = 0

    @rx.event
    def increment(self):
        self.count += 1

    @rx.event
    def decrement(self):
        self.count -= 1


class IntroTabsState(rx.State):
    """The app state."""

    value: str = "tab1"
    tab_selected: str = ""

    @rx.event
    def change_value(self, val: str):
        self.tab_selected = f"{val} clicked!"
        self.value = val


def counter_code_section(code: str, tab: str) -> rx.Component:
    active = IntroTabsState.value == tab
    return rx.box(
        rx.code_block(
            code,
            class_name="code-block counter-code-block",
        ),
        background=rx.cond(active, "var(--c-violet-3)", "transparent"),
        border_left=rx.cond(
            active,
            "3px solid var(--c-violet-9)",
            "3px solid transparent",
        ),
        padding="0.875rem 1.5rem",
        class_name="w-full transition-colors",
    )


def counter_code_gap() -> rx.Component:
    return rx.box(height="0.875rem", flex_shrink="0")


def tabs():
    return rx.tabs.root(
        rx.tabs.list(
            rx.tabs.trigger("Frontend", value="tab1", class_name="pill-tab"),
            rx.tabs.trigger("Backend", value="tab2", class_name="pill-tab"),
            rx.tabs.trigger("Page", value="tab3", class_name="pill-tab"),
            class_name="pill-tab-list",
        ),
        rx.tabs.content(
            rx.markdown(
                """前端使用 Reflex 组件以声明式方式构建，这些组件会编译为 JS 并在浏览器中运行。对于动态 UI，请使用 `rx.cond` 和 `rx.foreach` 代替 `if` 和 `for`。任何非 UI 逻辑都应放在 `State` 中。
                """,
            ),
            value="tab1",
            class_name="pt-4",
        ),
        rx.tabs.content(
            rx.markdown(
                """在 `State` 类中编写你的后端。在这里你可以定义能够在前端被引用的函数和变量。这段代码直接在服务器上运行，不会被编译，因此没有任何特殊限制。在这里你可以使用任何 Python 外部库并调用任何方法/函数。
                """,
            ),
            value="tab2",
            class_name="pt-4",
        ),
        rx.tabs.content(
            rx.markdown(
                """每个页面都是一个返回 Reflex 组件的 Python 函数。你可以添加任意数量的页面并在它们之间相互链接——详情参见[路由](/docs/pages/overview)。
                """,
            ),
            value="tab3",
            class_name="pt-4",
        ),
        class_name="text-secondary-12 font-normal",
        default_value="tab1",
        value=IntroTabsState.value,
        on_change=lambda x: IntroTabsState.change_value(x),
    )
```

```python demo box id=counter
rx.hstack(
    rx.button(
        "Decrement",
        color_scheme="ruby",
        on_click=CounterExampleState.decrement,
    ),
    rx.heading(CounterExampleState.count, as_="h2", font_size="2em"),
    rx.button(
        "Increment",
        color_scheme="grass",
        on_click=CounterExampleState.increment,
    ),
    spacing="4",
)
```

以下是本示例的完整代码：

```python eval
tabs()
```

```python eval
rx.box(
    counter_code_section("""import reflex as rx """, ""),
    counter_code_gap(),
    counter_code_section(
        """class State(rx.State):
    count: int = 0

    @rx.event
    def increment(self):
        self.count += 1

    @rx.event
    def decrement(self):
        self.count -= 1""",
        "tab2",
    ),
    counter_code_gap(),
    counter_code_section(
        """def index():
    return rx.hstack(
        rx.button(
            "Decrement",
            color_scheme="ruby",
            on_click=State.decrement,
        ),
        rx.heading(State.count, font_size="2em"),
        rx.button(
            "Increment",
            color_scheme="grass",
            on_click=State.increment,
        ),
        spacing="4",
    )""",
        "tab1",
    ),
    counter_code_gap(),
    counter_code_section(
        """app = rx.App()
app.add_page(index)""",
        "tab3",
    ),
    class_name=(
        "w-full flex flex-col overflow-hidden rounded-xl border "
        "border-secondary-4 bg-secondary-2 py-1"
    ),
)
```

## Reflex 应用的结构

让我们逐步拆解这个示例。

### 导入

```python
import reflex as rx
```

将 Reflex 导入为 `rx`。所有 Reflex 对象均通过 `rx.*` 访问。

### 状态（State）

```python
class State(rx.State):
    count: int = 0
```

State 保存应用的可变数据。在这里声明的变量称为 **[vars](/docs/vars/base-vars)**。我们的计数器有一个：`count`，初始值为 `0`。

### 事件处理器（Event Handlers）

```python
@rx.event
def increment(self):
    self.count += 1


@rx.event
def decrement(self):
    self.count -= 1
```

**事件处理器（Event handlers）** 是修改状态的唯一方式。它们由用户操作（点击、输入等）触发——这些操作称为**事件（events）**。我们的计数器有两个处理器：`increment` 和 `decrement`。

### 用户界面（UI）

```python
def index():
    return rx.hstack(
        rx.button(
            "Decrement",
            color_scheme="ruby",
            on_click=State.decrement,
        ),
        rx.heading(State.count, as_="h2", font_size="2em"),
        rx.button(
            "Increment",
            color_scheme="grass",
            on_click=State.increment,
        ),
        spacing="4",
    )
```

UI 由组件（`rx.hstack`、`rx.button`、`rx.heading`）构建，可以嵌套并通过 CSS 或 [Tailwind](/docs/styling/tailwind) 设置样式。Reflex 内置了 [50 多个组件](/docs/library)，你还可以[包装任何 React 组件](/docs/wrapping-react/overview)。

组件引用状态 vars（`rx.heading(State.count, …)`），并在状态变化时响应式地重新渲染。事件触发器（`on_click=State.decrement`）将 UI 与处理器连接起来。

整个流程如下：

1. 用户点击 "Increment"。
2. `on_click` 被触发。
3. `State.increment` 在服务器上运行。
4. `State.count` 被更新。
5. UI 使用新值重新渲染。

### 添加页面

```python
app = rx.App()
app.add_page(index)
```

创建应用并将页面注册到根路由。

## 下一步

🎉 你已经用纯 Python 构建了一个完全可交互的 Web 应用。

```md alert info
# 继续学习

- [仪表盘教程](/docs/getting-started/dashboard-tutorial/)——构建一个真实的数据应用。
- [聊天应用教程](/docs/getting-started/chatapp-tutorial/)——接入流式 AI 响应。
- [Reflex 工作原理](/docs/advanced-onboarding/how-reflex-works/)——了解底层发生了什么。
```

```md alert info
# 借助 AI 更快交付

- [Reflex Build](https://build.reflex.dev/)——通过提示词生成完整应用。
- [Reflex Cloud](/docs/hosting/deploy-quick-start/)——一条命令即可部署。
```

按 `Cmd+K` / `Ctrl+K` 搜索文档。
