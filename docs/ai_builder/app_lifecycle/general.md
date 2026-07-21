# 常规应用设置（General App Settings）

**常规应用设置**部分让你管理应用的关键方面，包括其名称、ID 和删除。这是查看和更新应用核心信息的中心位置。


```python exec
import reflex as rx


def render_image():
    return rx.el.div(
        rx.image(
            src="https://web.reflex-assets.dev/ai_builder/app_lifecycle/general_light.avif",
            alt="General app settings in Reflex AI Builder",
            class_name="rounded-md h-auto",
            border=f"0.81px solid {rx.color('slate', 5)}",
        ),
        class_name="w-full flex flex-col rounded-md",
    )
```

```python eval
rx.el.div(render_image())
```


## 如何访问设置

1. 在 AI Builder 工作区中，在顶部栏点击更多（三个点）图标，然后点击 **Settings**（设置）选项卡。
2. 这将打开 **Settings**（设置）选项卡，查看应用的主要设置。

## 你可以做什么

- **更改应用名称**
  更新应用的名称以反映其目的或版本。

- **更改应用可见性**
  将应用的可见性更新为公开或私有。

- **查看应用 ID**
  查找应用的唯一标识符，可用于集成或支持。

- **分叉应用**
  分叉你的应用以创建其副本。

-- **下载应用**
  将应用下载到你的本地机器。

- **删除应用**
  永久删除你不再需要的应用。**警告：** 此操作无法撤消。
