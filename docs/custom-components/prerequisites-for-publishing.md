# Python 包索引

```python exec
import reflex as rx

image_style = {
    "width": "400px",
    "border_radius": "12px",
    "border": "1px solid var(--secondary-5)",
}
```

要发布 Python 包，你需要使用发布工具。任何工具都可以，但我们推荐 [Twine](https://twine.readthedocs.io/en/stable/) 或 [uv](https://docs.astral.sh/uv/guides/package/#publishing-your-package)。

## PyPI

在 PyPI 上创建账户和 API 令牌很简单。[PyPI 网站](https://pypi.org/help/)上有官方帮助。有关快速参考，请转到 PyPI 网站的右上角，查找注册按钮并填写个人信息。

```python eval
rx.center(
    rx.image(
        src="https://web.reflex-assets.dev/custom_components/pypi_register.webp",
        alt="PyPI account registration page",
        style=image_style,
        margin_bottom="16px",
        loading="lazy",
    ),
)
```

用户可以在发布时使用用户名和密码向 PyPI 进行身份验证。

```python eval
rx.center(
    rx.image(
        src="https://web.reflex-assets.dev/custom_components/pypi_account_settings.webp",
        alt="PyPI account settings page",
        style=image_style,
        margin_bottom="16px",
        loading="lazy",
    ),
)
```

向下滚动到 API 令牌部分，点击"Add API token"按钮。填写表单并点击"Generate API token"。

```python eval
rx.center(
    rx.image(
        src="https://web.reflex-assets.dev/custom_components/pypi_api_tokens.webp",
        alt="PyPI API tokens settings page",
        style=image_style,
        width="700px",
        loading="lazy",
    ),
)
```
