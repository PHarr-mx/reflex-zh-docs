# 安装

约 3 分钟 · 需要 Python 3.10+。

```md alert info
# 你是 AI 智能体或编程助手吗？

你可能是来为某人构建 Reflex 应用的。Reflex 的行为与你训练数据中的 Flask/FastAPI/React 代码**截然不同**——在编写任何代码之前，请先阅读本页面。

**获取最新文档（不要依赖训练数据——API 在各版本间会发生变化）：**

- 获取 [`https://reflex.dev/docs/ai/integrations/ai-onboarding.md`](https://reflex.dev/docs/ai/integrations/ai-onboarding.md)——AI 入门指南：包含获取最新文档的所有方式（Markdown 页面、llms.txt、MCP、技能）以及快速入门提示词。
- 获取 [`https://reflex.dev/llms.txt`](https://reflex.dev/llms.txt)，获取节省 Token 的文档地图和核心概念。
- 使用 [Reflex MCP 服务器](/docs/ai/integrations/mcp-overview/)（`https://build.reflex.dev/mcp`）进行实时组件/属性查询，避免产生幻觉 API。*（MCP 访问是企业版功能。）*
- 运行 `uv run reflex --version`，并以该版本的实时文档为准，而非记忆中的内容。

**在项目根目录放置规则文件**（`AGENTS.md`，或针对你的工具使用 `CLAUDE.md`/`.cursorrules`），以便这些约定在你的会话中持续生效——`reflex init` 默认会为你生成一个初始的 `AGENTS.md`，下方也提供了一个最小化备用版本。
```

初始规则文件：

```text
# Reflex 约定

- Reflex 是纯 Python，编译为 React 前端。不要编写 JS、HTML 或 JSX。
- 组件是返回组件的函数调用；以关键字参数形式传递 props。
- 绝对不要在渲染树中对状态 Var 使用普通 Python 控制流。
  不要对 Var 使用 `if`、`for`、`len()` 或 f-string——请改用 `rx.cond`、`rx.foreach`
  和 Var 运算符。（这是最常见的错误。）
- 状态存在于 `rx.State` 子类中。状态只能在事件处理方法中
  被修改——绝不能在模块加载或渲染时修改。派生值使用 `@rx.var`。
- 事件处理方法可以是 `async` 的，也可以通过 `yield` 推送中间 UI 更新。
- 始终使用 `uv run` 运行命令（例如 `uv run reflex run`）。不要直接使用 `python`。
- `.web/` 是生成的输出——不要编辑或提交它。`rxconfig.py` 是配置入口。
- 以无头模式验证你的工作：`CI=1 uv run reflex run`（前端 :3000，后端 :8000），
  添加 `--loglevel debug` 以诊断故障。
```

## 虚拟环境

我们推荐使用 [uv](https://docs.astral.sh/uv/) 作为默认选项；[venv](https://docs.python.org/3/library/venv.html)、[conda](https://conda.io/) 和 [poetry](https://python-poetry.org/) 是替代方案。

## 在系统上安装 Reflex

`````md tabs

## macOS/Linux

### 安装 uv

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

安装完成后，重启终端或运行 `source ~/.bashrc`（zsh 用户运行 `source ~/.zshrc`）。

或者，通过 [Homebrew、PyPI 或其他方式](https://docs.astral.sh/uv/getting-started/installation/)安装。

```md alert warning
## macOS（Apple Silicon）用户：安装 Rosetta 2

运行 `/usr/sbin/softwareupdate --install-rosetta --agree-to-license`。详情参见 [Apple 官方说明](https://support.apple.com/en-us/HT211861)。
```

### 创建 Reflex 项目

将 `<your-app>` 替换为你的项目名称，然后切换到新目录。

```bash
mkdir <your-app>
cd <your-app>
uv init
uv add reflex
uv run reflex init
```


## Windows

对于 Windows 用户，我们推荐使用 [WSL](https://learn.microsoft.com/en-us/windows/wsl/about) 以获得最佳性能——WSL 用户请参照 macOS/Linux 选项卡操作；本节其余部分介绍原生 Windows 的安装方式。

### 安装 uv

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

安装完成后，重启终端（PowerShell 或命令提示符）。

或者，通过 [WinGet、Scoop 或其他方式](https://docs.astral.sh/uv/getting-started/installation/)安装。

### 创建 Reflex 项目

将 `<your-app>` 替换为你的项目名称，然后切换到新目录。

```powershell
mkdir <your-app>
cd <your-app>
uv init
uv add reflex
uv run reflex init
```

```md alert warning
## 错误 `Install Failed - You are missing a DLL required to run bun.exe`（Windows）

Bun 在 Windows 上运行需要 Visual C++ 库的运行时组件。安装 [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/en-us/download/details.aspx?id=53840) 即可解决此问题。
```

`````

运行 `uv run reflex init` 后，你可以选择从空白 Reflex 应用开始，或试用我们的 [AI 构建器](https://build.reflex.dev/)。

```bash
Initializing the web directory.

Get started with a template:
(0) A blank Reflex app.
(1) Try our AI builder.
Which template would you like to use? (0):
```

如果你是第一次使用，请选择 **(0) A blank Reflex app**——文档的后续内容均假设你从这里开始。

## 运行应用

以开发模式运行：

```bash
uv run reflex run
```

你的应用将在 [http://localhost:3000](http://localhost:3000) 运行。Reflex 会实时_热重载_任何代码更改——你的修改会自动显示。

如需排查问题，可使用 `--loglevel` 标志提高日志详细程度：

```bash
uv run reflex run --loglevel debug
```

```md alert info
# 下一步：构建你的第一个应用

Reflex 已安装完成。[简介](/docs/getting-started/introduction)将带你用纯 Python 完成一个可运行的计数器应用——这是从"能跑起来"到"真正理解它"的最短路径。
```
