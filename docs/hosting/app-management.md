```python exec
import reflex as rx
```

# 应用（App）

在 Reflex Cloud 中，"应用"（或"应用程序"或"网站"）指的是使用 Reflex 框架构建的 Web 应用程序，可以在 Cloud 平台内部署和管理。

你可以使用 `reflex deploy` 命令部署应用。

你可以在 Cloud UI 中执行许多操作来管理你的应用。以下是你可能想要执行的一些最常见操作。


## 停止应用

要停止应用，请按照下图中的箭头操作，然后点击 `Stop app` 按钮。暂停应用将停止其运行，并且在你恢复之前用户将无法访问。此外，这将停止对你的应用计费。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/stopping_app.webp",
    alt="Stopping an app in Reflex Cloud",
    padding_bottom="20px",
)
```

```md alert info
# 停止应用的 CLI 命令
`reflex cloud apps stop [OPTIONS] [APP_ID]`
```

## 删除应用

要删除应用，点击应用页面 Cloud UI 中的 `Settings` 选项卡。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/environment_variables.webp",
    alt="App environment variables in Reflex Cloud",
)
```

然后点击如下所示的 `Danger` 选项卡。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/deleting_app.webp",
    alt="Deleting an app in Reflex Cloud",
)
```

这里有一个 `Delete app` 按钮。按下此按钮将删除应用及其所有数据。此操作不可逆。

```md alert info
# 删除应用的 CLI 命令
`reflex cloud apps delete [OPTIONS] [APP_ID]`
```


## 其他应用设置

点击应用页面 Cloud UI 中的 `Settings` 选项卡还允许用户更改`应用名称`、更改`应用描述`和查看`应用 ID`。

其他应用设置还允许用户编辑和添加应用的密钥（环境变量）。有关密钥的更多信息，请参阅[密钥（环境变量）](/docs/hosting/secrets-environment-vars/)页面。
