```python exec
import reflex as rx
```

# 文件

除了你随应用一起提供的任何资源文件外，许多 Web 应用通常还需要接收或发送文件，无论是分享媒体、允许用户导入数据，还是导出一些后端数据。

在本节中，我们将涵盖你在 Reflex 中操作文件所需了解的所有内容。

## 资源文件与上传目录

在深入讨论文件上传和下载之前，理解 Reflex 中资源文件与上传目录之间的区别非常重要：

```python eval
# Simple table comparing assets vs upload directory
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell("Feature"),
            rx.table.column_header_cell("Assets"),
            rx.table.column_header_cell("Upload Directory"),
        ),
    ),
    rx.table.body(
        rx.table.row(
            rx.table.cell(rx.text("Purpose", font_weight="bold")),
            rx.table.cell(
                rx.text(
                    "Static files included with your app (images, stylesheets, scripts)"
                )
            ),
            rx.table.cell(rx.text("Dynamic files uploaded by users during runtime")),
        ),
        rx.table.row(
            rx.table.cell(rx.text("Location", font_weight="bold")),
            rx.table.cell(
                rx.hstack(
                    rx.code(
                        "assets/",
                        style={
                            "color": rx.color("violet", 11),
                            "border_radius": "0.25rem",
                            "border": f"1px solid {rx.color('violet', 5)}",
                            "background": rx.color("violet", 3),
                        },
                    ),
                    rx.text(" folder or next to Python files (shared assets)"),
                    spacing="2",
                )
            ),
            rx.table.cell(
                rx.hstack(
                    rx.code(
                        "uploaded_files/",
                        style={
                            "color": rx.color("violet", 11),
                            "border_radius": "0.25rem",
                            "border": f"1px solid {rx.color('violet', 5)}",
                            "background": rx.color("violet", 3),
                        },
                    ),
                    rx.text(" directory (configurable)"),
                    spacing="2",
                )
            ),
        ),
        rx.table.row(
            rx.table.cell(rx.text("Access Method", font_weight="bold")),
            rx.table.cell(
                rx.hstack(
                    rx.code(
                        "rx.asset()",
                        style={
                            "color": rx.color("violet", 11),
                            "border_radius": "0.25rem",
                            "border": f"1px solid {rx.color('violet', 5)}",
                            "background": rx.color("violet", 3),
                        },
                    ),
                    rx.text(" or direct path reference"),
                    spacing="2",
                )
            ),
            rx.table.cell(
                rx.code(
                    "rx.get_upload_url()",
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                )
            ),
        ),
        rx.table.row(
            rx.table.cell(rx.text("When to Use", font_weight="bold")),
            rx.table.cell(
                rx.text("For files that are part of your application's codebase")
            ),
            rx.table.cell(
                rx.text(
                    "For files that users upload or generate through your application"
                )
            ),
        ),
        rx.table.row(
            rx.table.cell(rx.text("Availability", font_weight="bold")),
            rx.table.cell(rx.text("Available at compile time")),
            rx.table.cell(rx.text("Available at runtime")),
        ),
    ),
    width="100%",
)
```

有关资源文件的更多信息，请参见[资源文件概述](/docs/assets/overview/)。

## 下载

如果你想让应用的用户从你的服务器下载文件到他们的计算机，Reflex 提供了两种方式。

### 使用普通链接

对于一些基本用法，只需在 `rx.link` 中提供资源路径即可，点击链接将会下载或显示该资源。

```python demo
rx.link("Download", href="/reflex_banner.webp")
```

### 使用 `rx.download` 事件

使用 `rx.download` 事件总会提示浏览器下载文件，即使该文件可以在浏览器中显示。

`rx.download` 事件还允许从另一个后端事件处理器触发下载。

```python demo
rx.button(
    "Download",
    on_click=rx.download(url="/reflex_banner.webp"),
)
```

`rx.download` 允许你为要下载的文件指定一个名称，如果你希望它不同于服务器端的名称。

```python demo
rx.button(
    "Download and Rename",
    on_click=rx.download(url="/reflex_banner.webp", filename="different_name_logo.png"),
)
```

如果要下载的数据还没有在一个已知的 URL 上可用，可以从后端将 `data` 直接传递给 `rx.download` 事件。

```python demo exec
import random


class DownloadState(rx.State):
    @rx.event
    def download_random_data(self):
        return rx.download(
            data=",".join([str(random.randint(0, 100)) for _ in range(10)]),
            filename="random_numbers.csv",
        )


def download_random_data_button():
    return rx.button(
        "Download random numbers", on_click=DownloadState.download_random_data
    )
```

`data` 参数接受 `str` 或 `bytes` 数据、`data:` URI、`PIL.Image` 或任何状态 Var。如果 Var 不是字符串，它将使用 `JSON.stringify` 转换为字符串。这使得复杂的状态结构可以作为 JSON 下载提供。

`rx.download` 的参考页面在[此处](/docs/api-reference/special-events#rx.download)。

## 上传

将文件上传到你的服务器让用户能够以不同于仅仅填写表单来提供数据的方式与你的应用交互。

`rx.upload` 组件让用户可以将文件上传到服务器。

以下是一个基本的使用示例：

```python
def index():
    return rx.fragment(
        rx.upload(rx.text("Upload files"), rx.icon(tag="upload")),
        rx.button(on_submit=State.<your_upload_handler>)
    )
```

详细信息请参见该组件的参考页面[此处](/docs/library/forms/upload)。
