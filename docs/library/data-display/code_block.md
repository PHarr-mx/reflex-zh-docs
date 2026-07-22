---
components:
  - rx.code_block
---

```python exec
import reflex as rx
```

# 代码块（Code Block）

代码块（Code Block）组件可以在网站中轻松显示代码。
传入一个具有正确缩进的多行字符串，并指定语言即可显示所需的代码。

```python demo
rx.code_block(
    """def fib(n):
    if n <= 1:
        return n
    else:
        return(fib(n-1) + fib(n-2))""",
    language="python",
    show_line_numbers=True,
)
```

## 主题

`theme` 属性必须设置为从 `rx.code_block.themes` 命名空间访问的 `Theme` 值；不接受字符串。默认情况下，代码块在浅色模式下使用 `one_light`，在深色模式下使用 `one_dark`。

```python demo
rx.code_block(
    """print("Hello, world!")""",
    language="python",
    theme=rx.code_block.themes.dracula,
)
```

要选择响应全局颜色模式的主题，请传入带有所需浅色和深色变体的 `rx.color_mode_cond`：

```python demo
rx.code_block(
    """print("Hello, world!")""",
    language="python",
    theme=rx.color_mode_cond(
        light=rx.code_block.themes.one_light,
        dark=rx.code_block.themes.one_dark,
    ),
)
```
