# 共享应用（Share App）

**共享**功能让你无需部署即可轻松向他人展示你的应用。
当你共享时，Reflex Build 会生成一个指向构建器中项目当前版本的唯一链接。

```python exec
import reflex as rx


def render_image():
    return rx.el.div(
        rx.image(
            src="https://web.reflex-assets.dev/ai_builder/app_lifecycle/share_light.avif",
            alt="Sharing an app in Reflex AI Builder",
            class_name="rounded-md h-auto",
            border=f"0.81px solid {rx.color('slate', 5)}",
        ),
        class_name="w-full flex flex-col rounded-md",
    )
```

```python eval
rx.el.div(render_image())
```

## 如何共享

1. 在 AI Builder 工作区中，点击部署按钮旁边的向下箭头图标，然后点击 **Share**（共享）按钮。
2. 将弹出一个包含**可共享链接**的窗口。
3. 复制链接并发送给队友、协作者或利益相关者。


## 他人看到什么

- 链接将打开你应用生成的**只读视图**。
- 接收者可以查看应用预览，但无法进行编辑。
- 这使得共享进行中的版本以获取快速反馈变得安全。


## 常见用例

- **快速获取反馈**
  在部署之前与团队共享进行中的版本。

- **演示功能**
  发送链接以展示新组件、布局或集成。

- **协作**
  在移交给 GitHub 或下载之前与另一位开发者共享上下文。
