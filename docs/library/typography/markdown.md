---
components:
  - rx.markdown
---

```python exec
import reflex as rx
```

# Markdown

`rx.markdown` 组件可用于渲染 Markdown 文本。
它基于 [Github Flavored Markdown](https://github.github.com/gfm/)。

```python demo
rx.vstack(
    rx.markdown("## Hello World!"),
    rx.markdown("### Hello World!"),
    rx.markdown("#### Hello World!"),
    rx.markdown("Support us on [Github](https://github.com/reflex-dev/reflex)."),
    rx.markdown("Use `reflex deploy` to deploy your app with **a single command**."),
)
```

## 数学公式

你可以使用 LaTeX 渲染数学公式。
对于行内公式，用 `$` 包围公式：

```python demo
rx.markdown("Pythagorean theorem: $a^2 + b^2 = c^2$.")
```

```md alert warning
# 转义美元符号
由于 `$` 被用作数学定界符，内容中的任何一对 `$` 字符都会被解释为行内 LaTeX 公式——它们之间的文本可能会消失或渲染为乱码公式。这通常会影响价格和其他货币值，尤其是在流式 LLM 输出等动态内容中。要渲染字面美元符号，请在将文本传递给 `rx.markdown` 之前进行转义，例如 `content.replace("$", "\\$")`。
```

## 语法高亮

你可以使用 \`\`\`{language} 语法渲染带语法高亮的代码块：

````python demo
rx.markdown(
    r"""
```python
import reflex as rx
from .pages import index

app = rx.App()
app.add_page(index)
```
"""
)
````

## 表格

你可以使用 `|` 语法渲染表格：

```python demo
rx.markdown(
    """
| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title       |
| Paragraph   | Text        |
"""
)
```

## 插件

插件可用于扩展 Markdown 渲染器的功能。

默认情况下，Reflex 使用以下插件：

- `remark-gfm` 用于 Github Flavored Markdown 支持（`use_gfm`）。
- `remark-math` 和 `rehype-katex` 用于数学公式支持（`use_math`、`use_katex`）。
- `rehype-unwrap-images` 用于移除图片周围的段落标签（`use_unwrap_images`）。
- `rehype-raw` 用于渲染 Markdown 中的原始 HTML（`use_raw`）。注意：出于安全原因，未来版本将默认禁用此功能。

这些默认插件可以通过向 `rx.markdown` 组件传递 `use_[plugin_name]=False` 来禁用。例如，要禁用原始 HTML 渲染，使用 `rx.markdown(..., use_raw=False)`。

## 任意插件

你还可以使用 `remark_plugins` 和 `rehype_plugins` prop 配合 `rx.markdown.plugin` 辅助函数来添加任意 remark 或 rehype 插件。

`rx.markdown.plugin` 接受两个参数：

1. 插件的 npm 包名和版本（例如 `remark-emoji@5.0.2`）。
2. 要使用的插件命名导出（例如 `remarkEmoji`）。

### Remark 插件示例

例如，要使用 `remark-emoji` 插件添加 emoji 支持：

```python demo
rx.markdown(
    "Hello :smile:! :rocket: :tada:",
    remark_plugins=[
        rx.markdown.plugin("remark-emoji@5.0.2", "remarkEmoji"),
    ],
)
```

### Rehype 插件示例

为了使 `rehype-raw` 对不受信任的 HTML 输入更安全，我们可以使用 `rehype-sanitize`，它默认使用类似于 Github 的安全 schema。

```python demo
rx.markdown(
    """Here is some **bold** text and a <script>alert("XSS Attack!")</script>.""",
    use_raw=True,
    rehype_plugins=[
        rx.markdown.plugin("rehype-sanitize@5.0.1", "rehypeSanitize"),
    ],
)
```

### 插件选项

`remark_plugins` 和 `rehype_plugins` 都接受一个混合列表，包含 `plugin` 或 `(plugin, options)` 元组，用于插件需要某种特殊配置的情况。

例如，`rehype-slug` 是一个简单的插件，为标题添加 ID 属性，但 `rehype-autolink-headings` 插件接受选项来指定如何渲染指向这些锚点的链接。

```python demo
rx.markdown(
    """
## Heading 2
### Heading 3
""",
    rehype_plugins=[
        rx.markdown.plugin("rehype-slug@6.0.0", "rehypeSlug"),
        (
            rx.markdown.plugin(
                "rehype-autolink-headings@7.1.0", "rehypeAutolinkHeadings"
            ),
            {
                "behavior": "wrap",
                "properties": {
                    "className": ["heading-link"],
                },
            },
        ),
    ],
)
```

## 组件映射

你可以使用 `component_map` prop 来指定用于渲染 Markdown 元素的组件。

`component_map` prop 中的每个键是一个 Markdown 元素，值是一个函数，接受该元素的文本作为输入并返回一个 Reflex 组件。

```md alert
`pre` 和 `a` 标签是特殊情况。除了 `text` 之外，它们还接收一个包含组件额外 props 的 `props` 参数。
```

````python demo exec
component_map = {
    "h1": lambda text: rx.heading(text, as_="h2", size="5", margin_y="1em"),
    "h2": lambda text: rx.heading(text, as_="h2", size="3", margin_y="1em"),
    "h3": lambda text: rx.heading(text, as_="h2", size="1", margin_y="1em"),
    "p": lambda text: rx.text(text, color="green", margin_y="1em"),
    "code": lambda text: rx.code(text, color="purple"),
    "pre": lambda text, **props: rx.code_block(
        text, **props, theme=rx.code_block.themes.dark, margin_y="1em"
    ),
    "a": lambda text, **props: rx.link(
        text, **props, color="blue", _hover={"color": "red"}
    ),
}


def index():
    return rx.box(
        rx.markdown(
            r"""
# Hello World!

## This is a Subheader

### And Another Subheader

Here is some `code`:

```python
import reflex as rx

component = rx.text("Hello World!")
```

And then some more text here,
followed by a link to
[Reflex](https://reflex.dev/).
""",
            component_map=component_map,
        )
    )
````
