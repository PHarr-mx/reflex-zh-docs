# 自带云（Bring Your Own Cloud）

自带云（BYOC）让你可以使用你已经使用的相同 `reflex cloud deploy` 命令将 Reflex 应用部署到你自己的 **AWS**、**GCP** 或 **Azure** 账户。一切都在你的账户内运行：构建在你的云构建器上运行，镜像被推送到你的内部注册表，应用在你的云的托管运行时上运行。Reflex 永远不会持有你账户的常驻凭据，应用或其数据的任何内容都不会离开你的边界。

```md alert info
# 仅限企业版。

BYOC 是 Reflex Cloud **企业版**的一部分。联系 [sales@reflex.dev](mailto:sales@reflex.dev) 进行升级。
```

## 为什么选择 BYOC

Reflex Cloud 让你只需一个命令就可以从 Python 文件到实时应用，但该应用运行在 Reflex 的基础设施上——对于云由安全审查、采购或数据驻留策略固定的团队来说，这是不可接受的。如果部署目标未获批准，应用就无法上线。

另一种选择是在 Cloud Run、ECS 或 Container Apps 上自己构建部署：一个 Dockerfile、一个构建管道、一个注册表、运行时配置、基础设施即代码，以及持续维护。这本身就是一个工程项目，其中大部分与你的应用无关。

BYOC 保留了你的团队已经使用的 Reflex Cloud 工作流——应用生命周期、自动扩展、环境变量和部署 CLI 的行为都与以前一样。唯一改变的是容器最终运行的位置。

## 部署目标

相同的 `reflex cloud deploy` 命令可以通过单个标志针对每个云工作：

```bash
reflex cloud deploy --aws
reflex cloud deploy --gcp
reflex cloud deploy --azure
```

| 云 | 标志 | 托管运行时 |
| --- | --- | --- |
| AWS | `--aws` | ECS |
| GCP | `--gcp` | Cloud Run |
| Azure | `--azure` | Container Apps |

## 部署命令如何工作

运行 `reflex cloud deploy --<cloud>` 会启动一个简短的交互式流程：

1. **认证。** CLI 检查你的云自己的 CLI（`aws`、`gcloud` 或 `az`）是否已安装并且你已登录，如果你没有登录，会引导你完成 `aws configure`、`gcloud auth login` 或 `az login`。从那里开始，一切都在你的凭据下运行。
2. **配置。** 它拉取你应用的最新 Reflex Cloud 配置，并提示你输入它需要的任何标志。
3. **审查。** 在运行任何内容之前，它会打印它即将执行的确切构建和部署命令，并请求批准。
4. **部署。** 批准后，它使用你的云原生的构建器构建容器镜像，将其推送到你的内部工件注册表，并将应用部署到托管运行时。

最终输出是实时应用的 URL，在你自己的账户中运行。整个流程第一次大约需要三分钟，后续部署不到一分钟。

## 当前范围内的内容

BYOC 在 AWS、GCP 和 Azure 上对 Reflex 企业客户普遍可用。它支持：

- Cloud Run、ECS 和 Container Apps 上的 Reflex 应用
- 通过你现有的云 CLI 进行认证
- 标准的 Reflex Cloud 配置标志集

## 后续步骤

- 有关 GCP 目标的详细演练——前提条件、选项、安全模型和故障排除——请参阅[部署到 GCP Cloud Run](/docs/hosting/deploy-to-gcp/)。
- 如果你是 Reflex 企业版用户，更新 CLI 并运行 `reflex cloud deploy --<cloud>`。
- 如果你不是，请参阅 [reflex.dev/pricing](https://reflex.dev/pricing/) 了解比较和演示链接。
