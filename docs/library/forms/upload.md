---
components:
  - rx.upload
  - rx.upload.root
---

```python exec
import reflex as rx
```

# 文件上传（File Upload）

Reflex 让添加文件上传功能变得简单。你可以让用户选择文件、将文件存储在服务器上，并根据需要显示或处理它们。下面是一个最小示例，演示如何上传文件、将其保存到磁盘，以及使用应用状态显示已上传的图片。

## 基本文件上传示例

你可以让用户上传文件并在应用状态中跟踪它们。下面的示例允许用户上传文件，使用后端保存它们，然后将上传的文件显示为图片。

```python
import reflex as rx


class State(rx.State):
    uploaded_files: list[str] = []

    @rx.event
    async def handle_upload(self, files: list[rx.UploadFile]):
        for file in files:
            data = await file.read()
            path = rx.get_upload_dir() / file.name
            with path.open("wb") as f:
                f.write(data)
            self.uploaded_files.append(file.name)


def upload_component():
    return rx.vstack(
        rx.upload(id="upload"),
        rx.button("Upload", on_click=State.handle_upload(rx.upload_files("upload"))),
        rx.foreach(
            State.uploaded_files,
            lambda f: rx.image(src=rx.get_upload_url(f), alt="Uploaded file preview"),
        ),
    )
```

## 文件上传的工作原理

选择文件会将其添加到浏览器文件列表中，可以使用 `rx.selected_files(id)` 特殊 Var 在前端渲染。要清除已选择的文件，可以使用另一个特殊 Var `rx.clear_selected_files(id)` 作为事件处理函数。

要上传文件，绑定一个事件处理函数并传入以下特殊事件参数之一：

- `rx.upload_files(upload_id=id)` 用于上传
- `rx.upload_files_chunk(upload_id=id)` 用于需要增量处理的较大文件

`id` 将上传组件与这些特殊 Var 和事件参数关联起来，因此它们必须引用相同的值。将其作为字符串字面量或模块级常量传入——状态变量不能用作上传 `id`。

## 文件存储函数

Reflex 提供了两个处理上传文件的关键函数：

### rx.get_upload_dir()

- **用途**：返回一个指向服务器端目录的 `Path` 对象，上传的文件应保存在该目录中
- **用法**：在后端事件处理函数中使用，确定上传文件的保存位置
- **默认位置**：`./uploaded_files`（可通过 `REFLEX_UPLOADED_FILES_DIR` 环境变量自定义）
- **类型**：返回 `pathlib.Path`

### rx.get_upload_url(filename)

- **用途**：返回可在前端组件中用于访问上传文件的 URL
- **用法**：在前端组件（如 `rx.image`、`rx.video`）中使用，显示上传的文件
- **URL 格式**：`/_upload/filename`
- **类型**：返回前端 `Var`（一个 JavaScript 表达式），而不是 Python `str`

因为它返回的是前端 Var，所以 `rx.get_upload_url` 仅用于前端：在组件代码和事件触发器中使用它，而不是在后端事件处理函数或计算变量中使用，因为在那些地方该值不会解析为可用的字符串。如果你确实需要在后端以纯字符串形式获取上传 URL，可以从配置的上传端点构建——`Endpoint.UPLOAD.get_url()`（来自 `reflex.constants`）与文件名拼接——这会考虑协议、任何 `backend_path` 前缀以及前后端分离部署的情况。

### 主要区别

- **rx.get_upload_dir()** -> 用于保存文件的后端文件路径
- **rx.get_upload_url()** -> 用于显示文件的前端 URL

### 基本上传模式

以下是处理文件上传的标准模式：

```python
import reflex as rx


def create_unique_filename(file_name: str):
    import random
    import string

    filename = "".join(random.choices(string.ascii_letters + string.digits, k=10))
    return filename + "_" + file_name


class State(rx.State):
    uploaded_files: list[str] = []

    @rx.event
    async def handle_upload(self, files: list[rx.UploadFile]):
        """处理文件上传并进行适当的目录管理。"""
        for file in files:
            # 读取文件数据
            upload_data = await file.read()

            # 获取上传目录（后端路径）
            upload_dir = rx.get_upload_dir()

            # 确保目录存在
            upload_dir.mkdir(parents=True, exist_ok=True)

            # 创建唯一文件名以防止冲突
            unique_filename = create_unique_filename(file.name)

            # 创建完整文件路径
            file_path = upload_dir / unique_filename

            # 保存文件
            with file_path.open("wb") as f:
                f.write(upload_data)

            # 存储文件名用于前端显示
            self.uploaded_files.append(unique_filename)


def upload_component():
    return rx.vstack(
        rx.upload(
            rx.text("Drop files here or click to select"),
            id="file_upload",
            border="2px dashed #ccc",
            padding="2em",
        ),
        rx.button(
            "Upload Files",
            on_click=State.handle_upload(rx.upload_files(upload_id="file_upload")),
        ),
        # 使用 rx.get_upload_url() 显示上传的文件
        rx.foreach(
            State.uploaded_files,
            lambda filename: rx.image(
                src=rx.get_upload_url(filename), alt="Uploaded file preview"
            ),
        ),
    )
```

### 多文件上传

下面是一个允许多文件上传的示例（本例中为图片）。

```python demo box
rx.image(
    src="https://web.reflex-assets.dev/other/upload.webp",
    alt="Multiple file upload component example",
)
```

```python
class State(rx.State):
    """应用状态。"""

    # 要显示的图片。
    img: list[str]

    @rx.event
    async def handle_upload(self, files: list[rx.UploadFile]):
        """处理文件上传。

        Args:
            files: 上传的文件。
        """
        for file in files:
            upload_data = await file.read()
            outfile = rx.get_upload_dir() / file.name

            # 保存文件。
            with outfile.open("wb") as file_object:
                file_object.write(upload_data)

            # 更新 img 变量。
            self.img.append(file.name)


color = "rgb(107,99,246)"


def index():
    """主视图。"""
    return rx.vstack(
        rx.upload(
            rx.vstack(
                rx.button(
                    "Select File", color=color, bg="white", border=f"1px solid {color}"
                ),
                rx.text("Drag and drop files here or click to select files"),
            ),
            id="upload1",
            border=f"1px dotted {color}",
            padding="5em",
        ),
        rx.hstack(rx.foreach(rx.selected_files("upload1"), rx.text)),
        rx.button(
            "Upload",
            on_click=State.handle_upload(rx.upload_files(upload_id="upload1")),
        ),
        rx.button(
            "Clear",
            on_click=rx.clear_selected_files("upload1"),
        ),
        rx.foreach(
            State.img,
            lambda img: rx.image(
                src=rx.get_upload_url(img), alt="Uploaded file preview"
            ),
        ),
        padding="5em",
    )
```

### 上传单个文件（视频）

下面是一个只允许上传单个文件并渲染的示例（本例中为视频）。

```python demo box
rx.el.video(
    src="https://web.reflex-assets.dev/other/upload_single_video.webm",
    auto_play=True,
    controls=True,
    loop=True,
)
```

```python
class State(rx.State):
    """应用状态。"""

    # 要显示的视频。
    video: str

    @rx.event
    async def handle_upload(self, files: list[rx.UploadFile]):
        """处理文件上传。

        Args:
            files: 上传的文件。
        """
        current_file = files[0]
        upload_data = await current_file.read()
        outfile = rx.get_upload_dir() / current_file.name

        # 保存文件。
        with outfile.open("wb") as file_object:
            file_object.write(upload_data)

        # 更新 video 变量。
        self.video = current_file.name


color = "rgb(107,99,246)"


def index():
    """主视图。"""
    return rx.vstack(
        rx.upload(
            rx.vstack(
                rx.button(
                    "Select File",
                    color=color,
                    bg="white",
                    border=f"1px solid {color}",
                ),
                rx.text("Drag and drop files here or click to select files"),
            ),
            id="upload1",
            max_files=1,
            border=f"1px dotted {color}",
            padding="5em",
        ),
        rx.text(rx.selected_files("upload1")),
        rx.button(
            "Upload",
            on_click=State.handle_upload(rx.upload_files(upload_id="upload1")),
        ),
        rx.button(
            "Clear",
            on_click=rx.clear_selected_files("upload1"),
        ),
        rx.cond(
            State.video,
            rx.video(src=rx.get_upload_url(State.video)),
        ),
        padding="5em",
    )
```

### 自定义上传

在下面的示例中，上传组件接受最多 5 个特定类型的文件。它还禁用了使用空格键或回车键上传文件。

要使用一步上传，将事件处理函数绑定到 `rx.upload` 组件的 `on_drop` 触发器。

```python
class State(rx.State):
    """应用状态。"""

    # 要显示的图片。
    img: list[str]

    async def handle_upload(self, files: list[rx.UploadFile]):
        """处理文件上传。

        Args:
            files: 上传的文件。
        """
        for file in files:
            upload_data = await file.read()
            outfile = rx.get_upload_dir() / file.name

            # 保存文件。
            with outfile.open("wb") as file_object:
                file_object.write(upload_data)

            # 更新 img 变量。
            self.img.append(file.name)


color = "rgb(107,99,246)"


def index():
    """主视图。"""
    return rx.vstack(
        rx.upload(
            rx.vstack(
                rx.button(
                    "Select File", color=color, bg="white", border=f"1px solid {color}"
                ),
                rx.text("Drag and drop files here or click to select files"),
            ),
            id="upload2",
            multiple=True,
            accept={
                "application/pdf": [".pdf"],
                "image/png": [".png"],
                "image/jpeg": [".jpg", ".jpeg"],
                "image/gif": [".gif"],
                "image/webp": [".webp"],
                "text/html": [".html", ".htm"],
            },
            max_files=5,
            disabled=False,
            no_keyboard=True,
            on_drop=State.handle_upload(rx.upload_files(upload_id="upload2")),
            border=f"1px dotted {color}",
            padding="5em",
        ),
        rx.grid(
            rx.foreach(
                State.img,
                lambda img: rx.vstack(
                    rx.image(src=rx.get_upload_url(img), alt="Uploaded file preview"),
                    rx.text(img),
                ),
            ),
            columns="2",
            spacing="1",
        ),
        padding="5em",
    )
```

### 无样式上传组件

要使用完全无样式的上传组件并应用自己的自定义样式，请改用 `rx.upload.root`：

```python
rx.upload.root(
    rx.box(
        rx.icon(
            tag="cloud_upload",
            style={
                "width": "3rem",
                "height": "3rem",
                "color": "#2563eb",
                "marginBottom": "0.75rem",
            },
        ),
        rx.hstack(
            rx.text(
                "Click to upload",
                style={"fontWeight": "bold", "color": "#1d4ed8"},
            ),
            " or drag and drop",
            style={"fontSize": "0.875rem", "color": "#4b5563"},
        ),
        rx.text(
            "SVG, PNG, JPG or GIF (MAX. 5MB)",
            style={"fontSize": "0.75rem", "color": "#6b7280", "marginTop": "0.25rem"},
        ),
        style={
            "display": "flex",
            "flexDirection": "column",
            "alignItems": "center",
            "justifyContent": "center",
            "padding": "1.5rem",
            "textAlign": "center",
        },
    ),
    id="my_upload",
    style={
        "maxWidth": "24rem",
        "height": "16rem",
        "borderWidth": "2px",
        "borderStyle": "dashed",
        "borderColor": "#60a5fa",
        "borderRadius": "0.75rem",
        "cursor": "pointer",
        "transitionProperty": "background-color",
        "transitionDuration": "0.2s",
        "transitionTimingFunction": "ease-in-out",
        "display": "flex",
        "alignItems": "center",
        "justifyContent": "center",
        "boxShadow": "0 1px 2px rgba(0, 0, 0, 0.05)",
    },
)
```

## 处理上传

对于上传，你的事件处理函数应该是一个异步函数，接受单个参数 `files: list[UploadFile]`，其中包含 [Starlette UploadFile](https://www.starlette.io/requests/#request-files) 实例。每个文件通过 `file.name` 暴露其原始文件名。你可以读取文件并将它们保存到任何位置，如示例所示。

```md alert warning
# 上传处理函数是普通的事件处理函数。
不要使用 `@rx.event(background=True)` 声明标准上传处理函数，也不要在其中使用 `async with self`——这些仅适用于分块上传处理函数（见下文）。标准处理函数是一个普通的 `@rx.event` 异步函数。
```

在你的 UI 中，你可以将事件处理函数绑定到触发器，例如按钮的 `on_click` 事件或上传的 `on_drop` 事件，并使用 `rx.upload_files()` 传入文件。

```md alert warning
# 将上传触发按钮放在上传组件外部。
拖放区域会将 `rx.upload` / `rx.upload.root` 内部的任何点击视为打开文件选择器的请求。如果触发上传事件的按钮放在内部，每次点击都会同时打开选择器并开始上传。拖放区域内部的按钮（如上面示例中的 "Select File"）应该纯粹是视觉性的。
```

### 从表单上传

表单的 `on_submit` 事件永远不会包含上传的文件数据。当上传组件位于表单内部时，使用带有 `type="button"` 的单独按钮（或从拖放区域的 `on_drop` 触发器）通过 `rx.upload_files()` 触发上传，并在 `on_submit` 中像往常一样处理其余表单字段。

### 保存文件

按照惯例，Reflex 提供了 `rx.get_upload_dir()` 函数来获取上传文件可以保存的目录。上传目录来自环境变量 `REFLEX_UPLOADED_FILES_DIR`，如果未指定则为 `./uploaded_files`。

你的应用后端会将此上传文件目录挂载到 `/_upload` 上，没有任何限制。通过此机制上传的任何文件都会自动公开访问。要获取上传目录中文件的 URL，在前端组件中使用 `rx.get_upload_url(filename)` 函数。

```md alert info
# 使用 Reflex 托管服务时，上传文件目录不是持久化的，每次部署时都会被清除。对于上传文件的持久存储，建议使用外部服务，如 S3。
```

### 目录结构和 URL

默认情况下，Reflex 创建以下结构：

```text
your_project/
├── uploaded_files/          # rx.get_upload_dir() 指向此处
│   ├── image1.png
│   ├── document.pdf
│   └── video.mp4
└── ...
```

文件自动在以下路径提供服务：

- `/_upload/image1.png` ← `rx.get_upload_url("image1.png")`
- `/_upload/document.pdf` ← `rx.get_upload_url("document.pdf")`
- `/_upload/video.mp4` ← `rx.get_upload_url("video.mp4")`

这些路径是实现细节——避免在你的应用中硬编码 `/upload/` 或 `/_upload/`。通过 `rx.get_upload_url(filename)` 获取 URL，这样当前端和后端从不同的主机或端口提供服务时，URL 仍然正确。

### 大文件的分块上传

当文件可能很大或你希望后端增量写入数据时，使用 `rx.upload_files_chunk(...)`。标准上传在处理函数启动前会将文件假脱机到磁盘，但在处理函数中调用 `await file.read()` 会一次性将整个文件加载到内存中，这可能导致大文件的高内存消耗。

分块上传处理函数：

- 必须使用 `@rx.event(background=True)` 声明
- 必须接受 `chunk_iter: rx.UploadChunkIterator`
- 必须完全消费 `chunk_iter`

要在你自己的应用中使用分块上传：

1. 创建一个 `@rx.event(background=True)` 处理函数。
2. 接受 `chunk_iter: rx.UploadChunkIterator`。
3. 遍历块并在 `chunk.offset` 处写入 `chunk.data`。
4. 使用 `rx.upload_files_chunk(upload_id=...)` 触发处理函数。

每个块包含：

- `chunk.filename`
- `chunk.offset`
- `chunk.content_type`
- `chunk.data`

```python
class ChunkUploadState(rx.State):
    uploaded_files: list[str] = []
    status: str = "No chunked upload has finished yet."

    @rx.event(background=True)
    async def handle_large_upload(self, chunk_iter: rx.UploadChunkIterator):
        file_handles = {}
        destinations = {}

        try:
            async with self:
                self.status = "Streaming upload in progress."
            async for chunk in chunk_iter:
                path = destinations.setdefault(
                    chunk.filename,
                    rx.get_upload_dir() / "stream" / chunk.filename,
                )
                path.parent.mkdir(parents=True, exist_ok=True)

                fh = file_handles.get(chunk.filename)
                if fh is None:
                    fh = path.open("wb")
                    file_handles[chunk.filename] = fh

                fh.seek(chunk.offset)
                fh.write(chunk.data)
        finally:
            for fh in file_handles.values():
                fh.close()

        async with self:
            self.uploaded_files = sorted(destinations)
            self.status = "Chunked upload complete."


def chunked_upload_component():
    return rx.vstack(
        rx.upload(
            rx.text("Drop files here or click to select"),
            id="large_upload",
            border="2px dashed #ccc",
            padding="2em",
        ),
        rx.button(
            "Upload Large Files",
            on_click=ChunkUploadState.handle_large_upload(
                rx.upload_files_chunk(upload_id="large_upload")
            ),
        ),
        rx.text(ChunkUploadState.status),
        rx.foreach(
            ChunkUploadState.uploaded_files,
            lambda filename: rx.text(filename),
        ),
    )
```

从处理函数提前返回会导致上传失败，因为剩余的块没有被消费。

如果你想要进度条或取消按钮，`rx.upload_files_chunk(...)` 支持与上传相同的 `on_upload_progress` 回调，你可以使用 `rx.cancel_upload(upload_id)` 停止上传。

## 取消上传

提供给 `rx.upload` 组件的 `id` 可以传递给特殊事件处理函数 `rx.cancel_upload(id)` 来按需停止上传。取消可以由前端事件触发器直接触发，也可以从后端事件处理函数返回。

## 进度

`rx.upload_files` 和 `rx.upload_files_chunk` 都接受一个 `on_upload_progress` 事件触发器，该触发器会在上传操作期间触发以报告上传进度。这可以用于更新进度条或其他 UI 元素，向用户显示上传进度。

```python
class UploadExample(rx.State):
    uploading: bool = False
    progress: int = 0
    total_bytes: int = 0

    @rx.event
    async def handle_upload(self, files: list[rx.UploadFile]):
        for file in files:
            self.total_bytes += len(await file.read())

    @rx.event
    def handle_upload_progress(self, progress: dict):
        self.uploading = True
        self.progress = round(progress["progress"] * 100)
        if self.progress >= 100:
            self.uploading = False

    @rx.event
    def cancel_upload(self):
        self.uploading = False
        return rx.cancel_upload("upload3")


def upload_form():
    return rx.vstack(
        rx.upload(
            rx.text("Drag and drop files here or click to select files"),
            id="upload3",
            border="1px dotted rgb(107,99,246)",
            padding="5em",
        ),
        rx.vstack(rx.foreach(rx.selected_files("upload3"), rx.text)),
        rx.progress(value=UploadExample.progress, max=100),
        rx.cond(
            ~UploadExample.uploading,
            rx.button(
                "Upload",
                on_click=UploadExample.handle_upload(
                    rx.upload_files(
                        upload_id="upload3",
                        on_upload_progress=UploadExample.handle_upload_progress,
                    ),
                ),
            ),
            rx.button("Cancel", on_click=UploadExample.cancel_upload),
        ),
        rx.text("Total bytes uploaded: ", UploadExample.total_bytes),
        align="center",
    )
```

`progress` 字典包含以下键：

```python
{
    "loaded": 36044800,
    "total": 54361908,
    "progress": 0.6630525183185255,
}
```
