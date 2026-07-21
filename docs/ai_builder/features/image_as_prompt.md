# 使用图片作为提示词

```python exec
import reflex as rx
```

上传你想要构建的网站（Web）应用的图片（截图）可以为 AI 提供非常好的上下文。

*这是开始应用生成的推荐方式。*


下面是一张图片，展示了如何向 AI Builder 上传图片，你可以点击"Attach"（附加）按钮上传图片，拖放图片，或从剪贴板粘贴图片：

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/image_upload.avif",
        alt="Using an image as a prompt in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

建议使用的提示词是：

`Build an app from a reference image`
