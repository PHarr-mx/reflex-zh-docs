# 模板（Templates）

Reflex 拥有许多经过认证的模板，可以在 Reflex Build 的 `Trending`（热门）选项卡中看到，可用于快速启动你的应用。

```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/overview/templates_light.avif",
        alt="Reflex AI Builder templates gallery",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

## 使用模板

要使用模板，只需点击该模板，然后在应用右下角点击 `Fork`（分叉）按钮。这将在你自己的账户中创建该模板的副本。然后你可以根据需要进一步提示来编辑应用。

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/overview/fork_template_light.avif",
        alt="Forking a template in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

如果模板的 UI 与你想构建的内容相似，那么模板是快速入门的好方法。然后你可以向应用添加自己的数据。
