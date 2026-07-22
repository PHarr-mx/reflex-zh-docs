---
components:
  - rx.tabs.root
  - rx.tabs.list
  - rx.tabs.trigger
  - rx.tabs.content

only_low_level:
  - True

TabsRoot: |
  lambda **props: rx.tabs.root(
      rx.tabs.list(
          rx.tabs.trigger("Account", value="account"),
          rx.tabs.trigger("Documents", value="documents"),
          rx.tabs.trigger("Settings", value="settings"),
      ),
      rx.box(
          rx.tabs.content(
              rx.text("Make changes to your account"),
              value="account",
          ),
          rx.tabs.content(
              rx.text("Update your documents"),
              value="documents",
          ),
          rx.tabs.content(
              rx.text("Edit your personal profile"),
              value="settings",
          ),
      ),
      **props,
  )

TabsList: |
  lambda **props: rx.tabs.root(
      rx.tabs.list(
          rx.tabs.trigger("Account", value="account"),
          rx.tabs.trigger("Documents", value="documents"),
          rx.tabs.trigger("Settings", value="settings"),
          **props,
      ),
      rx.box(
          rx.tabs.content(
              rx.text("Make changes to your account"),
              value="account",
          ),
          rx.tabs.content(
              rx.text("Update your documents"),
              value="documents",
          ),
          rx.tabs.content(
              rx.text("Edit your personal profile"),
              value="settings",
          ),
      ),
  )

TabsTrigger: |
  lambda **props: rx.tabs.root(
      rx.tabs.list(
          rx.tabs.trigger("Account", value="account", **props),
          rx.tabs.trigger("Documents", value="documents", **props),
          rx.tabs.trigger("Settings", value="settings", **props),
      ),
      rx.box(
          rx.tabs.content(
              rx.text("Make changes to your account"),
              value="account",
          ),
          rx.tabs.content(
              rx.text("Update your documents"),
              value="documents",
          ),
          rx.tabs.content(
              rx.text("Edit your personal profile"),
              value="settings",
          ),
      ),
  )

TabsContent: |
  lambda **props: rx.tabs.root(
      rx.tabs.list(
          rx.tabs.trigger("Account", value="account"),
          rx.tabs.trigger("Documents", value="documents"),
          rx.tabs.trigger("Settings", value="settings"),
      ),
      rx.box(
          rx.tabs.content(
              rx.text("Make changes to your account"),
              value="account",
              **props,
          ),
          rx.tabs.content(
              rx.text("Update your documents"),
              value="documents",
              **props,
          ),
          rx.tabs.content(
              rx.text("Edit your personal profile"),
              value="settings",
              **props,
          ),
      ),
  )
---

```python exec
import reflex as rx
```

# 选项卡（Tabs）

选项卡（Tabs）是一组分层的、被称为选项卡面板的内容区域，每次只显示一个。
它们有助于组织和导航具有关联关系且处于相似层级的一组内容。

## 基本示例

```python demo
rx.tabs.root(
    rx.tabs.list(
        rx.tabs.trigger("Tab 1", value="tab1"), rx.tabs.trigger("Tab 2", value="tab2")
    ),
    rx.tabs.content(
        rx.text("item on tab 1"),
        value="tab1",
    ),
    rx.tabs.content(
        rx.text("item on tab 2"),
        value="tab2",
    ),
)
```

`tabs` 组件由 `rx.tabs.root` 组成，它将 `rx.tabs.list` 和 `rx.tabs.content` 部分组合在一起。

## 样式

### 默认值

我们使用 `default_value` 属性来设置默认激活的选项卡，这将默认选中指定的选项卡。

```python demo
rx.tabs.root(
    rx.tabs.list(
        rx.tabs.trigger("Tab 1", value="tab1"), rx.tabs.trigger("Tab 2", value="tab2")
    ),
    rx.tabs.content(
        rx.text("item on tab 1"),
        value="tab1",
    ),
    rx.tabs.content(
        rx.text("item on tab 2"),
        value="tab2",
    ),
    default_value="tab2",
)
```

### 方向

我们使用 `orientation` 属性将选项卡组件的方向设置为 `vertical` 或 `horizontal`。默认情况下，方向
设置为 `horizontal`。设置此值将同时改变视觉方向和功能方向。

```md alert info
功能方向意味着对于 `vertical`，`上`和`下`方向键在下一个或上一个选项卡之间移动焦点，
而对于 `horizontal`，`左`和`右`方向键在选项卡之间移动焦点。
```

```md alert warning
# 使用垂直方向时，请确保为每个 trigger 分配一个 tabs.content。

定义没有内容的 trigger 会导致视觉错误，即 trigger 列表的宽度不是恒定的。
```

```python demo
rx.tabs.root(
    rx.tabs.list(
        rx.tabs.trigger("Tab 1", value="tab1"), rx.tabs.trigger("Tab 2", value="tab2")
    ),
    rx.tabs.content(
        rx.text("item on tab 1"),
        value="tab1",
    ),
    rx.tabs.content(
        rx.text("item on tab 2"),
        value="tab2",
    ),
    default_value="tab1",
    orientation="vertical",
)
```

```python demo
rx.tabs.root(
    rx.tabs.list(
        rx.tabs.trigger("Tab 1", value="tab1"), rx.tabs.trigger("Tab 2", value="tab2")
    ),
    rx.tabs.content(
        rx.text("item on tab 1"),
        value="tab1",
    ),
    rx.tabs.content(
        rx.text("item on tab 2"),
        value="tab2",
    ),
    default_value="tab1",
    orientation="horizontal",
)
```

### 值

我们使用 `value` 属性来指定要激活的选项卡的受控值。此属性应与 `on_change` 事件参数配合使用。

```python demo exec
class TabsState(rx.State):
    """The app state."""

    value = "tab1"
    tab_selected = ""

    @rx.event
    def change_value(self, val):
        self.tab_selected = f"{val} clicked!"
        self.value = val


def index() -> rx.Component:
    return rx.container(
        rx.flex(
            rx.text(f"{TabsState.value}  clicked !"),
            rx.tabs.root(
                rx.tabs.list(
                    rx.tabs.trigger("Tab 1", value="tab1"),
                    rx.tabs.trigger("Tab 2", value="tab2"),
                ),
                rx.tabs.content(
                    rx.text("items on tab 1"),
                    value="tab1",
                ),
                rx.tabs.content(
                    rx.text("items on tab 2"),
                    value="tab2",
                ),
                default_value="tab1",
                value=TabsState.value,
                on_change=lambda x: TabsState.change_value(x),
            ),
            direction="column",
            spacing="2",
        ),
        padding="2em",
        font_size="2em",
        text_align="center",
    )
```

## Tablist

Tablist 用于列出选项卡组件中各自的选项卡。

## Tab Trigger

这是激活选项卡关联内容的按钮。通常在 `Tablist` 中使用。

## 样式

### 值

我们使用 `value` 属性来分配一个唯一值，将 trigger 与内容关联起来。

```python demo
rx.tabs.root(
    rx.tabs.list(
        rx.tabs.trigger("Tab 1", value="tab1"),
        rx.tabs.trigger("Tab 2", value="tab2"),
        rx.tabs.trigger("Tab 3", value="tab3"),
    ),
)
```

### 禁用

我们使用 `disabled` 属性来禁用选项卡。

```python demo
rx.tabs.root(
    rx.tabs.list(
        rx.tabs.trigger("Tab 1", value="tab1"),
        rx.tabs.trigger("Tab 2", value="tab2"),
        rx.tabs.trigger("Tab 3", value="tab3", disabled=True),
    ),
)
```

## Tabs Content

包含与每个 trigger 关联的内容。

## 样式

### 值

我们使用 `value` 属性来分配一个唯一值，将内容与 trigger 关联起来。

```python
rx.tabs.root(
    rx.tabs.list(
        rx.tabs.trigger("Tab 1", value="tab1"), rx.tabs.trigger("Tab 2", value="tab2")
    ),
    rx.tabs.content(
        rx.text("item on tab 1"),
        value="tab1",
    ),
    rx.tabs.content(
        rx.text("item on tab 2"),
        value="tab2",
    ),
    default_value="tab1",
    orientation="vertical",
)
```
