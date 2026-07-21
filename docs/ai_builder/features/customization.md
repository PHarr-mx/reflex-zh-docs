# 应用样式自定义

## 概述

应用样式功能允许你自定义 AI 生成应用的视觉外观。你可以从预定义的设计主题中选择，或创建自定义样式以匹配你的品牌和偏好。

## 如何使用

1. **访问功能**：点击应用样式（App Style）选项以打开自定义面板
2. **选择你的方式**：
   - **自定义（Custom）**：手动配置各个设计元素
   - **主题（Themes）**：从专业设计的模板中选择

## 自定义样式选项

```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.image(
        src=rx.color_mode_cond(
            "https://web.reflex-assets.dev/ai_builder/features/style_light.webp",
            "https://web.reflex-assets.dev/ai_builder/features/style_dark.webp",
        ),
        alt="Custom styling options in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

使用自定义模式时，你可以调整：

- **主色（Primary Color）**：从 20 多个预设选项中选择你的主要品牌颜色
- **辅助色（Secondary Color）**：选择用于强调的互补颜色
- **排版（Typography）**：为你的应用选择字体系列
- **边框圆角（Border Radius）**：控制圆角的外观
- **阴影（Shadows）**：使用阴影效果添加深度
- **间距（Spacing）**：调整元素之间的间距

## 主题

这些选项是你可以为应用选择的预定义流行主题。以下是你可以选择的一些可用主题

```python eval
rx.el.div(
    rx.image(
        src=rx.color_mode_cond(
            "https://web.reflex-assets.dev/ai_builder/features/theme_light.webp",
            "https://web.reflex-assets.dev/ai_builder/features/theme_dark.webp",
        ),
        alt="Theme customization in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

- **极简设计（Minimal Design）**：干净、几何化的设计，无阴影或渐变
- **现代 UI（Modern UI）**：时尚、现代的界面，针对性能优化
- **Carbon 设计（Carbon Design）**：遵循 IBM Carbon 系统的企业级设计
- **Material 设计（Material Design）**：Google 的设计语言，具有层次和语义颜色
