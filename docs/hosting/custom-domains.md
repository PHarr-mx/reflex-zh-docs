```python exec
import reflex as rx
```

# 自定义域名（Custom Domains）


使用 Reflex Cloud 的企业版，你可以使用自己的自定义域名来托管你的应用。

## 前提条件

你必须从域名注册商（如 GoDaddy、Cloudflare、Namecheap 或 AWS）购买域名。

在本教程中，我们将使用 GoDaddy 和示例域名 `tomgotsman.us`。


## 步骤

购买域名后，你可以按照以下步骤将其添加到你的 Reflex Cloud 应用：

1 - 确保你已将应用部署到 Reflex Cloud。

2 - 应用部署后，点击 `Custom Domain` 选项卡，将你的自定义域名添加到输入字段中，然后按 Add domain 按钮。你现在应该看到如下页面：

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/custom-domains-DNS-inputs.webp",
    alt="Custom domain DNS record inputs",
)
```

```python eval
rx.box(height="20px")
```

3 - 在域名注册商的网站上，导航到你域名的 DNS 设置。它应该看起来像下面的图片：

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/custom-domains-DNS-before.webp",
    alt="DNS settings before adding custom domain records",
)
```

```python eval
rx.box(height="20px")
```

4 - 将 Reflex Cloud 提供的所有四个 DNS 记录添加到你的域名注册商的 DNS 设置中。如果已经有一个 A 名称记录，删除它并替换为 Reflex Cloud 提供的记录。你的 DNS 设置应该看起来像下面的图片：

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/custom-domains-DNS-after.webp",
    alt="DNS settings after adding custom domain records",
)
```

```md alert warning
# 它可能会警告你此记录将在 ######.tomgotsman.us.tomgotsman.us 上解析。
如果发生这种情况，请确保选择仅在 ######.tomgotsman.us 上解析记录。
```

```md alert warning
# 你的域名提供商可能不支持 Apex CNAME 记录，在这种情况下只需使用 A 记录。
![显示 CNAME 记录失败的图片](/custom-domains-CNAME-fail.png)
```

```python eval
rx.box(height="20px")
```

5 - 添加 DNS 记录后，刷新 Reflex Cloud 页面上的页面（可能需要几分钟到几小时才能成功更新）。如果记录正确，你应该看到如下所示的成功消息：

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/custom-domains-success.webp",
    alt="Custom domain successfully configured",
)
```

```python eval
rx.box(height="20px")
```

6 - 现在使用 `reflex deploy` 命令重新部署你的应用，你的应用现在应该在你的自定义域名上线了！
