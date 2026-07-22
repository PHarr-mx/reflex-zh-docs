---
components:
  - rx.accordion.root
  - rx.accordion.item

AccordionRoot: |
  lambda **props: rx.accordion.root(
      rx.accordion.item(
          header="First Item",
          content="The first accordion item's content",
          **props,
      ),
      rx.accordion.item(
          header="Second Item",
          content="The second accordion item's content",
          **props,
      ),
      rx.accordion.item(
          header="Third item",
          content="The third accordion item's content",
          **props,
      ),
      width="300px",
      **props,
  )

AccordionItem: |
  lambda **props: rx.accordion.root(
      rx.accordion.item(
          header="Single Item",
          content="The accordion item's content",
          **props,
      ),
      width="300px",
  )
---

```python exec
import reflex as rx
```

# 手风琴（Accordion）

手风琴（Accordion）是一组垂直堆叠的交互式标题，每个标题展开后会显示相关的内容区域。
手风琴组件由 `accordion` 组成，它是组件的根节点，接受 `accordion.item`，
其中包含可折叠区域的所有内容。

## 基本示例

```python demo
rx.accordion.root(
    rx.accordion.item(
        header="First Item", content="The first accordion item's content"
    ),
    rx.accordion.item(
        header="Second Item",
        content="The second accordion item's content",
    ),
    rx.accordion.item(
        header="Third item", content="The third accordion item's content"
    ),
    width="300px",
)
```

## 样式

### 类型

我们使用 `type` 属性来确定是否可以同时打开多个项目。该属性允许的值为
`single` 和 `multiple`，其中 `single` 一次只能打开一个项目。该属性的默认值为 `single`。

```python demo
rx.accordion.root(
    rx.accordion.item(
        header="First Item", content="The first accordion item's content"
    ),
    rx.accordion.item(
        header="Second Item",
        content="The second accordion item's content",
    ),
    rx.accordion.item(
        header="Third item", content="The third accordion item's content"
    ),
    collapsible=True,
    width="300px",
    type="multiple",
)
```

### 默认值

我们使用 `default_value` 属性来指定默认打开哪个项目。该属性的值应为
`accordion.item` 设置的任意唯一值。

```python demo
rx.flex(
    rx.accordion.root(
        rx.accordion.item(
            header="First Item",
            content="The first accordion item's content",
            value="item_1",
        ),
        rx.accordion.item(
            header="Second Item",
            content="The second accordion item's content",
            value="item_2",
        ),
        rx.accordion.item(
            header="Third item",
            content="The third accordion item's content",
            value="item_3",
        ),
        width="300px",
        default_value="item_2",
    ),
    direction="row",
    spacing="2",
)
```

### 可折叠

我们使用 `collapsible` 属性来允许所有项目关闭。如果设置为 `False`，已打开的项目将无法关闭。

```python demo
rx.flex(
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item", content="The second accordion item's content"
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=True,
        width="300px",
    ),
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item", content="The second accordion item's content"
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=False,
        width="300px",
    ),
    direction="row",
    spacing="2",
)
```

### 禁用

我们使用 `disabled` 属性来禁止与手风琴及其所有项目的交互。

```python demo
rx.accordion.root(
    rx.accordion.item(
        header="First Item", content="The first accordion item's content"
    ),
    rx.accordion.item(
        header="Second Item", content="The second accordion item's content"
    ),
    rx.accordion.item(
        header="Third item", content="The third accordion item's content"
    ),
    collapsible=True,
    width="300px",
    disabled=True,
)
```

### 方向

我们使用 `orientation` 属性将手风琴的方向设置为 `vertical` 或 `horizontal`。默认情况下，方向
设置为 `vertical`。请注意，orientation 属性不会改变视觉方向，而是改变
手风琴的功能方向。这意味着对于垂直方向，上和下方向键在下一个或上一个项目之间移动焦点，
而对于水平方向，左或右方向键在项目之间移动焦点。

```python demo
rx.accordion.root(
    rx.accordion.item(
        header="First Item", content="The first accordion item's content"
    ),
    rx.accordion.item(
        header="Second Item",
        content="The second accordion item's content",
    ),
    rx.accordion.item(
        header="Third item", content="The third accordion item's content"
    ),
    collapsible=True,
    width="300px",
    orientation="vertical",
)
```

```python demo
rx.accordion.root(
    rx.accordion.item(
        header="First Item", content="The first accordion item's content"
    ),
    rx.accordion.item(
        header="Second Item",
        content="The second accordion item's content",
    ),
    rx.accordion.item(
        header="Third item", content="The third accordion item's content"
    ),
    collapsible=True,
    width="300px",
    orientation="horizontal",
)
```

### 变体

```python demo
rx.flex(
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item",
            content="The second accordion item's content",
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=True,
        variant="classic",
    ),
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item",
            content="The second accordion item's content",
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=True,
        variant="soft",
    ),
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item",
            content="The second accordion item's content",
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=True,
        variant="outline",
    ),
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item",
            content="The second accordion item's content",
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=True,
        variant="surface",
    ),
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item",
            content="The second accordion item's content",
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=True,
        variant="ghost",
    ),
    direction="row",
    spacing="2",
)
```

### 配色方案

我们使用 `color_scheme` 属性为手风琴背景分配特定颜色，忽略全局主题。

```python demo
rx.flex(
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item",
            content="The second accordion item's content",
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=True,
        width="300px",
        color_scheme="grass",
    ),
    rx.accordion.root(
        rx.accordion.item(
            header="First Item", content="The first accordion item's content"
        ),
        rx.accordion.item(
            header="Second Item",
            content="The second accordion item's content",
        ),
        rx.accordion.item(
            header="Third item", content="The third accordion item's content"
        ),
        collapsible=True,
        width="300px",
        color_scheme="green",
    ),
    direction="row",
    spacing="2",
)
```

### 值

我们使用 `value` 属性来指定要激活的手风琴项目的受控值。
此属性应与 `on_value_change` 事件参数配合使用。

```python demo exec
class AccordionState(rx.State):
    """The app state."""

    value: str = "item_1"
    item_selected: str

    @rx.event
    def change_value(self, value):
        self.value = value
        self.item_selected = f"{value} selected"


def index() -> rx.Component:
    return rx.theme(
        rx.container(
            rx.text(AccordionState.item_selected),
            rx.flex(
                rx.accordion.root(
                    rx.accordion.item(
                        header="Is it accessible?",
                        content=rx.button("Test button"),
                        value="item_1",
                    ),
                    rx.accordion.item(
                        header="Is it unstyled?",
                        content="Yes. It's unstyled by default, giving you freedom over the look and feel.",
                        value="item_2",
                    ),
                    rx.accordion.item(
                        header="Is it finished?",
                        content="It's still in beta, but it's ready to use in production.",
                        value="item_3",
                    ),
                    collapsible=True,
                    width="300px",
                    value=AccordionState.value,
                    on_value_change=lambda value: AccordionState.change_value(value),
                ),
                direction="column",
                spacing="2",
            ),
            padding="2em",
            text_align="center",
        )
    )
```

## AccordionItem

手风琴项目包含可折叠区域的所有部分。

## 样式

### 值

```python demo
rx.accordion.root(
    rx.accordion.item(
        header="First Item",
        content="The first accordion item's content",
        value="item_1",
    ),
    rx.accordion.item(
        header="Second Item",
        content="The second accordion item's content",
        value="item_2",
    ),
    rx.accordion.item(
        header="Third item",
        content="The third accordion item's content",
        value="item_3",
    ),
    collapsible=True,
    width="300px",
)
```

### 禁用

```python demo
rx.accordion.root(
    rx.accordion.item(
        header="First Item",
        content="The first accordion item's content",
        disabled=True,
    ),
    rx.accordion.item(
        header="Second Item",
        content="The second accordion item's content",
    ),
    rx.accordion.item(
        header="Third item", content="The third accordion item's content"
    ),
    collapsible=True,
    width="300px",
    color_scheme="blue",
)
```
