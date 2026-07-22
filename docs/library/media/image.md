---
components:
  - rx.image
---

```python exec
import reflex as rx
```

# 图片（Image）

图片组件（Image）可以通过传入 `src` 路径作为参数来显示图片。该路径可以是 assets 文件夹中的本地路径，也可以是外部链接。

```python demo
rx.image(
    src="https://web.reflex-assets.dev/other/logo.jpg",
    alt="Reflex logo",
    width="100px",
    height="auto",
)
```

Image 组合了一个 box，可以用类似的方式进行样式设置。

```python demo
rx.image(
    src="https://web.reflex-assets.dev/other/logo.jpg",
    alt="Reflex logo",
    width="100px",
    height="auto",
    border_radius="15px 50px",
    border="5px solid #555",
)
```

你也可以将 `PIL` 图片对象作为 `src` 传入。

```python demo box
rx.image(src="https://picsum.photos/id/1/200/300", alt="An Unsplash Image")
```

```python
from PIL import Image
import requests


class ImageState(rx.State):
    url: str = "https://picsum.photos/id/1/200/300"
    image: Image.Image = Image.open(requests.get(url, stream=True).raw)


def image_pil_example():
    return rx.vstack(rx.image(src=ImageState.image, alt="Dynamically generated image"))
```

```md alert info
# rx.image 仅接受 URL 和 Pillow 图片

cv2 图片必须先转换为 PIL 图片，才能作为 State 变量直接传递给 `rx.image`，或者保存到 `assets` 文件夹后再传递给 `rx.image` 组件。
```

```md alert info
# 如何让用户上传图片

要让用户向你的应用上传图片，请查看[上传文档](/docs/library/forms/upload)。
```
