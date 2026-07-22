```python exec
import reflex as rx

code_style = {
    "color": rx.color("violet", 11),
    "border_radius": "0.25rem",
    "border": f"1px solid {rx.color('violet', 5)}",
    "background": rx.color("violet", 3),
}

cell_style = {
    "font_family": "Instrument Sans",
    "font_style": "normal",
    "font_weight": "500",
    "font_size": "14px",
    "line_height": "1.5",
    "letter_spacing": "-0.0125em",
    "color": "var(--secondary-11)",
}

github_actions_configs = [
    {
        "name": "auth_token",
        "description": "存储在 GitHub Secrets 中的 Reflex 认证令牌。",
        "required": True,
        "default": "N/A",
    },
    {
        "name": "project_id",
        "description": "你要部署到的项目的 ID。",
        "required": True,
        "default": "N/A",
    },
    {
        "name": "app_directory",
        "description": "包含你的 Reflex 应用的目录。",
        "required": False,
        "default": ". (root)",
    },
    {
        "name": "extra_args",
        "description": "传递给 `reflex deploy` 命令的额外参数。",
        "required": False,
        "default": "N/A",
    },
    {
        "name": "python_version",
        "description": "用于部署环境的 Python 版本。",
        "required": False,
        "default": "3.12",
    },
]
```

# 使用 Github Actions 部署

此 GitHub Action 简化了将 Reflex 应用部署到 Reflex Cloud 的过程。它处理环境设置、安装 Reflex CLI，并以最少的配置部署你的应用。

```md alert info
# 此操作需要 `reflex>=0.6.6`
```

**功能：**
- 直接从你的 GitHub 仓库将 Reflex 应用部署到 Reflex Cloud。
- 支持基于子目录的应用结构。
- 通过 GitHub Secrets 安全使用认证令牌。

## 用法
### 将操作添加到你的工作流
在你的仓库中创建一个 `.github/workflows/deploy.yml` 文件并添加以下内容：

```yaml
name: Deploy Reflex App

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Reflex Cloud
        uses: reflex-dev/reflex-deploy-action@v1
        with:
          auth_token: ${{ secrets.REFLEX_PROJECT_ID }}
          project_id: ${{ secrets.REFLEX_PROJECT_ID }}
          app_directory: "my-app-folder" # 可选，默认为根目录
          extra_args: "--env THIRD_PARTY_APIKEY=***" # 可选
          python_version: "3.12" # 可选
```

### 设置你的密钥
将你的 Reflex 认证令牌安全地存储在你仓库的密钥中：


1. 转到你的 GitHub 仓库。
2. 导航到 Settings > Secrets and variables > Actions > New repository secret。
3. 为 `REFLEX_AUTH_TOKEN` 和 `REFLEX_PROJECT_ID` 创建新密钥。

（在你 UI 的令牌选项卡中创建 `REFLEX_AUTH_TOKEN`，查看这些[文档](/docs/hosting/tokens/#tokens)。

`REFLEX_PROJECT_ID` 可以在 UI 中找到，当你在项目内点击右上角的 How to deploy 按钮并复制 `--project` 标志后的 ID。）



### 输入

```python eval
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell("Name"),
            rx.table.column_header_cell("Description"),
            rx.table.column_header_cell("required"),
            rx.table.column_header_cell("Default"),
        ),
    ),
    rx.table.body(*[
        rx.table.row(
            rx.table.cell(rx.code(github_config["name"], style=code_style)),
            rx.table.cell(github_config["description"], style=cell_style),
            rx.table.cell(rx.code(github_config["required"], style=code_style)),
            rx.table.cell(
                rx.code(github_config["default"], style=code_style), min_width="100px"
            ),
        )
        for github_config in github_actions_configs
    ]),
    variant="surface",
)
```
