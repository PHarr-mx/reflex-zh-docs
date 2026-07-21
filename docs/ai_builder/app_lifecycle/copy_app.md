# 复制应用（Copy App）

**复制**功能让你可以在 Reflex Build 中复制现有应用。
当你想在不影响原始项目的情况下尝试更改，或者想将应用作为新想法的起点时，这非常有用。


```python exec
import reflex as rx


def render_image():
    return rx.el.div(
        rx.image(
            src="https://web.reflex-assets.dev/ai_builder/app_lifecycle/copy_light.avif",
            alt="Copying an app in Reflex AI Builder",
            class_name="rounded-md h-auto",
            border=f"0.81px solid {rx.color('slate', 5)}",
        ),
        class_name="w-full flex flex-col rounded-md",
    )
```

```python eval
rx.el.div(render_image())
```

## 如何复制应用

1. 在 Reflex Build 工作区中，点击部署按钮旁边的向下箭头图标，然后点击 **Copy**（复制）按钮。你也可以在设置（Settings）选项卡中执行此操作。
2. Reflex Build 将在你的工作区中创建一个新应用，具有相同的：
   - 代码文件和组件
   - 状态和配置
   - 依赖项

复制的应用将显示为一个独立的项目，与原始项目分离。


## 常见用例

- **安全实验**
  尝试新组件、布局或集成，而不会影响你正在工作的应用。

- **创建变体**
  使用原始应用作为基础，快速创建不同版本（例如，浅色和深色主题版本）。

- **模板复用**
  将应用变成个人模板，每次开始新项目时复制它。

## 最佳实践

- 立即重命名复制的应用，以便轻松将其与原始应用区分开来。
