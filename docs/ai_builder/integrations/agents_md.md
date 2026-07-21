# AGENTS.md 和 CLAUDE.md

`AGENTS.md` 和 `CLAUDE.md` 是项目级别的指令文件，AI 编码助手在进入你的仓库时会读取它们。它们为助手提供持久的、仓库特定的上下文，使其遵循 Reflex 约定而不是通用默认值。

- `AGENTS.md` 被遵循 [AGENTS.md 约定](https://agents.md)的代理读取，包括 Cursor、OpenCode、OpenAI Codex 和 Pi。
- `CLAUDE.md` 被 [Claude Code](https://code.claude.com/docs/en/memory) 读取。Claude Code 不直接读取 `AGENTS.md` — 请参阅下面的[与 Claude Code 共享](#sharing-with-claude-code)。

Reflex 项目应在项目根目录（`rxconfig.py` 旁边）至少有一个这些文件。

## 推荐内容

[reflex-dev/agent-skills](https://github.com/reflex-dev/agent-skills) 仓库提供了一个 `AGENTS.md` 模板，将助手指向 [Reflex Agent Skills](/docs/ai/integrations/skills/)，用于环境设置、文档查找和进程管理。将其用作起点，然后添加任何特定于你的代码库的内容。

```md alert info
# `AGENTS.md` 按名称引用技能，因此一旦在助手中安装了 [Reflex Agent Skills](/docs/ai/integrations/skills/)，它就可以工作。
```

## 安装

`reflex init` 默认会在项目根目录（`rxconfig.py` 旁边）写入一个初始的 `AGENTS.md`（传递 `--no-agents` 以选择退出）：

```bash
reflex init
```

Reflex 提供的内容位于 `reflex managed` 开始/结束标记之间。你在标记之外添加的任何内容在 init 刷新受管部分时都会被保留，因此可以安全地重新运行。如果现有的 `AGENTS.md` 没有标记，受管部分将添加到顶部，你的内容保留在其下方。对于 Claude Code 用户，init 还会创建一个导入 `AGENTS.md` 的 `CLAUDE.md`（或更新一个未引用它的现有文件）。

要将模板添加到现有项目，将其下载到项目根目录（`rxconfig.py` 旁边）：

```bash
curl -fsSL https://raw.githubusercontent.com/reflex-dev/agent-skills/main/AGENTS.md -o AGENTS.md
```

或从 [reflex-dev/agent-skills](https://github.com/reflex-dev/agent-skills) 仓库的本地克隆中手动复制。

## 与 Claude Code 共享

Claude Code 读取 `CLAUDE.md`，而不是 `AGENTS.md`。为避免重复内容，创建一个使用 `@` 语法[导入](https://code.claude.com/docs/en/memory#import-additional-files) `AGENTS.md` 的 `CLAUDE.md`：

```md
@AGENTS.md

## Claude Code

在此处添加任何 Claude 特定的指令。
```

Claude Code 在会话开始时展开 `@AGENTS.md` 导入，然后附加你在其下方编写的任何内容。两个文件从单一来源保持同步。

安装后，你的项目根目录如下所示：

```text
my_app/
  AGENTS.md
  CLAUDE.md
  rxconfig.py
  my_app/
    my_app.py
```

## 项目特定的添加

该模板涵盖了 Reflex 范围的设置。在其下方，添加助手应该了解的关于你的项目的任何其他内容：

- 内部约定和代码风格。
- 必需的 lint、类型检查或测试命令。
- 文件夹布局以及新代码应该放在哪里。
- 托管或部署说明。

保持条目简短和命令式——助手比长段落更可靠地遵循简洁、直接的指令。

## 保持文件更新

Reflex 发展迅速。如果 `reflex init` 创建了你的 `AGENTS.md`，重新运行 `reflex init` 会刷新受管标记之间的内容，同时保留你在标记之外添加的所有内容。

如果你使用 `curl` 下载了模板，重新运行相同的命令来刷新它（注意这会替换整个文件）：

```bash
curl -fsSL https://raw.githubusercontent.com/reflex-dev/agent-skills/main/AGENTS.md -o AGENTS.md
```

如果你克隆了 [reflex-dev/agent-skills](https://github.com/reflex-dev/agent-skills) 仓库，拉取最新更改并将文件复制回你的项目：

```bash
cd agent-skills
git pull
cp AGENTS.md /path/to/your/reflex-project/AGENTS.md
```

## 与 Skills 和 MCP 结合

`AGENTS.md` 和 `CLAUDE.md` 将助手锚定在你的项目中。将它们与其他入门工具配对以获得更深入的 Reflex 知识：

- [Reflex Agent Skills](/docs/ai/integrations/skills/) 提供该文件按名称引用的可重用工作流。
- [Reflex MCP](/docs/ai/integrations/mcp-overview/) 提供运行时的结构化文档查找。
- [llms.txt 索引](/llms.txt) 在一个文件中提供文档的广泛地图。
