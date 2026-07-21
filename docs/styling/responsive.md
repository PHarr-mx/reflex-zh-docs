```python exec
import reflex as rx

cell_style = {
    "font_family": "Instrument Sans",
    "font_style": "normal",
    "font_weight": "500",
    "font_size": "14px",
    "line_height": "1.5",
    "letter_spacing": "-0.0125em",
    "color": "var(--secondary-11)",
}
```

# 响应式设计（Responsive）

Reflex 应用可以设计为响应式，以在手机、平板和桌面端都呈现良好效果。

你可以向任何样式属性传递一个值的列表，以指定其在不同的屏幕尺寸下的值。

```python demo
rx.text(
    "Hello World",
    color=["orange", "red", "purple", "blue", "green"],
)
```

文本将根据你的屏幕尺寸改变颜色。如果你在桌面上，尝试改变浏览器窗口的大小，就能看到颜色变化。

_在 0.5.6 版本中新增_

响应式值也可以使用 `rx.breakpoints` 指定。每个尺寸对应一个键，当屏幕尺寸大于或等于指定的断点（breakpoint）时，将应用该键对应的值。

```python demo
rx.text(
    "Hello World",
    color=rx.breakpoints(
        initial="orange",
        sm="purple",
        lg="green",
    ),
)
```

自定义的响应式断点可以使用 CSS 单位映射到每个组件的值，使用字典代替命名参数。

```python
rx.text(
    "Hello World",
    color=rx.breakpoints({
        "0px": "orange",
        "48em": "purple",
        "80em": "green",
    }),
)
```

对于支持响应式值的 Radix UI 组件字段，你也可以使用 `rx.breakpoints`（注意自定义断点和列表语法不受支持）。

```python demo
rx.grid(
    rx.foreach(
        list(range(6)),
        lambda _: rx.box(bg_color="#a7db76", height="100px", width="100px"),
    ),
    columns=rx.breakpoints(initial="2", sm="4", lg="6"),
    spacing="4",
)
```

## 设置默认值

默认断点如下所示。

```python eval
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell("Size"),
            rx.table.column_header_cell("Width"),
        ),
    ),
    rx.table.body(
        rx.table.row(
            rx.table.cell(
                rx.code(
                    "initial",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                )
            ),
            rx.table.cell("0px", style=cell_style),
        ),
        rx.table.row(
            rx.table.cell(
                rx.code(
                    "xs",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                )
            ),
            rx.table.cell("30em", style=cell_style),
        ),
        rx.table.row(
            rx.table.cell(
                rx.code(
                    "sm",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                )
            ),
            rx.table.cell("48em", style=cell_style),
        ),
        rx.table.row(
            rx.table.cell(
                rx.code(
                    "md",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                )
            ),
            rx.table.cell("62em", style=cell_style),
        ),
        rx.table.row(
            rx.table.cell(
                rx.code(
                    "lg",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                )
            ),
            rx.table.cell("80em", style=cell_style),
        ),
        rx.table.row(
            rx.table.cell(
                rx.code(
                    "xl",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                )
            ),
            rx.table.cell("96em", style=cell_style),
        ),
    ),
    margin_bottom="1em",
)
```

你可以使用样式属性自定义它们。

```python
app = rx.App(style={"breakpoints": ["520px", "768px", "1024px", "1280px", "1640px"]})
```

## 基于显示方式显示组件

响应式的一个常见用例是根据屏幕尺寸显示不同的组件。

Reflex 提供了有用的辅助组件来实现这一点。

```python demo
rx.vstack(
    rx.desktop_only(
        rx.text("Desktop View"),
    ),
    rx.tablet_only(
        rx.text("Tablet View"),
    ),
    rx.mobile_only(
        rx.text("Mobile View"),
    ),
    rx.mobile_and_tablet(
        rx.text("Visible on Mobile and Tablet"),
    ),
    rx.tablet_and_desktop(
        rx.text("Visible on Desktop and Tablet"),
    ),
)
```

## 指定显示断点

你可以使用 `display` 样式属性为响应式组件指定断点。

```python demo
rx.vstack(
    rx.text(
        "Hello World",
        color="green",
        display=["none", "none", "none", "none", "flex"],
    ),
    rx.text(
        "Hello World",
        color="blue",
        display=["none", "none", "none", "flex", "flex"],
    ),
    rx.text(
        "Hello World",
        color="red",
        display=["none", "none", "flex", "flex", "flex"],
    ),
    rx.text(
        "Hello World",
        color="orange",
        display=["none", "flex", "flex", "flex", "flex"],
    ),
    rx.text(
        "Hello World",
        color="yellow",
        display=["flex", "flex", "flex", "flex", "flex"],
    ),
)
```
