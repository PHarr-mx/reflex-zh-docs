---
components:
  - rx.clipboard
---

```python exec
import reflex as rx
```

# 剪贴板（Clipboard）

_0.5.6 版本新增_

剪贴板组件（Clipboard）可用于响应包含复杂数据的粘贴事件。

如果 Clipboard 组件在页面中不包含子元素（即 `rx.clipboard()`），它将附加到文档的 `paste` 事件处理器上，当数据被粘贴到页面的任何位置时都会触发。

```python demo exec
class ClipboardPasteState(rx.State):
    @rx.event
    def on_paste(self, data: list[tuple[str, str]]):
        for mime_type, item in data:
            yield rx.toast(f"Pasted {mime_type} data: {item}")


def clipboard_example():
    return rx.fragment(
        rx.clipboard(on_paste=ClipboardPasteState.on_paste),
        "Paste Content Here",
    )
```

传递给 `on_paste` 方法的 `data` 参数是一个元组列表，每个元组包含粘贴数据的 MIME 类型和数据本身。二进制数据将被 base64 编码为 data URI，可以使用 Python 的 `urlopen` 进行解码，也可以直接用作图片的 `src` 属性。

## 限定范围的粘贴事件

如果你想将粘贴事件的范围限定在特定元素内，可以将 `rx.clipboard` 组件包裹在应触发粘贴事件的元素外面。

为了避免外层的粘贴处理器也触发事件，你可以使用事件操作 `.stop_propagation` 来阻止粘贴事件在 DOM 中冒泡。

如果你还需要阻止将数据粘贴到文本框的默认行为，也可以附加 `.prevent_default` 操作。

```python demo exec
class ClipboardPasteImageState(rx.State):
    last_image_uri: str = ""

    def on_paste(self, data: list[tuple[str, str]]):
        for mime_type, item in data:
            if mime_type.startswith("image/"):
                self.last_image_uri = item
                break
        else:
            return rx.toast("Did not find an image in the pasted data")


def clipboard_image_example():
    return rx.vstack(
        rx.clipboard(
            rx.input(placeholder="Paste Image (stop propagation)"),
            on_paste=ClipboardPasteImageState.on_paste.stop_propagation,
        ),
        rx.clipboard(
            rx.input(placeholder="Paste Image (prevent default)"),
            on_paste=ClipboardPasteImageState.on_paste.prevent_default,
        ),
        rx.image(
            src=ClipboardPasteImageState.last_image_uri,
            alt="Image pasted from clipboard",
        ),
    )
```
