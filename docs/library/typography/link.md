---
components:
  - rx.link
---

```python exec
import reflex as rx
```

# Link（链接）

链接是主要用于导航的无障碍元素。使用 `href` prop 来指定链接要导航到的位置。

```python demo
rx.link("Reflex Home Page.", href="https://reflex.dev/")
```

你也可以提供指向项目中其他页面的本地链接，而无需编写完整的 URL。

```python demo
rx.link(
    "Example",
    href="/docs/library",
)
```

`link` 组件可以用来包裹其他组件，使它们链接到其他页面。

```python demo
rx.link(rx.button("Example"), href="https://reflex.dev/")
```

你还可以使用 `id` prop 创建锚点，链接到页面的特定部分。

```python demo
rx.box("Example", id="example")
```

要引用锚点，可以使用 `link` 组件的 `href` prop。`href` 的格式应该是你要链接的页面路径，后跟 # 和锚点的 id。

```python demo
rx.link("Example", href="/docs/library/typography/link#example")
```

```md alert info
# 使用 State 重定向用户

也可以使用 `rx.redirect()` 将用户重定向到应用内的新路径。查看[此处](/docs/api-reference/special-events)的文档。
```

## 样式

## 尺寸

使用 `size` prop 来控制链接的大小。该 prop 还提供正确的行高和修正的字间距——随着文本尺寸增大，相对行高和字间距会减小。

```python demo
rx.flex(
    rx.link("The quick brown fox jumps over the lazy dog.", size="1"),
    rx.link("The quick brown fox jumps over the lazy dog.", size="2"),
    rx.link("The quick brown fox jumps over the lazy dog.", size="3"),
    rx.link("The quick brown fox jumps over the lazy dog.", size="4"),
    rx.link("The quick brown fox jumps over the lazy dog.", size="5"),
    rx.link("The quick brown fox jumps over the lazy dog.", size="6"),
    rx.link("The quick brown fox jumps over the lazy dog.", size="7"),
    rx.link("The quick brown fox jumps over the lazy dog.", size="8"),
    rx.link("The quick brown fox jumps over the lazy dog.", size="9"),
    direction="column",
    spacing="3",
)
```

## 字重

使用 `weight` prop 来设置文本字重。

```python demo
rx.flex(
    rx.link("The quick brown fox jumps over the lazy dog.", weight="light"),
    rx.link("The quick brown fox jumps over the lazy dog.", weight="regular"),
    rx.link("The quick brown fox jumps over the lazy dog.", weight="medium"),
    rx.link("The quick brown fox jumps over the lazy dog.", weight="bold"),
    direction="column",
    spacing="3",
)
```

## 修剪

使用 `trim` prop 来修剪渲染文本开头、结尾或两侧的 leading 空间。

```python demo
rx.flex(
    rx.link(
        "Without Trim",
        trim="normal",
        style={
            "background": "var(--gray-a2)",
            "border_top": "1px dashed var(--gray-a7)",
            "border_bottom": "1px dashed var(--gray-a7)",
        },
    ),
    rx.link(
        "With Trim",
        trim="both",
        style={
            "background": "var(--gray-a2)",
            "border_top": "1px dashed var(--gray-a7)",
            "border_bottom": "1px dashed var(--gray-a7)",
        },
    ),
    direction="column",
    spacing="3",
)
```

## 下划线

使用 `underline` prop 来管理下划线提示的可见性。默认为 `auto`。

```python demo
rx.flex(
    rx.link("The quick brown fox jumps over the lazy dog.", underline="auto"),
    rx.link("The quick brown fox jumps over the lazy dog.", underline="hover"),
    rx.link("The quick brown fox jumps over the lazy dog.", underline="always"),
    direction="column",
    spacing="3",
)
```

## 颜色

使用 `color_scheme` prop 来指定特定颜色，忽略全局主题。

```python demo
rx.flex(
    rx.link("The quick brown fox jumps over the lazy dog.", color_scheme="indigo"),
    rx.link("The quick brown fox jumps over the lazy dog.", color_scheme="cyan"),
    rx.link("The quick brown fox jumps over the lazy dog.", color_scheme="crimson"),
    rx.link("The quick brown fox jumps over the lazy dog.", color_scheme="orange"),
    direction="column",
)
```

## 高对比度

使用 `high_contrast` prop 来增加与背景的颜色对比度。

```python demo
rx.flex(
    rx.link("The quick brown fox jumps over the lazy dog."),
    rx.link("The quick brown fox jumps over the lazy dog.", high_contrast=True),
    direction="column",
)
```
