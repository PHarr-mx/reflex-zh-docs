# 部署应用（Deploy App）

```python exec
import reflex as rx
```

从 Reflex Build 将应用部署到生产环境（Reflex Cloud）非常容易。

只需点击 Reflex Build 右上角的 `Deploy`（部署）按钮，如下所示：



```python exec
import reflex as rx


def render_image():
    return rx.el.div(
        rx.image(
            src="https://web.reflex-assets.dev/ai_builder/app_lifecycle/deploy_light.avif",
            alt="Deploying an app in Reflex AI Builder",
            class_name="rounded-md h-auto",
            border=f"0.81px solid {rx.color('slate', 5)}",
        ),
        class_name="w-full flex flex-col rounded-md",
    )
```

```python eval
rx.el.div(render_image())
```

部署时，你可以设置以下选项：
- **应用名称（App Name）**：你的应用名称
- **主机名（Hostname）**：通过设置主机名来设置你的 URL，例如，如果你将 `myapp` 设置为主机名，你的应用将在 `myapp.reflex.run` 访问
- **区域（Region）**：你的应用将部署的区域
- **虚拟机大小（VM Size）**：你的应用将部署的虚拟机大小
- **密钥（Secrets）**：将为你的应用设置的环境变量，你可以通过点击 `Load from settings`（从设置加载）按钮加载应用当前正在使用的变量

注意：主机名自定义、区域选择和虚拟机大小调整仅在付费计划中可用。
