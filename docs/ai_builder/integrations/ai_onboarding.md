# AI 入门（AI Onboarding）

```python exec
import reflex as rx


def _resource_card(
    title: str, body: str, href: str, action: str, target: str = "_self"
) -> rx.Component:
    return rx.el.a(
        rx.el.div(
            rx.el.h3(title, class_name="text-base font-semibold text-secondary-12"),
            rx.el.p(body, class_name="text-sm leading-6 text-secondary-11"),
            rx.el.div(action, class_name="text-sm font-semibold text-primary-10"),
            class_name="flex h-full flex-col gap-2 rounded-lg border border-secondary-a4 bg-white-1 p-4 transition-colors hover:bg-secondary-2 shadow-xs",
        ),
        href=href,
        target=target,
        class_name="no-underline",
    )


def onboarding_resources() -> rx.Component:
    return rx.el.div(
        _resource_card(
            "Docs for Agents",
            "Give your agent current Reflex documentation as Markdown, llms.txt, or structured MCP context.",
            "/llms.txt",
            "Open llms.txt",
            target="_blank",
        ),
        _resource_card(
            "MCP",
            "Connect supported AI tools to Reflex documentation and component information through MCP.",
            "/ai/integrations/mcp-overview/",
            "View MCP overview",
        ),
        _resource_card(
            "Skills",
            "Install Reflex Agent Skills so assistants follow Reflex-specific workflows for docs, setup, and process management.",
            "/ai/integrations/skills/",
            "Install skills",
        ),
        _resource_card(
            "Reflex Build",
            "Use Reflex Build when you want an AI-native environment for creating, editing, previewing, and shipping apps.",
            "/ai/overview/best-practices/",
            "Read best practices",
        ),
        class_name="grid grid-cols-1 gap-3 md:grid-cols-2 my-6",
    )
```

将你的 AI 编码助手入门到 Reflex 所需的一切。

如果你正在使用 AI 构建 Reflex 应用，请结合当前文档、结构化 MCP 上下文和本地技能，使助手能够以与 Reflex 文档相同的假设进行规划、编码、运行和调试。

```python eval
onboarding_resources()
```

## 前提条件：选择你的工作流

你不需要 API 密钥来阅读 Reflex 文档。首先决定你的助手将如何与 Reflex 协作：

- 对于本地应用开发，使用 Python 3.10 或更高版本以及项目虚拟环境。
- 对于当前文档上下文，向助手提供 Markdown 文档或 `llms.txt`。
- 对于结构化工具访问，使用 Reflex MCP 集成。
- 对于可重复的代理行为，安装 Reflex Agent Skills。
- 对于基于浏览器的 AI 构建器，使用 Reflex Build。

## 面向代理的 Reflex 文档

你可以通过几种方式向助手提供当前的 Reflex 文档。

`````md tabs

## Markdown 页面

每个文档页面都有一个 Markdown 版本，代理可以直接阅读。在文档路径后添加 `.md`：

```text
https://reflex.dev/docs/ai/integrations/ai-onboarding.md
```

当代理需要一个专注的页面时使用此方式。


## llms.txt

当代理需要 Reflex 文档的广泛地图时，使用生成的文档索引：

```text
https://reflex.dev/docs/llms.txt
```

该索引按部分对文档进行分组，并链接到适合代理的 Markdown 资产。


## MCP

当你的编辑器或代理可以调用工具进行结构化文档和组件查找时，使用 MCP：

```text
https://build.reflex.dev/mcp
```

详情请参阅 [MCP 概述](/docs/ai/integrations/mcp-overview/)和 [MCP 安装](/docs/ai/integrations/mcp-installation/)指南。

`````

## Reflex MCP

Reflex MCP 集成为支持的 AI 工具提供对 Reflex 框架文档和组件信息的结构化访问。当你的助手可以连接到 MCP 服务器并在编辑代码时受益于工具辅助查找时使用它。

```md alert warning
# Reflex MCP 集成目前仅对企业客户可用。请[预约演示](https://reflex.dev/pricing/)以讨论访问权限。
```

## Reflex Agent Skills

Reflex Agent Skills 是本地指令包，教 AI 助手如何处理 Reflex 项目。它们涵盖：

- 当前 Reflex 文档和概念查找。
- Reflex 项目的 Python 环境设置。
- Reflex 编译、运行、重启和调试工作流。

从 [reflex-dev/agent-skills](https://github.com/reflex-dev/agent-skills) 仓库安装技能包，或遵循 [Skills 安装指南](/docs/ai/integrations/skills/)。

```bash
npx skills add reflex-dev/agent-skills
```

## 快速入门提示词

使用这些提示词为你的代理提供一个强有力的起点。

`````md tabs

## 新应用

```text
Create a new Reflex app. Use current Reflex documentation, set up a Python 3.10+ virtual environment, initialize the project, and validate it with reflex compile --dry before handing it back.
```


## 现有应用

```text
Work on this existing Reflex app. First inspect the project structure and current dependencies. Use Reflex docs for current APIs, make the requested change, then run reflex compile --dry or the project's existing test command.
```


## 调试

```text
Debug this Reflex app. Read the error and relevant logs, identify whether the failure is in imports, state, event handlers, routing, components, or runtime setup, then apply the smallest fix and re-run validation.
```

`````

## Reflex Build

Reflex Build 是在浏览器中创建 Reflex 应用的 AI 原生方式。当你想在不先设置本地环境的情况下生成、编辑、预览和共享应用时使用它。

从 [Reflex Build 最佳实践](/docs/ai/overview/best-practices/)指南开始，然后当你想让本地助手继续使用相同的 Reflex 概念时使用 MCP 和 Skills。

## 推荐验证

要求你的助手在交回工作之前验证 Reflex 更改：

```bash
reflex compile --dry
```

对于运行中的应用，将其与 Reflex Agent Skills 的进程管理工作流配对，以便助手仅重启它拥有的服务器进程。
