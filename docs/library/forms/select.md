---
components:
  - rx.select

HighLevelSelect: |
  lambda **props: rx.select(["apple", "grape", "pear"], default_value="pear", **props)

SelectRoot: |
  lambda **props: rx.select.root(
      rx.select.trigger(),
      rx.select.content(
          rx.select.group(
              rx.select.item("apple", value="apple"),
              rx.select.item("grape", value="grape"),
              rx.select.item("pear", value="pear"),
          ),
      ),
      default_value="pear",
      **props
  )

SelectTrigger: |
  lambda **props: rx.select.root(
      rx.select.trigger(**props),
      rx.select.content(
          rx.select.group(
              rx.select.item("apple", value="apple"),
              rx.select.item("grape", value="grape"),
              rx.select.item("pear", value="pear"),
          ),
      ),
      default_value="pear",
  )

SelectContent: |
  lambda **props: rx.select.root(
      rx.select.trigger(),
      rx.select.content(
          rx.select.group(
              rx.select.item("apple", value="apple"),
              rx.select.item("grape", value="grape"),
              rx.select.item("pear", value="pear"),
          ),
          **props,
      ),
      default_value="pear",
  )

SelectItem: |
  lambda **props: rx.select.root(
      rx.select.trigger(),
      rx.select.content(
          rx.select.group(
              rx.select.item("apple", value="apple", **props),
              rx.select.item("grape", value="grape", **props),
              rx.select.item("pear", value="pear", **props),
          ),
      ),
      default_value="pear",
  )
---

```python exec
import random
import reflex as rx
```

# 选择器（Select）

选择器（Select）组件显示一个下拉选项列表，供用户从中选择一个。它是 Web 应用中最常见的表单控件之一，当你需要用户从预定义列表中选择单个值且屏幕空间有限时，就可以使用它。

Reflex 的 `rx.select` 是一个功能完备、具有无障碍支持的下拉组件，基于 Radix UI 原语构建。它适用于简单的选项列表或完整的状态绑定，支持表单集成、键盘导航，并且在桌面端和移动端开箱即用。

## 何时使用选择器

在以下情况使用 `rx.select`：

- 你有超过五个选项且需要节省垂直空间
- 用户应该从预定义列表中恰好选择一个选项
- 你想要一个与应用主题一致的统一风格下拉框

在以下情况考虑替代方案：

- 你只有不到五个选项 → 使用[单选组](/docs/library/forms/radio-group)以获得更好的可见性
- 用户需要选择多个值 → 使用[复选框](/docs/library/forms/checkbox)组或多选模式
- 用户应该输入值而不是选择 → 使用[输入框](/docs/library/forms/input)
- 你需要分层或可搜索的选项 → 使用带有自定义内容的[底层 Select API](/docs/library/forms/select/low)

## 基本用法

最简单的用法是传入一个字符串选项列表。组件会渲染一个下拉按钮，点击后打开选项菜单。

```python demo
rx.select(["apple", "grape", "pear"])
```

用户可以点击触发按钮打开下拉框并选择不同的选项。

## 跟踪选中的值

在大多数实际应用中，你需要在用户选择值时做出响应——过滤数据、更新图表、保存到数据库或触发其他事件。使用 `value` 属性和 `on_change` 事件处理函数将选择器绑定到[状态](/docs/state/overview)变量。

```python demo exec
class SelectState(rx.State):
    selected_fruit: str = "apple"

    @rx.event
    def update_fruit(self, value: str):
        self.selected_fruit = value


def select_with_state():
    return rx.hstack(
        rx.select(
            ["apple", "grape", "pear"],
            value=SelectState.selected_fruit,
            on_change=SelectState.update_fruit,
        ),
        rx.text("You selected:"),
        rx.badge(SelectState.selected_fruit),
        align="center",
        spacing="3",
    )
```

这是最常见的模式。因为选择器绑定到了状态变量，所以值始终反映其中存储的内容，你可以从应用的任何位置更新它，选择器会自动重新渲染。

## 设置默认值

如果你只需要选择器以特定选项开始，而不需要在状态中跟踪用户的选择，使用 `default_value`。当你只关心表单提交时的值，或者不需要选中的值影响应用中的其他内容时，这是最简单的模式。

```python demo
rx.select(
    ["apple", "grape", "pear"],
    default_value="grape",
)
```

## 占位文本

当你想让选择器初始为空，提示用户做出选择时，省略 `default_value` 并提供 `placeholder`。

```python demo
rx.select(
    ["apple", "grape", "pear"],
    placeholder="Choose a fruit…",
)
```

## 从状态动态获取选项

实际应用很少有硬编码的选项。更常见的是，选项来自数据库、API 或从其他状态计算得出。将状态变量作为选项列表传入，当列表变化时下拉框会自动更新。

```python demo exec
class SelectStateDynamic(rx.State):
    options: list[str] = ["apple", "grape", "pear"]
    selected: str = "apple"

    @rx.event
    def set_selected(self, value: str):
        self.selected = value

    @rx.event
    def randomize(self):
        self.selected = random.choice(self.options)

    @rx.event
    def add_option(self):
        new_options = ["banana", "orange", "mango", "kiwi", "cherry"]
        available = [o for o in new_options if o not in self.options]
        if available:
            self.options = self.options + [available[0]]


def select_dynamic():
    return rx.vstack(
        rx.select(
            SelectStateDynamic.options,
            value=SelectStateDynamic.selected,
            on_change=SelectStateDynamic.set_selected,
        ),
        rx.hstack(
            rx.button("Pick Random", on_click=SelectStateDynamic.randomize),
            rx.button("Add Option", on_click=SelectStateDynamic.add_option),
            spacing="2",
        ),
        spacing="3",
    )
```

## 禁用状态

要阻止用户交互，设置 `disabled=True`。选择器会以灰色外观渲染且无法打开。

```python demo
rx.select(
    ["apple", "grape", "pear"],
    default_value="apple",
    disabled=True,
)
```

要禁用单个选项而不是整个选择器，使用[底层 API](/docs/library/forms/select/low) 并在特定的 `rx.select.item` 组件上设置 `disabled=True`。

## 在表单中使用选择器

选择器组件与 Reflex 表单无缝集成。`name` 属性设置表单提交时使用的键，`required=True` 阻止用户在做出选择之前提交。

```python demo exec
class SelectFormState(rx.State):
    form_data: dict = {}

    @rx.event
    def handle_submit(self, form_data: dict):
        self.form_data = form_data


def select_form():
    return rx.card(
        rx.vstack(
            rx.heading("Order Form", as_="h2", size="4"),
            rx.form.root(
                rx.vstack(
                    rx.text("Favorite fruit"),
                    rx.select(
                        ["apple", "grape", "pear"],
                        name="fruit",
                        placeholder="Pick one",
                        required=True,
                    ),
                    rx.text("Quantity"),
                    rx.select(
                        ["1", "2", "3", "4", "5"],
                        name="quantity",
                        default_value="1",
                    ),
                    rx.button("Submit Order", type="submit"),
                    spacing="2",
                    align="stretch",
                ),
                on_submit=SelectFormState.handle_submit,
                reset_on_submit=True,
            ),
            rx.divider(),
            rx.hstack(
                rx.heading("Results:", as_="h2"),
                rx.badge(SelectFormState.form_data.to_string()),
            ),
            spacing="3",
            width="100%",
            align_items="left",
        ),
        width="400px",
    )
```

有关构建表单、验证和提交处理的完整详细信息，请参阅[表单文档](/docs/library/forms/form)。

## 将显示标签映射到底层值

当你的选项有单独的显示标签和底层值时（例如，值为用户 ID，标签为名称），使用[计算变量](/docs/vars/computed-vars)在它们之间进行映射。

```python demo exec
class SelectDictState(rx.State):
    users: dict[str, str] = {
        "user_001": "Alice Johnson",
        "user_002": "Bob Smith",
        "user_003": "Carol Davis",
    }
    selected_name: str = "Alice Johnson"

    @rx.var
    def user_names(self) -> list[str]:
        return list(self.users.values())

    @rx.var
    def selected_id(self) -> str:
        for uid, name in self.users.items():
            if name == self.selected_name:
                return uid
        return ""

    @rx.event
    def set_user(self, name: str):
        self.selected_name = name


def select_dict_example():
    return rx.vstack(
        rx.select(
            SelectDictState.user_names,
            value=SelectDictState.selected_name,
            on_change=SelectDictState.set_user,
        ),
        rx.text("Selected user ID:"),
        rx.badge(SelectDictState.selected_id),
        spacing="3",
    )
```

要在下拉框本身中原生支持标签/值分离，使用[底层 Select API](/docs/library/forms/select/low) 并向每个 `rx.select.item` 传入 `value=` 和显示标签子元素。

## 在对话框中使用选择器

当将选择器放置在[对话框](/docs/library/overlay/dialog)或其他基于 portal 的容器中时，在选择器上设置 `position="popper"`，使下拉菜单正确定位在覆盖层内容之上。

```python demo
rx.dialog.root(
    rx.dialog.trigger(rx.button("Open Dialog")),
    rx.dialog.content(
        rx.dialog.title("Pick a fruit"),
        rx.vstack(
            rx.select(
                ["apple", "grape", "pear"],
                position="popper",
                default_value="apple",
            ),
            rx.dialog.close(rx.button("Close")),
            spacing="3",
        ),
    ),
)
```

## 响应打开和关闭事件

除了 `on_change` 之外，`on_open_change` 事件会在下拉框打开或关闭时触发。使用它来触发分析、预取数据或为相关 UI 添加动画。

下面的示例使用 [rx.cond](/docs/library/dynamic-rendering/cond) 根据下拉框是否打开来切换两个徽章。

```python demo exec
class SelectOpenState(rx.State):
    is_open: bool = False
    open_count: int = 0

    @rx.event
    def on_toggle(self, is_open: bool):
        self.is_open = is_open
        if is_open:
            self.open_count += 1


def select_open_change():
    return rx.vstack(
        rx.select(
            ["apple", "grape", "pear"],
            default_value="apple",
            on_open_change=SelectOpenState.on_toggle,
        ),
        rx.text("Open count: ", SelectOpenState.open_count),
        rx.cond(
            SelectOpenState.is_open,
            rx.badge("Open", color_scheme="green"),
            rx.badge("Closed", color_scheme="gray"),
        ),
        spacing="3",
    )
```

## 常见模式

### 根据选择过滤列表

一个经典用例——选择器控制页面其他位置显示的数据。

```python demo exec
class FilterState(rx.State):
    all_items: list[dict[str, str]] = [
        {"name": "MacBook Pro", "category": "laptops"},
        {"name": "Dell XPS", "category": "laptops"},
        {"name": "iPhone 15", "category": "phones"},
        {"name": "Pixel 8", "category": "phones"},
        {"name": "iPad Air", "category": "tablets"},
        {"name": "Galaxy Tab", "category": "tablets"},
    ]
    category: str = "laptops"

    @rx.event
    def set_category(self, value: str):
        self.category = value

    @rx.var
    def filtered_items(self) -> list[dict[str, str]]:
        return [i for i in self.all_items if i["category"] == self.category]


def select_filter():
    return rx.vstack(
        rx.select(
            ["laptops", "phones", "tablets"],
            value=FilterState.category,
            on_change=FilterState.set_category,
        ),
        rx.foreach(
            FilterState.filtered_items,
            lambda item: rx.card(item["name"]),
        ),
        spacing="3",
        width="300px",
    )
```

### 级联选择器

当一个选择器的选项取决于另一个选择器的值时——这是国家/省份、类别/子类别等的常见模式。

```python demo exec
class CascadeState(rx.State):
    regions: dict[str, list[str]] = {
        "North America": ["USA", "Canada", "Mexico"],
        "Europe": ["UK", "France", "Germany"],
        "Asia": ["Japan", "Korea", "Singapore"],
    }
    region: str = "North America"
    country: str = "USA"

    @rx.event
    def set_region(self, value: str):
        self.region = value
        self.country = self.regions[value][0]

    @rx.event
    def set_country(self, value: str):
        self.country = value

    @rx.var
    def region_names(self) -> list[str]:
        return list(self.regions.keys())

    @rx.var
    def countries(self) -> list[str]:
        return self.regions.get(self.region, [])


def select_cascade():
    return rx.hstack(
        rx.select(
            CascadeState.region_names,
            value=CascadeState.region,
            on_change=CascadeState.set_region,
        ),
        rx.select(
            CascadeState.countries,
            value=CascadeState.country,
            on_change=CascadeState.set_country,
        ),
        spacing="3",
    )
```


<!-- faqs-start -->

## 常见问题

### st.selectbox 的最佳替代方案是什么？

对于原型和快速数据脚本，`st.selectbox` 就够用了。但对于生产应用，你需要一个不会在每次交互时触发全页重新运行的下拉组件。`rx.select` 是最直接的替代方案——同样简单的 API，但基于 Radix UI 构建为真正的 React 组件，绑定到 Python 状态。它开箱即用地支持表单集成、键盘导航和无障碍访问，并且适用于带有认证、路由和复杂状态的应用。

### 如何在不编写 JavaScript 的情况下用 Python 创建下拉菜单？

使用 `rx.select(["option_1", "option_2", "option_3"])`。这就是一个完整可用的下拉菜单的全部代码——Reflex 将你的 Python 编译为 React 应用，因此菜单会渲染为带样式的、具有无障碍支持的 Web 组件，无需你编写任何 JavaScript、HTML 或 CSS。对于绑定到应用状态的交互式下拉框，添加指向状态变量和事件处理函数的 `value=` 和 `on_change=` 属性。

### 如何用更简单的 API 替换 dcc.Dropdown？

Plotly Dash 的 `dcc.Dropdown` 可以用，但需要回调装饰器模式——每次交互都需要一个带有显式输入和输出的 `@app.callback`。`rx.select` 用标准的 Python 事件处理函数完成同样的工作：在状态类上定义一个方法，将其传递给 `on_change=`，Reflex 会处理连接。迁移通常是机械性的：将 `dcc.Dropdown(options=..., value=..., id=...)` 替换为 `rx.select(options, value=State.value, on_change=State.set_value)` 并删除 `@app.callback` 装饰器。

### 为什么我的 Streamlit selectbox 会导致整个页面重新运行？

因为这就是 Streamlit 的默认工作方式——每次组件交互都会从头重新运行整个脚本。这对小脚本没问题，但随着应用增长会成为性能问题。替代方案有：积极使用 `st.cache_data` 和 `st.session_state` 来限制重新计算，切换到 fragments（`@st.fragment`）进行隔离的重新运行，或者迁移到像 Reflex 这样的框架，其中状态更新触发细粒度的重新渲染而不是完全重新运行。Reflex 的 `rx.select` 只更新实际依赖于选中值的 UI 元素。

### 哪个下拉组件最适合生产环境的 Python Web 应用？

对于原型，`st.selectbox`（Streamlit）、`gr.Dropdown`（Gradio）和 `dcc.Dropdown`（Plotly Dash）都可以。对于带有认证、路由和复杂状态的生产应用，`rx.select`（Reflex）是专门为此构建的——底层是真正的 React 组件，支持完整的表单集成，并且不会受到限制 Streamlit 大规模使用的全页重新运行问题的影响。选择通常取决于你是在构建快速演示还是真正的产品。

### 如何构建一个过滤数据表的下拉框？

将下拉框绑定到状态变量，然后使用计算属性派生过滤后的数据。在 Reflex 中：`rx.select(categories, value=State.selected, on_change=State.set_selected)` 配合 `@rx.var filtered_rows(self) -> list: return [r for r in self.all_rows if r.category == self.selected]`。这个模式在 Streamlit、Dash 和 Gradio 中基本相同，但 Reflex 的版本只重新渲染表格，而不是整个页面。

### 有什么好的 Retool 替代方案来构建带下拉框的管理面板？

Reflex 是构建内部工具和管理面板最流行的开源 Retool 替代方案。与 Retool 不同——Retool 是一个基于席位定价的托管低代码平台——Reflex 是开源的、可自托管的，让你用 Python 构建一切，包括下拉框（`rx.select`）、表格、表单和完整的 CRUD 工作流。你获得与 Retool 相同的基于组件的用户体验，但拥有完整的代码所有权且没有按用户收费。

### 如何在 Python 中从数据库填充下拉框？

在页面加载时将值加载到状态变量中，然后将该变量作为选项列表传入。在 Reflex 应用中：在 State 类上定义 `options: list[str] = []`，在查询数据库的 `on_mount` 处理函数中填充它，然后将 `State.options` 传递给 `rx.select`。当底层数据变化时，下拉框会自动更新。同样的模式适用于任何数据库——SQLAlchemy、原始 SQL、ORM 或 API 调用。

### 如何构建带有可搜索或可过滤选项的下拉框？

原生 `<select>` 元素不支持搜索。对于可搜索的下拉框——有时称为组合框（combobox）或自动补全（typeahead）——你需要自定义实现。在 Reflex 中，这通过底层 Select API 结合一个过滤选项列表的输入框来完成。Streamlit 的 `st.selectbox` 添加了基本的搜索行为，Dash 有 `dcc.Dropdown(searchable=True)`，但与使用可组合原语自己构建相比，两者在自定义方面都有限制。

### 下拉框和多选框有什么区别？

下拉框（或单选）让用户选择一个选项。多选框让他们选择多个。在原生 HTML 中这些是不同的元素（`<select>` 与 `<select multiple>`）。在 Python 框架中：Streamlit 使用 `st.selectbox` 和 `st.multiselect`，Dash 使用 `dcc.Dropdown(multi=True)`，Reflex 使用 `rx.select` 进行单选，使用 `rxe.mantine.multi_select`（Reflex Enterprise）进行多选。根据你的数据模型是否允许每个字段有一个或多个值来选择。

<!-- faqs-end -->

## 相关组件

- [单选组](/docs/library/forms/radio-group) - 少于 5 个选项时的内联单选
- [复选框](/docs/library/forms/checkbox) - 具有可见状态的单选或多选
- [表单](/docs/library/forms/form) - 将选择器与其他输入组合在一起提交
- [对话框](/docs/library/overlay/dialog) - 可以包含选择器的模态对话框
- [底层 Select API](/docs/library/forms/select/low) - 对触发器、内容和选项的细粒度控制

## 相关概念

- [状态管理](/docs/state/overview) - Reflex 组件如何跟踪和更新值
- [事件处理函数](/docs/events/events-overview) - 理解 `on_change`、`on_open_change` 和相关触发器
- [表单和验证](/docs/library/forms/form) - 构建带有类型化、验证输入的完整表单
- [计算变量](/docs/vars/computed-vars) - 从状态派生值用于动态选项和查找
