```python exec
import reflex as rx
```

# 密钥（环境变量）


## 通过 CLI 添加密钥

以下是如何使用环境变量文件的示例。你可以传递带有 env 文件路径的 `--envfile` 标志。例如：

```bash
reflex deploy --project f88b1574-f101-####-####-5f########## --envfile .env
```

在此示例中，文件路径为 `.env`。


如果你更喜欢手动传递环境变量，以下是部署命令示例：

```bash
reflex deploy --project f88b1574-f101-####-####-5f########## --env OPENAI_API_KEY=sk-proj-vD4i9t6U############################
```

它们在 `--env` 标志后作为键值对传递。

要传递多个环境变量，你可以重复 `--env` 标签。即 `reflex deploy --project f88b1574-f101-####-####-5f########## --env KEY1=VALUE1 --env KEY2=VALUE`。`--envfile` 标志将覆盖任何手动设置的环境变量。


```md alert info
# 有关环境变量的更多信息
环境变量经过加密并安全存储。我们建议将后端 API 密钥或密钥作为 `envs` 输入。确保输入 `envs` 时不带任何引号。我们不会在任何 CLI 命令中显示它们的值，只显示它们的名称（或键）。

你通过在应用后端中使用它们的名称作为键引用 `os.environ` 来访问 `envs` 的值。例如，如果你设置了一个环境变量 `ASYNC_DB_URL`，你可以通过 `os.environ["ASYNC_DB_URL"]` 访问它。一些 Python 库会自动查找某些环境变量。例如，`openai` Python 客户端的 `OPENAI_API_KEY`。`boto3` 客户端凭据可以通过设置 `AWS_ACCESS_KEY_ID`、`AWS_SECRET_ACCESS_KEY` 来配置。此信息通常可在你使用的 Python 包的文档中找到。
```

## 通过 Cloud UI 添加密钥

要找到密钥选项卡，点击应用页面 Cloud UI 中的 `Settings` 选项卡。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/environment_variables.webp",
    alt="Environment variables panel in Reflex Cloud UI",
)
```

然后点击如下所示的 `Secrets` 选项卡。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/environment_variables_2.webp",
    alt="Adding environment variables in Reflex Cloud UI",
)
```

从这里你可以添加或编辑你的环境变量。你需要重启应用才能使这些更改生效。

UI 中的此功能可以由项目管理员禁用。
