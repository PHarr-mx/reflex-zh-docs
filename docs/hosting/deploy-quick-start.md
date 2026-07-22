# Reflex Cloud - 快速入门

```python exec
import reflex as rx
```

到目前为止，我们一直在自己的机器上本地运行应用。
但是如果我们想与世界分享我们的应用呢？这就是
托管服务的用武之地。

## 快速入门

Reflex 的托管服务使部署应用变得容易，无需担心配置基础设施。

### 前提条件

1. 托管服务需要 `reflex>=0.6.6`。
2. 本教程假设你已成功 `reflex init` 和 `reflex run` 你的应用。
3. 还要确保你在顶级应用目录中有一个 `requirements.txt` 文件，其中包含所有 Python 依赖项！（要创建 `requirements.txt` 文件，运行 `pip freeze > requirements.txt`。）


### 认证

首先运行以下命令登录/注册你的 Reflex Cloud 账户：（命令行）

```bash
reflex login
```

你将被重定向到浏览器，在那里你可以通过 Github 或 Gmail 进行认证。

### Web UI

当你到达此 URL 并成功认证后，点击你工作区中的一个项目。你应该会得到如下屏幕：

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/cloud_project_page.webp",
    alt="Reflex Cloud Dashboard",
)
```

此屏幕显示登录命令和部署命令。由于我们已经登录，可以跳过登录命令。

### 部署

现在你可以开始部署你的应用了。

在你的 Cloud UI 中复制类似于下面显示的 `reflex deploy` 命令。

```bash
reflex deploy --project 2a432b8f-2605-4753-####-####0cd1####
```

在你的项目目录中（你通常运行 `reflex run` 的地方）粘贴此命令。

该命令默认是交互式的。它会问你几个部署所需信息的问题。


1. 第一个问题将比较你的 `requirements.txt` 与你的 Python 环境，如果它们不同，它会询问你是否要更新 `requirements.txt` 或继续使用当前的。如果它们相同，则不会出现此问题。要创建 `requirements.txt` 文件，运行 `pip freeze > requirements.txt`。
2. 第二个问题将搜索与你当前应用同名的已部署应用，如果找不到，它会询问你是否要继续部署你的新应用。
3. 第三个问题是可选的，会询问你应用描述。


就是这样！你应该会收到一些关于部署进度的反馈，几分钟后你的应用应该就上线了。🎉

有关部署命令及其选项的详细信息，请参阅[部署 API 参考](/docs/hosting/deploy-quick-start/)和 [CLI 参考](https://reflex.dev/docs/api-reference/cli/)。


```md alert info
# 代码上传后，托管服务将开始部署。完成上传后，退出命令**不会**影响部署过程。当你可以安全关闭它而不影响部署时，该命令会打印一条消息。
```

如果你回到 Cloud UI，你应该能够看到你部署的应用和其他有用的应用信息。


```md alert info
# 设置 Cloud 配置文件
要为你的应用创建 `config.yml` 文件以设置应用配置，请查看 [Cloud 配置文档](/docs/hosting/config-file/)。
```

```md alert info
# 在 Cloud UI 中导航
要返回，即从应用到项目或从项目到项目列表，你只需点击页面左上角的 `REFLEX 标志`。
```

```md alert info
# 所有标志值在运行之间保存
你所有的标志值，即环境变量或区域或令牌，在运行之间保存。这意味着如果你运行一个命令并传递一个标志值，下次你运行相同的命令时，标志值将与你上次运行时相同。这意味着你只应在想要更改标志值时再次设置它们。
```
