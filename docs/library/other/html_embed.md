---
components:
  - rx.html
---

```python exec
import reflex as rx
```

# HTML 嵌入（HTML Embed）

HTML 组件可用于渲染原始 HTML 代码。

在使用此组件之前，请考虑使用 Reflex 的原始 HTML 元素支持。

```python demo
rx.vstack(
    rx.html("<h2>Hello World</h2>"),
    rx.html("<h3>Hello World</h3>"),
    rx.html("<h4>Hello World</h4>"),
    rx.html("<h5>Hello World</h5>"),
    rx.html("<h6>Hello World</h6>"),
)
```

```md alert
# 样式缺失？

Reflex 使用 Radix-UI 和 Tailwind 进行样式设置，两者都会重置标题的默认样式。如果你使用 html 组件并希望获得美观的默认样式，可以考虑设置 `class_name='prose'`，将 `@tailwindcss/typography` 包添加到 `frontend_packages` 中，并通过 `rxconfig.py` 中的 `tailwind` 配置启用它。有关添加此插件的示例，请参阅 [Tailwind 文档](/docs/styling/overview)。
```

在这个例子中，我们渲染了一张图片。

```python demo
rx.html(
    "<img src='https://web.reflex-assets.dev/other/reflex_banner.png' alt='Reflex banner' />"
)
```
