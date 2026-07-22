---
components:
  - rx.flex

Flex: |
  lambda **props: rx.flex(
      rx.card("Card 1", size="2", width="5rem"),
      rx.card("Card 2", size="2", width="5rem"),
      rx.card("Card 3", size="2", width="5rem"),
      gap="0.75rem",
      width="100%",
      min_height="10rem",
      border="1px dashed var(--gray-7)",
      border_radius="0.5rem",
      padding="0.75rem",
      **props,
  )
---

```python exec
import reflex as rx
```

# 弹性布局（Flex）

Flex 组件用于创建[弹性盒子布局](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox)。
它可以轻松地将子组件按水平或垂直方向排列、应用换行、对齐内容，并根据可用空间自动调整组件大小，非常适合构建响应式布局。

默认情况下，子元素按水平方向排列（`direction="row"`），不换行。

## 基本示例

```python demo
rx.flex(
    rx.card("Card 1"),
    rx.card("Card 2"),
    rx.card("Card 3"),
    rx.card("Card 4"),
    rx.card("Card 5"),
    spacing="2",
    width="100%",
)
```

## 换行

设置 `flex_wrap="wrap"` 后，子元素会换到下一行，而不是被缩放。

```python demo
rx.flex(
    rx.foreach(
        rx.Var.range(10),
        lambda i: rx.card(f"Card {i + 1}", width="16%"),
    ),
    spacing="2",
    flex_wrap="wrap",
    width="100%",
)
```

## 方向

设置 `direction="column"` 后，子元素将按垂直方向排列。

```python demo
rx.flex(
    rx.card("Card 1"),
    rx.card("Card 2"),
    rx.card("Card 3"),
    rx.card("Card 4"),
    spacing="2",
    direction="column",
)
```

## 对齐

两个属性控制子元素在 Flex 组件中的对齐方式：

- `align` 控制子元素沿交叉轴的对齐方式（`row` 方向时为垂直轴，`column` 方向时为水平轴）。
- `justify` 控制子元素沿主轴的对齐方式（`row` 方向时为水平轴，`column` 方向时为垂直轴）。

以下示例直观地展示了这些属性在不同 `wrap` 和 `direction` 值下的效果。

```python demo exec
class FlexPlaygroundState(rx.State):
    align: str = "stretch"
    justify: str = "start"
    direction: str = "row"
    wrap: str = "nowrap"

    @rx.event
    def set_align(self, value: str):
        self.align = value

    @rx.event
    def set_justify(self, value: str):
        self.justify = value

    @rx.event
    def set_direction(self, value: str):
        self.direction = value

    @rx.event
    def set_wrap(self, value: str):
        self.wrap = value


def select(label, items, value, on_change):
    return rx.flex(
        rx.text(label),
        rx.select.root(
            rx.select.trigger(),
            rx.select.content(*[rx.select.item(item, value=item) for item in items]),
            value=value,
            on_change=on_change,
        ),
        align="center",
        justify="center",
        direction="column",
    )


def selectors():
    return rx.flex(
        select(
            "Wrap",
            ["nowrap", "wrap", "wrap-reverse"],
            FlexPlaygroundState.wrap,
            FlexPlaygroundState.set_wrap,
        ),
        select(
            "Direction",
            ["row", "column", "row-reverse", "column-reverse"],
            FlexPlaygroundState.direction,
            FlexPlaygroundState.set_direction,
        ),
        select(
            "Align",
            ["start", "center", "end", "baseline", "stretch"],
            FlexPlaygroundState.align,
            FlexPlaygroundState.set_align,
        ),
        select(
            "Justify",
            ["start", "center", "end", "between"],
            FlexPlaygroundState.justify,
            FlexPlaygroundState.set_justify,
        ),
        width="100%",
        spacing="2",
        justify="between",
    )


def example1():
    return rx.box(
        selectors(),
        rx.flex(
            rx.foreach(
                rx.Var.range(10),
                lambda i: rx.card(f"Card {i + 1}", width="16%"),
            ),
            spacing="2",
            direction=FlexPlaygroundState.direction,
            align=FlexPlaygroundState.align,
            justify=FlexPlaygroundState.justify,
            wrap=FlexPlaygroundState.wrap,
            width="100%",
            height="20vh",
            margin_top="16px",
        ),
        width="100%",
    )
```

## 尺寸提示

当子组件包含在弹性容器中时，`flex_grow`（默认 `"0"`）和 `flex_shrink`（默认 `"1"`）属性控制该盒子相对于同一容器中其他组件的尺寸调整方式。

尺寸调整始终应用于弹性容器的主轴。如果方向为 `row`，则尺寸调整应用于 `width`。如果方向为 `column`，则尺寸调整应用于 `height`。要设置主轴上的最优尺寸，可使用 `flex_basis` 属性，其值可以是百分比或 CSS 尺寸单位。未指定时，如果设置了对应的 `width` 或 `height` 值则使用该值，否则使用内容尺寸。

当 `flex_grow="0"` 时，盒子不会增长超过 `flex_basis`。

当 `flex_shrink="0"` 时，盒子不会缩小到小于 `flex_basis`。

这些属性用于创建灵活的响应式布局。

拖动下面的滑块，观察调整弹性容器宽度时，基于所设置的属性如何影响弹性项目的计算尺寸。

```python demo exec
class FlexGrowShrinkState(rx.State):
    width_pct: list[int] = [100]

    @rx.event
    def set_width_pct(self, value: list[int | float]):
        self.width_pct = [int(value[0])]


def border_box(*children, **props):
    return rx.box(
        *children,
        border="1px solid var(--gray-10)",
        border_radius="2px",
        **props,
    )


def example2():
    return rx.box(
        rx.flex(
            border_box("flex_shrink=0", flex_shrink="0", width="100px"),
            border_box("flex_shrink=1", flex_shrink="1", width="200px"),
            border_box("flex_grow=0", flex_grow="0"),
            border_box("flex_grow=1", flex_grow="1"),
            width=f"{FlexGrowShrinkState.width_pct}%",
            margin_bottom="16px",
            spacing="2",
        ),
        rx.slider(
            min=0,
            max=100,
            value=FlexGrowShrinkState.width_pct,
            on_change=FlexGrowShrinkState.set_width_pct,
        ),
        width="100%",
    )
```
