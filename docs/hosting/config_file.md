```python exec
import reflex as rx
```

# Cloud 配置文件

## 什么是 reflex cloud config？

以下命令：

```bash
reflex cloud config
```

生成一个 `cloud.yml` 配置文件，用于将你的 Reflex 应用部署到 Reflex 云平台。此文件告诉 Reflex 如何在云中运行你的应用以及在哪里运行。

## 配置文件结构

`cloud.yml` 文件使用 YAML 格式并支持以下结构。**所有字段都是可选的**，如果未指定将使用合理的默认值：

```yaml
# 基本部署设置
name: my-app-prod                    # 可选：默认为项目文件夹名称
description: 'Production deployment' # 可选：默认为空
projectname: my-client-project          # 可选：默认为个人项目

# 基础设施设置
regions:                            # 可选：默认为 sjc: 1
  sjc: 1                           # San Jose（机器数量）
  lhr: 2                           # London（机器数量）
vmtype: c2m2                       # 可选：默认为 c1m1

# 自定义域名和环境
hostname: myapp                    # 可选：myapp.reflex.dev
envfile: .env.production           # 可选：默认为 .env

# 额外依赖项
packages:                          # 可选：默认为空
  - procps
```

## 配置选项参考

```python demo-only
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell(
                rx.text("Option", size="1", weight="bold", color=rx.color("slate", 11))
            ),
            rx.table.column_header_cell(
                rx.text("Type", size="1", weight="bold", color=rx.color("slate", 11))
            ),
            rx.table.column_header_cell(
                rx.text("Default", size="1", weight="bold", color=rx.color("slate", 11))
            ),
            rx.table.column_header_cell(
                rx.text(
                    "Description", size="1", weight="bold", color=rx.color("slate", 11)
                )
            ),
            align="center",
        )
    ),
    rx.table.body(*[
        rx.table.row(
            rx.table.cell(rx.text(option, class_name="text-sm")),
            rx.table.cell(rx.text(type_, class_name="text-sm")),
            rx.table.cell(rx.text(default, class_name="text-sm")),
            rx.table.cell(
                rx.link(description, href=link, class_name="text-sm")
                if link
                else rx.text(description, size="1", weight="regular")
            ),
            align="center",
        )
        for option, type_, default, description, link in [
            (
                "name",
                "string",
                "folder name",
                "仪表板中的部署标识符",
                None,
            ),
            ("description", "string", "empty", "部署描述", None),
            (
                "regions",
                "object",
                "sjc: 1",
                "区域部署映射",
                "/docs/hosting/regions",
            ),
            (
                "vmtype",
                "string",
                "c1m1",
                "虚拟机规格",
                "/docs/hosting/machine-types",
            ),
            ("hostname", "string", "null", "自定义子域名", None),
            (
                "envfile",
                "string",
                ".env",
                "环境变量文件路径",
                "/docs/hosting/secrets-environment-vars",
            ),
            ("project", "uuid", "null", "项目 uuid", None),
            ("projectname", "string", "null", "项目名称", None),
            ("packages", "array", "empty", "额外系统包", None),
            ("include_db", "boolean", "false", "包含本地 sqlite", None),
            ("strategy", "string", "auto", "部署策略", None),
        ]
    ]),
    variant="ghost",
    size="2",
    width="100%",
    max_width="800px",
)
```

## 配置选项

有关特定部分的详细信息，请点击表中的链接。

### 项目

使用项目组织部署：

```yaml
projectname: client-alpha    # 将相关部署分组
```

你也可以指定项目 uuid 而不是名称：
```yaml
project: 12345678-1234-1234-1234-1234567890ab
```

你可以转到 reflex cloud 仪表板中项目的主页，在 URL `https://build.reflex.dev/project/uuid` 中找到你的项目 uuid

### Apt 包

安装你的应用需要的额外系统包。包名基于 apt 包管理器：

```yaml
packages:
  - procps=2.0.32-1  # 版本固定是可选的
  - imagemagick 
  - ffmpeg      
```

### 包含 SQLite

包含本地 sqlite 数据库：

```yaml
include_db: true
```

这不是持久的，重启时会丢失。建议改用数据库服务。

### 策略

部署策略：
可用策略：
- `immediate`：[默认] 立即部署
- `rolling`：以滚动方式部署
- `bluegreen`：以蓝绿方式部署
- `canary`：以金丝雀方式部署，作为单台机器启动验证其健康状况，然后重启其余机器。

```yaml
strategy: immediate
```

## 多环境设置

**开发（`cloud-dev.yml`）：**
```yaml
name: myapp-dev
description: 'Development environment'
vmtype: c1m1
envfile: .env.development
```

**预发布（`cloud-staging.yml`）：**
```yaml
name: myapp-staging
description: 'Staging environment'
regions:
  sjc: 1
vmtype: c2m2
envfile: .env.staging
```

**生产（`cloud-prod.yml`）：**
```yaml
name: myapp-production
description: 'Production environment'
regions:
  sjc: 2
  lhr: 1
vmtype: c4m4
hostname: myapp
envfile: .env.production
```

使用特定配置文件部署：

```bash
# 使用默认 cloud.yml
reflex deploy

# 使用特定配置文件
reflex deploy --config cloud-prod.yml
reflex deploy --config cloud-staging.yml
```

