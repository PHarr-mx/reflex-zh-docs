# 下载应用（Download App）

如果你想在本地工作或想在 AI Builder 之外自托管，可以下载你的 Reflex Build 项目。

**提示：** 推荐的工作流是使用 GitHub 集成，它可以保持代码版本控制并同步。如果 GitHub 集成不可用，或者你只想进行一次性的导出，下载会很有用。


```python exec
import reflex as rx


def render_image():
    return rx.el.div(
        rx.image(
            src="https://web.reflex-assets.dev/ai_builder/app_lifecycle/download_light.avif",
            alt="Downloading an app in Reflex AI Builder",
            class_name="rounded-md h-auto",
            border=f"0.81px solid {rx.color('slate', 5)}",
        ),
        class_name="w-full flex flex-col rounded-md",
    )
```

```python eval
rx.el.div(render_image())
```

## 如何下载

1. 在 AI Builder 工作区中，点击部署按钮旁边的向下箭头图标，然后点击 **Download**（下载）按钮。你也可以在设置（Settings）选项卡中执行此操作。
2. 将生成一个 `.zip` 文件，包含你的整个 Reflex 应用，包括：
   - 源代码（`.py` 文件、组件、状态等）
   - 包含依赖项的 `requirements.txt`
   - 配置文件（`rxconfig.py`、`.env` 等）
