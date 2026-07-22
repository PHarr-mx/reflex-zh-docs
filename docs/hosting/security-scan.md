```python exec
import reflex as rx
```

# 安全扫描（Security Scan）

`reflex cloud scan` 命令对你的应用源代码运行 Reflex 感知的安全审查。它将你的项目上传到 Reflex Cloud，检查安全和逻辑缺陷，并按严重程度报告发现。

```md alert info
# 扫描应用的 CLI 命令
`reflex cloud scan [OPTIONS] [DIRECTORY]`
```

扫描需要认证。如果你尚未登录，请先运行 `reflex login`。

## 运行扫描

从你的应用根目录运行：

```bash
reflex cloud scan
```

这将扫描当前目录。传递路径以扫描其他位置：

```bash
reflex cloud scan path/to/app
```

该命令压缩你的应用源代码，跳过依赖项和构建目录（`.web`、`node_modules`、`.venv`、`__pycache__` 等），提交审查，并等待结果。

## 阅读结果

发现按严重程度排序打印：

- **CRITICAL**：立即修复。
- **HIGH**：严重；尽快修复。
- **MEDIUM**：应该处理。
- **LOW**：小问题和建议。

每个发现显示触发的规则、其类别、文件和行以及描述。可用时包含推荐的修复。如果未发现任何问题，该命令报告审查通过。

## 按严重程度失败

`--fail-on` 使命令在存在等于或高于给定严重程度的发现时以非零退出：

```bash
reflex cloud scan --fail-on high
```

默认值为 `low`，因此任何发现都会导致非零退出。传递 `--fail-on none` 以始终退出 `0`。

## JSON 输出

`--json`（或 `-j`）将原始结果打印为 JSON 而不是格式化输出：

```bash
reflex cloud scan --json
```

## 在 CI 中运行

`--fail-on` 设置退出代码，因此扫描可以在发现问题时阻止合并或部署。使用 `--token` 传递令牌并添加 `--no-interactive` 以使命令永不提示。

在 Cloud UI 的令牌选项卡中创建 `REFLEX_AUTH_TOKEN`（参见[令牌文档](/docs/hosting/tokens/#tokens)）并将其存储为仓库密钥。

此 GitHub Actions 工作流在任何 `high` 或 `critical` 发现时使构建失败：

```yaml
name: Security Scan

on:
  pull_request:
    branches:
      - main

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: actions/setup-python@v6
        with:
          python-version: "3.12"
      - name: Install Reflex
        run: pip install reflex
      - name: Run security scan
        run: reflex cloud scan --no-interactive --fail-on high --token ${{ secrets.REFLEX_AUTH_TOKEN }}
```

## 选项

有关选项的完整列表，请参阅 [CLI 参考](/docs/hosting/cli/scan/)。
