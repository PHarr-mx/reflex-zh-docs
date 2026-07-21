# 分叉应用（Fork App）

**分叉应用**功能让你可以获取现有应用并创建自己的版本。这非常适合**实验、自定义或在他人工作的基础上构建**，而不会影响原始应用。

```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/overview/fork_template_light.avif",
        alt="Forking an app in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```


## 如何分叉应用

1. 浏览或打开你想用作起点的应用。
2. 点击应用右上角的 **Fork**（分叉）按钮。
3. AI Builder 将在你的工作区中创建该应用的**副本**。
4. 你现在可以独立于原始应用**编辑、自定义和扩展**你分叉的应用。

## 分叉时会发生什么

- 你将获得原始应用的**完整副本**，包括所有页面、组件和配置。
- 分叉的应用是**完全独立的**，因此你所做的更改不会影响原始应用。
- 你可以像工作区中的任何其他应用一样**重命名、部署或共享**你分叉的应用。

## 常见用例

- **从示例开始**
  使用示例或共享的应用作为基础，节省时间并学习最佳实践。

- **安全实验**
  尝试新想法或功能，而不会影响原始应用的更改。

- **协作和自定义**
  分叉队友的应用以根据你的需求进行定制，同时保持原始应用完好无损。
