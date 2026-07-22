```python exec
import reflex as rx
```

# 特殊事件（Special Events）

Reflex 包含一组内置的特殊事件，可以用作事件触发器
或从应用中的事件处理程序返回。这些事件增强了交互性和用户体验。
以下是 Reflex 中可用的特殊事件及其功能说明：

## rx.console_log

在浏览器的控制台中执行 console.log。

```python demo
rx.button("Log", on_click=rx.console_log("Hello World!"))
```

触发时，此事件将指定的消息记录到浏览器的开发者控制台。
它对于调试和监控应用的行为非常有用。

## rx.scroll_to

滚动到页面中的元素

```python demo
rx.button("Scroll to download button", on_click=rx.scroll_to("download button"))
```

触发时，它会滚动到作为参数传递的 id 对应的元素。点击按钮滚动到页面底部的下载按钮（rx.download 部分）

## rx.redirect

将用户重定向到应用内的新路径。

### 参数

- `path`：用户应被重定向到的目标路径或 URL。
- `external`：如果设置为 True，重定向将在新选项卡中打开。默认为 `False`。

```python demo
rx.vstack(
    rx.button(
        "open in tab", on_click=rx.redirect("/docs/api-reference/special-events")
    ),
    rx.button(
        "open in new tab",
        on_click=rx.redirect("https://github.com/reflex-dev/reflex/", is_external=True),
    ),
)
```

触发此事件时，它会将用户导航到 Reflex 应用内的不同页面或位置。
默认情况下，重定向在同一选项卡中发生。但是，如果你将 external 参数设置为 True，重定向
将在新选项卡或窗口中打开，提供无缝的用户体验。

此事件也可以从 State 中的事件处理程序运行。需要 `return` `rx.redirect()`。

```python demo exec
class RedirectExampleState(rx.State):
    """The app state."""

    @rx.event
    def change_page(self):
        return rx.redirect("https://github.com/reflex-dev/reflex/", is_external=True)


def redirect_example():
    return rx.vstack(
        rx.button("Change page in State", on_click=RedirectExampleState.change_page),
    )
```

## rx.set_clipboard

将指定的文本内容设置到剪贴板。

```python demo
rx.button(
    'Copy "Hello World" to clipboard',
    on_click=rx.set_clipboard("Hello World"),
)
```

此事件允许你将给定的文本或内容复制到用户的剪贴板。
当你想在应用中提供"复制到剪贴板"功能时非常方便，
允许用户轻松复制信息以粘贴到其他地方。

## rx.set_value

设置指定引用元素的值。

```python demo
rx.hstack(
    rx.input(id="input1"),
    rx.button("Erase", on_click=rx.set_value("input1", "")),
)
```

使用此事件，你可以修改特定 HTML 元素的值，通常是输入字段或其他表单元素。

## rx.window_alert

在浏览器中创建窗口警报。

```python demo
rx.button("Alert", on_click=rx.window_alert("Hello World!"))
```

## rx.download

下载给定路径的文件。

参数：

- `url`：要下载的文件的 URL。
- `data`：要下载的数据。应为 `str` 或 `bytes`、`data:` URI、`PIL.Image` 或任何状态 Var（将转换为 JSON）。
- `filename`：下载文件的期望文件名。

```md alert
# `url` 和 `data` 参数是互斥的，并且必须至少提供其中一个。
```

```python demo
rx.button(
    "Download",
    on_click=rx.download(
        url="/reflex_banner.webp", filename="different_name_logo.webp"
    ),
    id="download button",
)
```
