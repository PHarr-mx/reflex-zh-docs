```python exec
import reflex as rx
```

# 样式（Styling）

Reflex 组件可以使用完整的 [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS) 功能进行样式设置。

有三种主要方式为应用添加样式，它们的优先级按以下顺序排列：

1. **行内样式（Inline）：** 应用于单个组件实例的样式。
2. **组件样式（Component）：** 应用于特定类型组件的样式。
3. **全局样式（Global）：** 应用于所有组件的样式。

```md alert success
# 样式键可以是任何有效的 CSS 属性名。

为了与 Python 标准保持一致，你可以使用 `snake_case` 格式指定键名。
```

## 全局样式（Global Styles）

你可以向应用传递一个样式字典，为所有组件应用基础样式。

例如，你可以在此一次性设置默认字体系列和字体大小，而不必在每个组件上单独设置。

```python
style = {
    "font_family": "Comic Sans MS",
    "font_size": "16px",
}

app = rx.App(style=style)
```

## 组件样式（Component Styles）

在你的样式字典中，你还可以为特定组件类型或任意 CSS 类和 ID 指定默认样式。

```python
style = {
    # 全局设置选中高亮颜色。
    "::selection": {
        "background_color": accent_color,
    },
    # 应用全局 CSS 类样式。
    ".some-css-class": {
        "text_decoration": "underline",
    },
    # 应用全局 CSS ID 样式。
    "#special-input": {"width": "20vw"},
    # 为特定组件应用样式。
    rx.text: {
        "font_family": "Comic Sans MS",
    },
    rx.divider: {
        "margin_bottom": "1em",
        "margin_top": "0.5em",
    },
    rx.heading: {
        "font_weight": "500",
    },
    rx.code: {
        "color": "green",
    },
}

app = rx.App(style=style)
```

使用这样的样式字典，你可以轻松为应用创建一致的主题。

```md alert warning
# 注意类名和 ID 中的下划线

Reflex 在应用样式时会自动将 `snake_case` 标识符转换为 `camelCase` 格式。为了保持一致，建议在自定义类名和 ID 中使用连字符或 camelCase 标识符。如果要为依赖下划线类名的第三方库设置样式，应使用外部样式表。请参阅[自定义样式表](/docs/styling/custom-stylesheets)了解如何引用外部 CSS 文件。
```

## 行内样式（Inline Styles）

行内样式应用于单个组件实例。它们作为常规属性（props）传递给组件。

```python demo
rx.text(
    "Hello World",
    background_image="linear-gradient(271.68deg, #EE756A 0.75%, #756AEE 88.52%)",
    background_clip="text",
    font_weight="bold",
    font_size="2em",
)
```

子组件会继承行内样式，除非被它们自己的行内样式覆盖。

```python demo
rx.box(
    rx.hstack(
        rx.button("Default Button"),
        rx.button("Red Button", color="red"),
    ),
    color="blue",
)
```

### 样式属性（Style Prop）

行内样式也可以使用 `style` 属性进行设置。这对于在多个组件之间复用样式非常有用。

```python exec
text_style = {
    "color": "green",
    "font_family": "Comic Sans MS",
    "font_size": "1.2em",
    "font_weight": "bold",
    "box_shadow": "rgba(240, 46, 170, 0.4) 5px 5px, rgba(240, 46, 170, 0.3) 10px 10px",
}
```

```python
text_style = {
    "color": "green",
    "font_family": "Comic Sans MS",
    "font_size": "1.2em",
    "font_weight": "bold",
    "box_shadow": "rgba(240, 46, 170, 0.4) 5px 5px, rgba(240, 46, 170, 0.3) 10px 10px",
}
```

```python demo
rx.vstack(
    rx.text("Hello", style=text_style),
    rx.text("World", style=text_style),
)
```

```python exec
style1 = {
    "color": "green",
    "font_family": "Comic Sans MS",
    "border_radius": "10px",
    "background_color": "rgb(107,99,246)",
}
style2 = {
    "color": "white",
    "border": "5px solid #EE756A",
    "padding": "10px",
}
```

```python
style1 = {
    "color": "green",
    "font_family": "Comic Sans MS",
    "border_radius": "10px",
    "background_color": "rgb(107,99,246)",
}
style2 = {
    "color": "white",
    "border": "5px solid #EE756A",
    "padding": "10px",
}
```

```python demo
rx.box(
    "Multiple Styles",
    style=[style1, style2],
)
```

样式字典按传递的顺序应用。这意味着后面定义的样式会覆盖前面定义的样式。

## 主题化（Theming）

自 Reflex 'v0.4.0' 起，你可以为 Reflex Web 应用设置主题。要了解更多，请查看[主题文档](/docs/styling/theming)。

`Theme` 组件用于更改应用的主题。`Theme` 可以直接在 `rx.App` 中设置。

```python
app = rx.App(
    theme=rx.theme(
        appearance="light", has_background=True, radius="large", accent_color="teal"
    )
)
```

此外，你还可以通过使用 `Theme Panel` 组件来修改应用的主题，该组件可以在[主题面板文档](/docs/library/other/theme)中找到。

## 特殊样式（Special Styles）

我们支持 Chakra UI 的所有[伪样式](https://v2.chakra-ui.com/docs/styled-system/style-props#pseudo)。

以下是一个文本在悬停时改变颜色的示例。

```python demo
rx.box(
    rx.text("Hover Me", _hover={"color": "red"}),
)
```
