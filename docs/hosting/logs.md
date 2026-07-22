```python exec
import reflex as rx
```

# 日志（Logs）

## 查看日志

要查看应用日志，请按照下图中的箭头操作，然后点击 `Logs` 下拉菜单。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/view_logs.webp",
    alt="Viewing app logs in Reflex Cloud",
    padding_bottom="20px",
)
```

```md alert info
# 查看日志的 CLI 命令
`reflex cloud apps logs [OPTIONS] [APP_ID]`
```

## 查看部署日志和部署历史

要查看部署历史，请按照下图中的箭头操作，然后点击 `Deployments`。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/view_deployment_logs.webp",
    alt="Viewing deployment logs in Reflex Cloud",
)
```

这将带你到下面的页面，你可以在其中查看应用的部署历史。点击你想进一步探索的部署。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/view_deployment_logs_2.webp",
    alt="Deployment logs and history in Reflex Cloud",
    padding_bottom="20px",
)
```

```md alert info
# 查看部署历史的 CLI 命令
`reflex cloud apps history [OPTIONS] [APP_ID]`
```

这将带你到下面的页面，你可以通过点击 `Build logs` 下拉菜单查看应用的部署日志。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/view_deployment_logs_3.webp",
    alt="CLI command to view deployment history",
)
```
