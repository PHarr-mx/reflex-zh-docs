```python exec
import reflex as rx
```

# 令牌（Tokens）

令牌赋予他人与你作为用户或管理员相同的所有权限。他们可以使用 `--token` 标志从 CLI 运行任何 Reflex Cloud 命令，就好像他们是你一样。一个常见的用例是 GitHub Actions（你将此令牌存储在你的密钥中）。

要访问或创建令牌，首先点击右上角的头像打开下拉菜单，然后点击 `Account Settings`。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/hosting_tokens_1.webp",
    alt="Adding tokens to Reflex Cloud",
    padding="1em 0em",
)
```

点击 `Account Settings` 将重定向你到 `Settings` 和 `Tokens` 仪表板。点击顶部的 `Tokens` 选项卡以访问你的令牌或创建新令牌。


```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/hosting_tokens_2.webp",
    alt="Adding tokens to Reflex Cloud",
    padding="1em 0em",
)
```
