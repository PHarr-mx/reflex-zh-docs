# 图片（Images）

## 上传图片作为 AI Builder 的上下文

要上传一张图片到 AI Builder，该图片将用作 AI 构建器的上下文以构建与图片相似的应用，点击 `📎 Attach`（附加）按钮并从你的计算机中选择要上传的文件。你也可以直接将文件拖放到聊天窗口中。

```md alert
## 支持的图片类型
AI Builder 目前支持以下图片类型用于上传和处理：
1. `.png`
2. `.jpg`
3. `.jpeg`
4. `.webp`
5. `.gif`
6. `.svg`
7. `.bmp`
8. `.tiff`
9. `.tif`
10. `.ico`
```


## 上传图片以在应用中使用

如果你想上传一张图片以在应用中使用，例如公司徽标，那么你可以在 `code`（代码）选项卡中手动将其上传到 `assets/` 文件夹。将图片拖放到 `assets/` 文件夹中。

```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/add_images_to_assets.avif",
        alt="Uploading an image to app assets in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```


目前不支持视频上传，但即将推出！
