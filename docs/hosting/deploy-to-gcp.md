```python exec
import reflex as rx
```

# 部署到 GCP Cloud Run

`reflex cloud deploy --gcp` 命令将 Reflex 应用部署到你自己的 [Google Cloud Run](https://cloud.google.com/run) 服务。Reflex Cloud 获取一个 Cloud Run 就绪的 Dockerfile 和一个 `gcloud` 部署脚本，将 Dockerfile 包装在 [Cloud Build 配置（`cloudbuild.yaml`）](https://cloud.google.com/build/docs/build-config-file-schema)内，并针对你指定的 Google Cloud 项目运行脚本。镜像在 Cloud Build 上构建（因此它可以从任何主机操作系统工作，包括 Apple Silicon）并推送到 Artifact Registry。你的项目树永远不会被修改——Dockerfile 仅存在于提交给 Cloud Build 的构建配置内。

```md alert info
# 仅限企业版。

自部署到 GCP Cloud Run 是 Reflex Cloud **企业版**的一部分。控制平面将对非企业版令牌返回 `403`，CLI 会显示指向此处的明确错误。联系 [sales@reflex.dev](mailto:sales@reflex.dev) 进行升级。
```

## 前提条件

在运行命令之前，安装并认证部署脚本调用的本地工具：

- `gcloud` — 从 [Google Cloud SDK 文档](https://cloud.google.com/sdk/docs/install)安装，然后运行：
  - `gcloud auth login`
  - `gcloud auth application-default login`
- `docker` — `gcloud builds submit` 上传源代码所需。
- `bash` — 用于运行部署脚本。

你还需要：

- 一个**启用计费**的 GCP 项目。没有它，`gcloud services enable` 会失败并显示 `UREQ_PROJECT_BILLING_NOT_FOUND`。
- 一个企业版 Reflex Cloud 订阅和一个已登录的 Reflex CLI（`reflex login`）。

## 快速入门

从你的 Reflex 应用根目录：

```bash
reflex cloud deploy --gcp \
    --gcp-project my-gcp-project-id \
    --service-name my-reflex-app
```

CLI 将：

1. 对 Reflex Cloud 进行认证并获取部署清单（Dockerfile + `gcloud` 脚本）。
2. 生成一个将 Dockerfile 作为构建步骤嵌入的 `cloudbuild.yaml`，将其写入临时文件，并重写脚本的 `gcloud builds submit` 调用以使用 `--config="$REFLEX_CLOUDBUILD_YAML"`。
3. 打印（重写的）脚本以便你审查。
4. 请求确认，然后使用 `cwd=` 你的源目录运行脚本：启用所需的 API，创建 Artifact Registry 仓库，在 Cloud Build 上构建镜像（从 `cloudbuild.yaml` 中的构建步骤内实例化 Dockerfile），并部署一个公共 Cloud Run 服务。
5. 脚本完成后删除临时文件。

你的源树永远不会被写入——如果你在 `--source` 中有一个现有的 `Dockerfile`，它会保留在原处并被忽略。Reflex 提供的 Dockerfile 仅存在于 `cloudbuild.yaml` 临时文件内（以及 Cloud Build 作业内）。

完成后，你将获得一个类似 `https://my-reflex-app-<project-number>.us-central1.run.app` 的服务 URL。

## 选项

| 选项 | 默认值 | 描述 |
| --- | --- | --- |
| `--gcp` | _（必需）_ | 选择 GCP Cloud Run 目标。 |
| `--gcp-project` | _（必需）_ | 要部署到的 GCP **项目 ID**。`gcloud artifacts repositories` 不接受项目编号；使用项目 ID。 |
| `--region` | `us-central1` | Cloud Run 区域。 |
| `--service-name` | `reflex-app` | Cloud Run 服务名称。 |
| `--ar-repo` | `reflex` | Artifact Registry 仓库名称（首次部署时创建）。 |
| `--version` | UTC 时间戳（`YYYYMMDD-HHMMSS`） | 镜像版本标签。 |
| `--source` | `.` | 包含 Reflex 应用的目录。作为构建上下文上传到 Cloud Build；源树本身不会被修改。 |
| `--token` | _来自 `~/.reflex` 配置_ | Reflex 认证令牌。 |
| `--interactive / --no-interactive` | `--interactive` | 是否在运行部署脚本之前提示。 |
| `--dry-run` | _关闭_ | 打印清单、生成的 `cloudbuild.yaml` 和重写的脚本，而不写入临时文件或运行脚本。 |
| `--loglevel` | `info` | 日志详细程度。 |

## 在你的 GCP 项目中创建的内容

部署脚本启用这些 API（如果尚未启用）：

- `cloudbuild.googleapis.com`
- `run.googleapis.com`
- `artifactregistry.googleapis.com`

然后它（幂等地）创建并使用：

- 位于 `${REGION}-docker.pkg.dev/${GCP_PROJECT}/${AR_REPO}` 的 Artifact Registry Docker 仓库。
- 构建并推送镜像的 Cloud Build 作业。
- 名为 `${SERVICE_NAME}` 的 Cloud Run 服务，使用 `--allow-unauthenticated`、端口 8080、1 vCPU、1 GiB 内存、`--min-instances 1` 和 `--session-affinity` 部署。

重新运行命令会推送新的镜像标签并使 Cloud Run 服务向前滚动。

## 构建如何运行

生成的 `cloudbuild.yaml` 是一个单一的 Cloud Build 步骤，它：

1. 通过单引号 heredoc 将 Dockerfile 写入构建工作区：
    ```yaml
    - |
      cat > Dockerfile <<'REFLEX_DOCKERFILE_EOF'
      FROM python:3.13-slim
      ...
      REFLEX_DOCKERFILE_EOF
      docker build -t "$_IMAGE" .
      docker push "$_IMAGE"
    ```
2. 构建并推送镜像，使用 `_IMAGE` 标记（作为 `--substitutions=_IMAGE=...` 传递给 `gcloud builds submit`）。

由于 Cloud Build 对 `args` 运行自己的替换传递，Dockerfile 中的每个字面 `$` 在嵌入之前都会加倍为 `$$`（例如 `ENV PATH="${UV_PROJECT_ENVIRONMENT}/bin:$PATH"` 在 YAML 中变为 `ENV PATH="$${UV_PROJECT_ENVIRONMENT}/bin:$$PATH"`）。Cloud Build 的解析器在 bash 运行之前将 `$$` 转换回 `$`，因此写入工作区的 Dockerfile 包含原始字符。

## 安全模型

CLI 在**受限环境**下运行部署脚本。只有明确允许的主机变量白名单会转发给 `bash`——如 `PATH`、`HOME`、`CLOUDSDK_*`、`DOCKER_*` 和代理/TLS 变量。无关的主机密钥如 `AWS_*`、`GITHUB_TOKEN` 或任意用户变量**不会**被转发，因此被篡改或泄露的清单无法窃取它们。

你可以使用 `--dry-run` 在运行任何内容之前预览重写的脚本、生成的 `cloudbuild.yaml` 和 Dockerfile：

```bash
reflex cloud deploy --gcp \
    --gcp-project my-gcp-project-id \
    --dry-run
```

## 非交互式使用（CI）

对于自动化管道，传递 `--no-interactive` 和明确的 `--token`：

```bash
reflex cloud deploy --gcp \
    --gcp-project "$GCP_PROJECT_ID" \
    --service-name my-reflex-app \
    --token "$REFLEX_TOKEN" \
    --no-interactive
```

在非交互模式下，CLI 不会提示，如果无法解析令牌，它将以非零退出。

## 故障排除

**`Reflex denied the request (403). GCP Cloud Run deploys require an Enterprise tier subscription.`**
你的账户不在企业版上。联系 [sales@reflex.dev](mailto:sales@reflex.dev)。

**`Billing must be enabled for activation of service(s) ...`（`UREQ_PROJECT_BILLING_NOT_FOUND`）**
将计费账户附加到 GCP 项目，或使用不同的 `--gcp-project`。

**`The value of '--project' flag was set to Project number. To use this command, set it to PROJECT ID instead.`**
传递项目 ID（例如 `my-app-123456`），而不是数字项目编号。

**`No active GCP account found.`**
运行 `gcloud auth login` 和 `gcloud auth application-default login`。

**`The 'gcloud' / 'docker' / 'bash' CLI was not found on PATH.`**
安装缺失的工具并确保它在你调用 CLI 的 shell 的 `PATH` 上。

**`Dockerfile content contains the reserved heredoc marker 'REFLEX_DOCKERFILE_EOF'.`**
极不可能——来自 Reflex Cloud 的 Dockerfile 恰好包含一行与 CLI 用于嵌入它的 heredoc 终止符完全匹配的行。在下一个 CLI 版本后重新运行，或打开一个 issue。

**`Couldn't find 'gcloud builds submit' in the deploy script.`**
CLI 重写 Reflex 提供的部署脚本中的 `gcloud builds submit` 块以使用 `--config=`。如果 Reflex Cloud 在 CLI 更新以匹配之前更改了该脚本的形状，你将看到此错误——升级 `reflex-hosting-cli`（`uv tool upgrade reflex-hosting-cli` 或 `pip install -U reflex-hosting-cli`）。
