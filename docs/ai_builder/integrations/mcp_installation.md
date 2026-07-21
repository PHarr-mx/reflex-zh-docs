# 安装（Installation）

```python exec
import reflex as rx
```

```md alert warning
# Reflex MCP 集成目前仅对企业客户可用。请[预约演示](https://reflex.dev/pricing/)以讨论访问权限。
```

要使用 Reflex MCP 集成，你需要配置你的 AI 助手或编码工具以连接到 Reflex MCP 服务器。你的本地机器上不需要额外的 Python 包——服务器已托管并准备就绪。

## 前提条件

- 兼容 MCP 的 AI 工具（Claude Code、Claude Desktop、Codex、Cursor、Gemini CLI、GitHub Copilot、OpenCode、Windsurf 等）
- 访问托管 MCP 服务器的网络连接
- 用于 OAuth 2.1 认证的有效 Reflex 账户

## 认证

Reflex MCP 服务器使用 OAuth 2.1 协议进行安全认证。你需要一个有效的 Reflex 账户，当你提供 Reflex 凭据时，认证将通过你的 MCP 客户端配置自动处理。

## IDE 和编码助手集成

### Claude Code

通过运行以下命令将 Reflex MCP 服务器添加到 Claude Code：

```bash
claude mcp add --transport http reflex https://build.reflex.dev/mcp
```

然后在 Claude Code 内运行 `/mcp` 命令并按照浏览器中的登录步骤进行认证。认证令牌安全存储并自动刷新。详情请参阅 [Claude Code MCP 文档](https://code.claude.com/docs/en/mcp)。

### Claude Desktop

Claude Desktop 从你的 Claude 账户的连接器中拉取远程 MCP 服务器。转到 [claude.ai](https://claude.ai) → **Settings** → **Connectors** → **Add custom connector**，输入 `https://build.reflex.dev/mcp` 作为 URL，并完成 OAuth 登录。登录后，该连接器将在 Claude Desktop 中可用。详情请参阅[自定义连接器指南](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp)和计划可用性。

### Codex

通过运行以下命令将 Reflex MCP 服务器添加到 Codex：

```bash
codex mcp add reflex --url https://build.reflex.dev/mcp
```

详情请参阅 [Codex MCP 文档](https://developers.openai.com/codex/mcp)。

### Cursor

[点击此处在 Cursor 中安装 Reflex MCP 服务器](cursor://anysphere.cursor-deeplink/mcp/install?name=reflex&config=eyJ1cmwiOiJodHRwczovL2J1aWxkLnJlZmxleC5kZXYvbWNwIn0=)，或编辑（或创建）`~/.cursor/mcp.json` 用于全局配置，或项目根目录中的 `.cursor/mcp.json` 用于项目特定配置：

```json
{
  "mcpServers": {
    "reflex": {
      "url": "https://build.reflex.dev/mcp"
    }
  }
}
```

在 **MCP & Integrations** 下打开 Cursor 设置以验证服务器已连接并完成 OAuth 登录。详情请参阅 [Cursor MCP 文档](https://cursor.com/docs/context/mcp)。

### Gemini CLI

将 Reflex MCP 服务器添加到 `~/.gemini/settings.json`：

```json
{
  "mcpServers": {
    "reflex": {
      "httpUrl": "https://build.reflex.dev/mcp"
    }
  }
}
```

详情请参阅 [Gemini CLI MCP 文档](https://google-gemini.github.io/gemini-cli/docs/tools/mcp-server.html)。

### GitHub Copilot

在你的项目根目录中创建 `.vscode/mcp.json` 文件（或从 VS Code 命令面板打开 **MCP: Open User Configuration** 用于全局配置）：

```json
{
  "servers": {
    "reflex": {
      "type": "http",
      "url": "https://build.reflex.dev/mcp"
    }
  }
}
```

保存后，从 `mcp.json` 中条目上方的内联操作启动服务器，然后在提示时完成 OAuth 登录。详情请参阅 [VS Code MCP 文档](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)。

### OpenCode

将 Reflex MCP 服务器添加到你的 `opencode.json`（项目）或 `~/.config/opencode/opencode.json`（全局）：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "reflex": {
      "type": "remote",
      "url": "https://build.reflex.dev/mcp",
      "enabled": true
    }
  }
}
```

详情请参阅 [OpenCode MCP 文档](https://opencode.ai/docs/mcp-servers/)。

### Windsurf

编辑（或创建）`~/.codeium/windsurf/mcp_config.json` 并添加 Reflex 服务器：

```json
{
  "mcpServers": {
    "reflex": {
      "serverUrl": "https://build.reflex.dev/mcp"
    }
  }
}
```

保存后，打开 Cascade 并点击 MCP 工具栏中的刷新图标以加载新服务器。详情请参阅 [Windsurf MCP 文档](https://docs.windsurf.com/windsurf/cascade/mcp)。
