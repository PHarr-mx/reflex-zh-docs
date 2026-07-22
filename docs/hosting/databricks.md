```python exec
import reflex as rx
```

# 部署 Reflex 到 Databricks

本指南将引导你完成使用 Apps 平台在 Databricks 上部署 Reflex Web 应用程序。

## 前提条件

- 启用 Unity Catalog 的 Databricks 工作区
- 包含你的 Reflex 应用程序的 GitHub 仓库
- Reflex 企业版许可证（用于单端口部署）

## 步骤 1：连接你的仓库

1. **链接 GitHub 仓库**
   - 导航到你的 Databricks 工作区
   - 转到你的用户目录
   - 点击 **Create** → **Git folder**
   - 粘贴包含 Reflex 应用程序的 GitHub 仓库的 URL

## 步骤 2：配置应用程序设置

### 创建配置文件

直接在 Databricks 中创建一个名为 `app.yaml` 的新文件（不在 GitHub 中）：

```yaml
command: [
  "reflex", 
  "run",
  "--env",
  "prod",
  "--backend-port",
  "$DATABRICKS_APP_PORT"
]

env:
  - name: "HOME"
    value: "/tmp/reflex"
  - name: "REFLEX_ACCESS_TOKEN"
    value: "your-token-here"
  - name: "DATABRICKS_WAREHOUSE_ID"
    value: "your-sql-warehouse-id"
  - name: "DATABRICKS_CATALOG"
    value: "your-catalog-name"
  - name: "DATABRICKS_SCHEMA"
    value: "your-schema-name"
  - name: "REFLEX_SHOW_BUILT_WITH_REFLEX"
    value: 0
```

### 获取所需的令牌

1. **Reflex 访问令牌**
   - 访问 [Reflex Cloud 令牌文档](/docs/hosting/tokens)
   - 导航到 Account Settings → Tokens
   - 创建一个新令牌并复制该值
   - 替换配置中的 `your-token-here`
2. **Databricks 资源**
   - 使用你的 SQL 仓库 ID 更新 `DATABRICKS_WAREHOUSE_ID`
   - 使用你的目标目录名称更新 `DATABRICKS_CATALOG`
   - 使用你的目标架构名称更新 `DATABRICKS_SCHEMA`

## 步骤 3：启用单端口部署

更新你的 Reflex 应用程序以实现 Databricks 兼容性：

### 更新 rxconfig.py

```python
import reflex as rx
import reflex_enterprise as rxe

rxe.Config(app_name="app", use_single_port=True)
```

### 更新应用程序入口点

修改你定义 `rx.App` 的主应用程序文件：

```python
import reflex_enterprise as rxe

app = rxe.App(
    # 你的应用配置
)
```

```md alert info
# 还要将 `reflex-enterprise` 和 `asgiproxy` 添加到你的 `requirements.txt` 文件中。
```

## 步骤 4：创建 Databricks 应用

1. **导航到 Apps**
   - 转到 **Compute** → **Apps**
   - 点击 **Create App**
2. **配置应用程序**
   - 选择 **Custom App**
   - 为你的应用程序配置 SQL 仓库

## 步骤 5：设置权限

如果你使用的是 `samples` 目录，则可以跳过权限部分。

### 目录权限

1. 导航到 **Catalog** → 选择你的目标目录
2. 转到 **Permissions**
3. 添加应用的服务主体用户
4. 授予以下权限：
   - **USE CATALOG**
   - **USE SCHEMA**

### 架构权限

1. 导航到特定架构
2. 转到 **Permissions**
3. 授予以下权限：
   - **USE SCHEMA**
   - **EXECUTE**
   - **SELECT**
   - **READ VOLUME**（如果需要）

## 步骤 6：部署应用程序

1. **启动部署**
   - 在 Apps 界面中点击 **Deploy**
   - 当提示输入代码路径时，提供你的 Git 文件夹路径或选择你的仓库文件夹
2. **监控部署**
   - 部署过程将自动开始
   - 监控日志以查看任何配置问题

## 更新你的应用程序

要从你的 GitHub 仓库部署更新：

1. **拉取最新更改**
   - 在部署界面中，点击 **Deployment Source**
   - 选择 **main** 分支
   - 点击 **Pull** 以从 GitHub 获取最新更改
2. **重新部署**
   - 再次点击 **Deploy** 以应用更新

## 配置参考

```python eval
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell("Environment Variable"),
            rx.table.column_header_cell("Description"),
            rx.table.column_header_cell("Example"),
        ),
    ),
    rx.table.body(
        rx.table.row(
            rx.table.cell(rx.code("HOME")),
            rx.table.cell("应用程序主目录"),
            rx.table.cell(rx.code("/tmp/reflex")),
        ),
        rx.table.row(
            rx.table.cell(rx.code("REFLEX_ACCESS_TOKEN")),
            rx.table.cell("Reflex Cloud 的认证"),
            rx.table.cell(rx.code("rx_token_...")),
        ),
        rx.table.row(
            rx.table.cell(rx.code("DATABRICKS_WAREHOUSE_ID")),
            rx.table.cell("SQL 仓库标识符"),
            rx.table.cell("自动分配"),
        ),
        rx.table.row(
            rx.table.cell(rx.code("DATABRICKS_CATALOG")),
            rx.table.cell("目标目录名称"),
            rx.table.cell(rx.code("main")),
        ),
        rx.table.row(
            rx.table.cell(rx.code("DATABRICKS_SCHEMA")),
            rx.table.cell("目标架构名称"),
            rx.table.cell(rx.code("default")),
        ),
        rx.table.row(
            rx.table.cell(rx.code("REFLEX_SHOW_BUILT_WITH_REFLEX")),
            rx.table.cell("显示 Reflex 品牌（仅企业版）"),
            rx.table.cell([rx.code("0"), " 或 ", rx.code("1")]),
        ),
    ),
    variant="surface",
    margin_y="1em",
)
```

## 故障排除

- **权限错误**：验证所有目录和架构权限是否正确设置
- **端口问题**：确保你使用的是 `$DATABRICKS_APP_PORT` 和单端口配置
- **令牌问题**：验证你的 Reflex 访问令牌有效且配置正确
- **部署失败**：检查部署日志以获取特定错误消息

## 注意事项

- 单端口部署需要 Reflex 企业版
- 配置必须直接在 Databricks 中创建，而不是从 GitHub 推送
- 更新需要从部署界面手动拉取
