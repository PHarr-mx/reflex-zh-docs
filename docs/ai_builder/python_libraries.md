# Python 库（Python Libraries）


并非每个服务或工具都有一流的集成——但你的应用仍然可以直接连接到**任何 Python 库**。通过利用内置的 Python 运行时，AI Builder 可以安装包、导入库，并从工作流或组件中调用其函数。这为你提供了使用更广泛的 Python 生态系统扩展应用的最大灵活性。

当你要求某些功能时，AI Builder 会首先检查是否有`一流的集成`可用。如果没有，它将`搜索网络`以尝试找到相关的 Python 库来满足你的请求。如果找到，它将安装该包并要求你设置任何所需的 `API 密钥`。


## 示例用例

### Slack 

Slack 没有内置集成。但如果你要求 AI Builder 向 Slack 频道发送消息，它将自行研究最佳实现，然后使用 `slack_sdk` Python 包发送消息。


### Scikit-learn

Scikit-learn 没有内置集成。但如果你要求 AI Builder 使用 scikit-learn 对某些文本进行分类，它将自行研究最佳实现，然后使用 `scikit-learn` Python 包加载预训练模型并对文本进行分类。




## 添加自定义知识

如果你正在使用专业/不太知名的库，你可以添加自定义知识来帮助 AI Builder 理解如何使用它。只需在应用设置的 **Knowledge**（知识）部分提供库的简短描述、其用途和示例用法。这将在 AI Builder 尝试调用该库的函数时为其提供指导。

```python exec
import reflex as rx
```

```md alert warning
# 在哪里找到知识部分
```

```python eval
rx.image(
    src="https://web.reflex-assets.dev/ai_builder/features/knowledge_light.avif",
    alt="Where to find the Knowledge Section",
    class_name="rounded-lg border border-secondary-a4 mb-2",
)
```


## 不支持的内容

有极少数库由于其大小而不受支持。例如，像 `torch` 或 `tensorflow` 这样的大型机器学习框架不直接受支持。在这些情况下，我们建议使用可以模拟类似功能的一流集成（例如，用于在云中运行 ML 模型的 Replicate 集成）。
