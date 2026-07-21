# 集成快捷方式（Integrations Shortcut）

Reflex Build 支持强大的集成，如数据库、OpenAI 和 Databricks，允许你将外部服务连接到你的应用，而无需复杂的设置。这些集成帮助你添加高级功能——如 AI 驱动的功能、数据分析或持久存储——同时加速开发。

**添加集成（Add Integrations）**按钮使你在与 AI Builder 聊天时轻松将外部服务连接到你的应用。将出现一个包含可用集成列表的面板，你可以快速添加你的应用可以引用和连接的集成。

一旦在你的应用中，你可以通过点击聊天区域左下角的流程图或齿轮图标来访问你的集成。

```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.image(
        src=rx.color_mode_cond(
            "https://web.reflex-assets.dev/ai_builder/features/shortcut_light.webp",
            "https://web.reflex-assets.dev/ai_builder/features/shortcut_dark.webp",
        ),
        alt="Integrations shortcut in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

## 如何使用

1. 在 AI Builder 主页中，点击 **Add Integrations**（添加集成）按钮。如果你已经在应用中，点击聊天区域左下角的流程图或齿轮图标。
2. 将出现可用集成列表（例如数据库、Databricks、OpenAI 等）。
3. 点击集成旁边的添加按钮以选择它。
4. 集成将添加到你的应用并可供连接。然后你可以填写集成所需的字段。
5. AI Builder 现在知道你的应用可以访问此集成，并可以生成使用它的代码。

## 它的作用

- **快速访问** – 轻松浏览和选择可用的集成。
- **自动连接** – 集成被添加到你的应用，并可供 AI 在生成的代码中使用。
- **无需手动设置** – 跳过复杂的配置——AI Builder 为你处理集成设置。

## 常见用例

- **数据库查询**
  从我的数据库中显示按注册日期排序的前 10 名用户。

- **AI 功能**
  创建一个使用 OpenAI 生成响应的聊天应用。
