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

# 主题化（Theming）

自 Reflex `v0.4.0` 起，你可以为 Reflex 应用设置主题。我们主题系统的核心直接基于 [Radix Themes](https://www.radix-ui.com) 库。这使你可以轻松更改应用的主题，同时提供默认的浅色和深色主题。主题会使所有组件具有统一的颜色外观。

## 概述

`Theme` 组件用于更改应用的主题。`Theme` 可以直接在 `rx.App` 中设置。

```python
app = rx.App(
    theme=rx.theme(
        appearance="light", has_background=True, radius="large", accent_color="teal"
    )
)
```

以下是可传递给 `rx.theme` 组件的属性：

```python eval
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell("Name", class_name="table-header"),
            rx.table.column_header_cell("Type", class_name="table-header"),
            rx.table.column_header_cell("Description", class_name="table-header"),
        ),
    ),
    rx.table.body(
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "has_background",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    "Bool",
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                "Whether to apply the themes background color to the theme node. Defaults to True.",
                style=cell_style,
            ),
        ),
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "appearance",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    '"inherit" | "light" | "dark"',
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                "The appearance of the theme. Can be 'light' or 'dark'. Defaults to 'light'.",
                style=cell_style,
            ),
        ),
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "accent_color",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    "Str",
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                "The primary color used for default buttons, typography, backgrounds, etc.",
                style=cell_style,
            ),
        ),
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "gray_color",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    "Str",
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                "The secondary color used for default buttons, typography, backgrounds, etc.",
                style=cell_style,
            ),
        ),
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "panel_background",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    '"solid" | "translucent"',
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                'Whether panel backgrounds are translucent: "solid" | "translucent" (default).',
                style=cell_style,
            ),
        ),
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "radius",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    '"none" | "small" | "medium" | "large" | "full"',
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                )
            ),
            rx.table.cell(
                "The radius of the theme. Can be 'small', 'medium', or 'large'. Defaults to 'medium'.",
                style=cell_style,
            ),
        ),
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "scaling",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    '"90%" | "95%" | "100%" | "105%" | "110%"',
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell("Scale of all theme items.", style=cell_style),
        ),
    ),
    variant="surface",
    margin_y="1em",
)
```

此外，你还可以通过使用 `Theme Panel` 组件来修改应用的主题，该组件可以在[主题面板文档](/docs/library/other/theme)中找到。

## 颜色（Colors）

### 配色方案（Color Scheme）

在高层面上，组件的 `color_scheme` 继承自主题中指定的颜色。这意味着如果你更改主题，组件的颜色也会随之更改。可用的颜色可以在[此处](https://www.radix-ui.com/colors)找到。

你也可以指定 `color_scheme` 属性。

```python demo
rx.flex(
    rx.button(
        "Hello World",
        color_scheme="tomato",
    ),
    rx.button(
        "Hello World",
        color_scheme="teal",
    ),
    spacing="2",
)
```

### 色度（Shades）

有时你可能想使用主题中某个颜色的特定色度。建议使用此方式而非直接使用十六进制颜色，因为它会在主题在浅色/深色之间切换时自动变化。

要访问主题中特定色度的颜色，可以使用 `rx.color`。在浅色和深色主题之间切换时，颜色将自动变化。可以通过使用颜色名称和色度编号来访问色度。色度编号范围为 1 到 12。此外，可以通过使用 `True` 参数来设置其 alpha 值，默认为 `False`。完整的颜色列表可以在[此处](https://www.radix-ui.com/colors)找到。

```python demo
rx.flex(
    rx.button(
        "Hello World",
        color=rx.color("grass", 1),
        background_color=rx.color("grass", 7),
        border_color=f"1px solid {rx.color('grass', 1)}",
    ),
    spacing="2",
)
```

```python eval
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell("Name"),
            rx.table.column_header_cell("Type"),
            rx.table.column_header_cell("Description"),
        ),
    ),
    rx.table.body(
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "color",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    "Str",
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                "The color to use. Can be any valid accent color or 'accent' to reference the current theme color.",
                style=cell_style,
            ),
        ),
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "shade",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.link(
                    rx.code(
                        "1 - 12",
                        style={
                            "color": rx.color("gray", 11),
                            "border_radius": "0.25rem",
                            "border": f"1px solid {rx.color('gray', 5)}",
                            "background": rx.color("gray", 3),
                        },
                        class_name="code-style",
                    ),
                    href="https://www.radix-ui.com/colors",
                )
            ),
            rx.table.cell(
                "The shade of the color to use. Defaults to 7.", style=cell_style
            ),
        ),
        rx.table.row(
            rx.table.row_header_cell(
                rx.code(
                    "alpha",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                rx.code(
                    "Bool",
                    style={
                        "color": rx.color("gray", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('gray', 5)}",
                        "background": rx.color("gray", 3),
                    },
                    class_name="code-style",
                )
            ),
            rx.table.cell(
                "Whether to use the alpha value of the color. Defaults to False.",
                style=cell_style,
            ),
        ),
    ),
    variant="surface",
    margin_y="1em",
)
```

### 常规颜色（Regular Colors）

你也可以使用标准的十六进制、rgb 和 rgba 颜色。

```python demo
rx.flex(
    rx.button(
        "Hello World",
        color="white",
        background_color="#87CEFA",
        border="1px solid rgb(176,196,222)",
    ),
    spacing="2",
)
```

## 切换外观（Toggle Appearance）

要在浅色和深色模式之间手动切换，你可以将 `toggle_color_mode` 与任意事件触发器一起使用。

```python
from reflex.style import toggle_color_mode


def index():
    return rx.button(
        "Toggle Color Mode",
        on_click=toggle_color_mode,
    )
```

## 外观条件渲染（Appearance Conditional Rendering）

要根据应用处于 `light` 模式还是 `dark` 模式来渲染不同的组件，可以使用 `rx.color_mode_cond` 组件。如果应用处于 `light` 模式，则渲染第一个组件；如果应用处于 `dark` 模式，则渲染第二个组件。

```python demo
rx.color_mode_cond(
    light=rx.image(
        src="https://web.reflex-assets.dev/logos/light/reflex.svg",
        alt="Reflex Logo light",
        height="4em",
    ),
    dark=rx.image(
        src="https://web.reflex-assets.dev/logos/dark/reflex.svg",
        alt="Reflex Logo dark",
        height="4em",
    ),
)
```

这也可以应用于属性。

```python demo
rx.button(
    "Hello World",
    color=rx.color_mode_cond(light="black", dark="white"),
    background_color=rx.color_mode_cond(light="white", dark="black"),
)
```
