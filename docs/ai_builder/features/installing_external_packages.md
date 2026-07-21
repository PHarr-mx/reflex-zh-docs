# 安装外部包（Installing External Packages）

```python exec
import reflex as rx
```

Reflex Build 允许你安装外部 Python 包以在你的应用中使用。如果你想使用默认 Reflex Build 环境中未包含的包，这非常有用。示例可能包括 `openai`、`langsmith`、`requests` 等。

有两种安装外部包的方式：

1. **通过聊天界面**：你可以要求 AI 为你安装包。
2. **添加到 `requirements.txt` 文件**：你可以将包添加到 `requirements.txt` 文件，然后保存应用。这将在你的应用环境中安装该包。

## 通过聊天界面安装

在聊天界面中输入你要安装的包的名称。AI 将为你安装该包。


```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/external_packages_input.avif",
        alt="Installing external packages via the chat interface",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

## 通过 requirements.txt 文件安装

将包添加到 `requirements.txt` 文件，然后保存应用。这将在你的应用环境中安装该包并重新编译你的应用。

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/external_packages_requirements.avif",
        alt="Installing external packages via requirements.txt",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```
