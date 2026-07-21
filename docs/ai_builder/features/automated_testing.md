# AI 测试功能

## 概述

测试功能允许你自动测试生成的应用，以发现常见问题和功能问题。AI 将分析你的应用并识别潜在的 bug、损坏的链接、导航问题和其他问题。


```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.image(
        src=rx.color_mode_cond(
            "https://web.reflex-assets.dev/ai_builder/features/test_light.webp",
            "https://web.reflex-assets.dev/ai_builder/features/test_dark.webp",
        ),
        alt="Automated testing in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

## 如何使用

1. **开始测试**：输入 "test this app" 或类似命令以激活测试模式
2. **AI 分析**：AI 将自动切换到测试模式并开始分析你的应用
3. **查看结果**：预览选项卡切换到"测试"模式，显示测试过程和结果

## 测试内容

AI 会自动检查：

- **损坏的导航**：无法工作或指向缺失页面的链接
- **无功能的按钮**：不响应或触发错误的按钮
- **损坏的链接**：返回错误的外部或内部链接
- **UI/UX 问题**：未按预期运行的界面元素
- **数据流问题**：表单、输入和数据处理的问题
- **布局问题**：界面的视觉或结构问题

## 测试界面

当测试激活时：
- 预览选项卡更改为"测试"模式
- 你可以实时看到 AI 与你的应用交互
- 问题和结果在发现时报告
- 测试过程是可视化和交互式的

## 优势

- **质量保证**：在部署前发现问题
- **节省时间**：自动化测试比手动检查更快
- **全面覆盖**：测试应用的多个方面
