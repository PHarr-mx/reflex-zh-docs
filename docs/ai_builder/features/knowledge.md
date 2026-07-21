# 知识（Knowledge）

**知识（Knowledge）**功能让你添加 AI Builder 在生成应用时可以参考的上下文或规则。这确保你的应用遵循你的指南、标准或特定的业务逻辑。

```python exec
import reflex as rx


def render_image():
    return rx.el.div(
        rx.image(
            src="https://web.reflex-assets.dev/ai_builder/features/knowledge_light.avif",
            alt="Reflex AI Builder knowledge section",
            class_name="rounded-md h-auto",
            border=f"0.81px solid {rx.color('slate', 5)}",
        ),
        class_name="w-full flex flex-col rounded-md",
    )
```

```python eval
rx.el.div(render_image())
```

## 如何添加知识

1. 在 AI Builder 顶部栏中，点击更多（三个点）图标，然后点击 **`Knowledge`**（知识）选项卡。
2. 输入你的规则、指南或上下文描述。
3. 保存条目。AI Builder 将在生成应用时自动使用它。

## 工作原理

- AI Builder 将你的知识条目作为规则或指南参考。
- 规则可以定义命名约定、组件使用、布局偏好或其他自定义逻辑。
- 可以添加多个规则以涵盖应用生成的不同方面。

## 常见用例

- **保持一致性**
  确保所有生成的应用遵循你公司的设计或命名标准。

- **强制业务逻辑**
  引导 AI Builder 遵循特定的工作流、验证或功能要求。

- **快速调整 AI 行为**
  添加或更新规则以影响新的应用生成，而无需手动编辑。
