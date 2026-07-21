# 技能（Skills）

```python exec
import reflex as rx


def _summary_card(kicker: str, title: str, body: str) -> rx.Component:
    return rx.el.div(
        rx.el.div(kicker, class_name="text-xs font-semibold uppercase text-primary-10"),
        rx.el.h3(title, class_name="text-base font-semibold text-secondary-12"),
        rx.el.p(body, class_name="text-sm leading-6 text-secondary-11"),
        class_name="flex flex-col gap-2 rounded-lg border border-secondary-a4 bg-white-1 p-4",
    )


def skills_summary_cards() -> rx.Component:
    return rx.el.div(
        _summary_card(
            "Docs",
            "Current Reflex guidance",
            "Point agents to the right Reflex docs for state, vars, components, routing, styling, deployment, and more.",
        ),
        _summary_card(
            "Setup",
            "Python environment workflow",
            "Teach agents how to create a virtual environment, install Reflex, and initialize a new project safely.",
        ),
        _summary_card(
            "Runtime",
            "Process management",
            "Give agents a repeatable way to compile, run, restart, and debug Reflex apps without guessing at processes.",
        ),
        _summary_card(
            "Context",
            "Pairs well with MCP",
            "Use skills for durable local instructions and MCP for structured lookup of current docs and component data.",
        ),
        class_name="grid grid-cols-1 gap-3 md:grid-cols-2 my-6",
    )
```

Reflex Agent Skills 为 AI 编码助手提供构建 Reflex 应用的最新指导。它们将 Reflex 特定的知识、设置步骤和进程管理工作流打包成可重用的 `SKILL.md` 文件，代理可以在对话或代码库需要时加载它们。

这些技能在 [reflex-dev/agent-skills](https://github.com/reflex-dev/agent-skills) 仓库中维护，专为支持 Agent Skills 标准的代理设计，包括 Claude Code、Cursor、OpenCode、OpenAI Codex 和 Pi。

```python eval
skills_summary_cards()
```

## 何时使用技能

当你希望 AI 助手遵循 Reflex 特定的工作流而不是仅依赖通用训练数据时，使用 Reflex Agent Skills。它们在助手需要以下情况时特别有用：

- 构建或编辑 Reflex 应用。
- 设置新的 Python 环境。
- 决定哪些 Reflex 文档适用于任务。
- 编译、运行、重启或调试本地 Reflex 服务器。
- 使生成的代码与当前 Reflex 模式保持一致。

技能按上下文加载。例如，当助手看到导入 `reflex as rx` 的 Python 文件时，它可以加载 Reflex 文档技能。当你要求它启动新应用时，它可以在运行项目命令之前加载 Python 环境设置技能。

## 技能 vs MCP

技能和 MCP 解决相关但不同的问题。为了获得最佳体验，请同时使用两者。

```md definition
# 技能（Skills）

本地指令包，告诉代理如何处理 Reflex、遵循哪些工作流以及哪些参考对常见开发任务很重要。

# MCP

通过托管服务器对 Reflex 文档和组件信息的结构化运行时访问。当代理或编辑器可以直接调用 MCP 工具时使用它。
```

```md alert info
# 使用技能获取持久的本地指导。使用 MCP 获取结构化文档查找和更丰富的工具辅助上下文。
```

## 安装

选择与你的助手匹配的安装路径。

`````md tabs

## Claude Code

从 Claude Code 插件市场安装插件：

```text
/plugin marketplace add reflex-dev/agent-skills
/plugin install reflex@reflex-agent-skills
```

如果技能没有立即出现，安装后重启或刷新 Claude Code。


## Cursor

从 Cursor 市场安装，或从以下位置手动添加仓库：

```text
reflex-dev/agent-skills
```

在 Cursor 中，通过 **Settings > Rules > Add Rule > Remote Rule (GitHub)** 添加。


## CLI

如果你的环境支持 `skills` CLI，使用以下命令安装包：

```bash
npx skills add reflex-dev/agent-skills
```

稍后使用 CLI 的更新命令保持技能包最新。


## 手动

克隆仓库：

```bash
git clone https://github.com/reflex-dev/agent-skills.git
```

然后将 `skills/` 内的文件夹复制到适当位置：

| 代理 | 技能目录 |
| --- | --- |
| Claude Code | `~/.claude/skills/` |
| Cursor | `~/.cursor/skills/` |
| OpenCode | `~/.config/opencode/skills/` |
| OpenAI Codex | `~/.codex/skills/` |
| Pi | `~/.pi/agent/skills/` |

复制技能文件夹本身，而不是父级 `skills/` 目录。

`````

## 包含的技能

Reflex 技能包包含三个核心技能。

`````md tabs

## 文档（Docs）

`reflex-docs` 技能为助手提供 Reflex 特定的参考地图和框架核心概念的摘要。

### 在以下情况使用此技能：

- 使用 Reflex 构建全栈 Python Web 应用。
- 编写导入 `reflex` 或使用 `rx` 命名空间的文件。
- 创建组件。
- 管理状态、变量、计算变量或事件处理程序。
- 处理路由、样式、数据库模型、资产、认证、客户端存储、API 路由、自定义组件或封装的 React 组件。

### 它将助手指向以下文档：

- 核心应用结构：入门、组件、状态和变量、事件、页面和路由。
- UI 和样式：样式、资产、组件库和配方。
- 后端功能：数据库模型、认证、客户端存储、API 路由和 API 参考。
- 高级集成：自定义组件和封装的 React 组件。

它还提醒助手在冲突时优先使用当前 Reflex 文档而非预训练知识。


## 设置（Setup）

`setup-python-env` 技能指导助手为 Reflex 应用设置 Python 环境。

### 在以下情况使用此技能：

- 启动新的 Reflex 项目。
- 设置开发环境。
- 没有 `.venv` 目录。
- 由于缺少依赖项导致 Reflex 导入失败。

### 首选工作流：

```bash
uv venv .venv
source .venv/bin/activate
uv add reflex
reflex init
```

如果 `uv` 不可用，技能将回退到：

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install reflex
reflex init
```

该工作流检查现有的 `.venv`，验证 Python 3.10 或更高版本，并仅在需要时安装 Reflex。


## 进程（Process）

`reflex-process-management` 技能教助手如何编译、运行、重新加载和调试 Reflex 应用。

### 在以下情况使用此技能：

- 测试 Reflex 应用是否编译。
- 启动本地 Reflex 服务器。
- 代码更改后重启服务器。
- 读取日志以诊断应用错误。

### 验证命令：

```bash
reflex compile --dry
```

### 运行命令：

```bash
reflex run --env prod --single-port 2>&1 | tee reflex.log
```

生产模式不会热重载。要应用代码更改，助手应停止应用端口的监听进程并重启服务器：

```bash
lsof -i :<port> -sTCP:LISTEN -t
kill -INT $(lsof -i :<port> -sTCP:LISTEN -t)
```

使用 `-sTCP:LISTEN` 帮助助手定位服务器进程而不是浏览器连接。

`````

## 推荐工作流

`````md tabs

## 新应用

1. 在你的助手中安装 Reflex Agent Skills。
2. 要求助手创建或初始化 Reflex 应用。
3. 助手应加载 `setup-python-env`。
4. 环境准备好后，助手可以运行 `reflex init`。
5. 在构建应用时，助手应使用 `reflex-docs` 获取当前框架指导。
6. 在交回应用之前，助手应使用 `reflex-process-management` 编译或运行项目。


## 现有应用

1. 在支持代理的编辑器中打开你的 Reflex 项目。
2. 要求你需要的功能、bug 修复或重构。
3. 当助手看到 Reflex 代码时应加载 `reflex-docs`。
4. 对于本地验证，助手应使用 `reflex compile --dry` 编译应用或使用进程管理工作流运行它。


## 调试

1. 要求助手检查错误。
2. 如果应用是通过进程管理工作流启动的，助手应读取 `reflex.log`。
3. 助手应识别失败的导入、组件、事件处理程序、路由或状态更新。
4. 应用修复后，如果应用在生产模式下运行，助手应重启应用。

`````

## 保持技能更新

由于 Reflex 发展迅速，请定期更新技能包。

如果你通过市场或 skills CLI 安装，请通过同一工具更新。如果你手动克隆了仓库，请拉取最新更改：

```bash
cd agent-skills
git pull
```

如果你的设置不直接从克隆的仓库读取，则将更新的技能文件夹复制回助手的技能目录。

## 故障排除

`````md tabs

## 加载

检查：

- 你的助手支持 Agent Skills。
- 技能文件夹在正确的技能目录中。
- 每个技能文件夹包含一个 `SKILL.md` 文件。
- 如果需要，安装后助手已重启或刷新。


## 过时的建议

要求助手使用 Reflex 文档技能，或将技能包与 Reflex MCP 集成配对，以结构化访问当前文档和组件信息。


## 命令

要求助手再次遵循 `setup-python-env` 技能并验证：

- 虚拟环境已激活。
- Python 版本为 3.10 或更高。
- Reflex 已安装在活动环境中。
- 命令是从项目根目录运行的。


## 应用更新

如果应用是使用 `--env prod` 启动的，它将不会热重载。使用 `reflex-process-management` 工作流重启服务器。

`````

## 贡献

每个技能位于 `skills/<name>/` 中并包含一个 `SKILL.md` 清单。要贡献新指导，请在 [reflex-dev/agent-skills](https://github.com/reflex-dev/agent-skills) 仓库中更新或添加技能，并遵循 [Agent Skills 规范](https://agentskills.io/)的技能格式。
