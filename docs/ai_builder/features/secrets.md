# 密钥（Secrets）

**密钥（Secrets）**功能允许你安全地存储应用可以使用的环境特定值，如 API 密钥、令牌或其他敏感信息。


```python exec
import reflex as rx


def render_image():
    return rx.el.div(
        rx.image(
            src="https://web.reflex-assets.dev/ai_builder/features/secrets_light.avif",
            alt="Adding secrets in Reflex AI Builder",
            class_name="rounded-md h-auto",
            border=f"0.81px solid {rx.color('slate', 5)}",
        ),
        class_name="w-full flex flex-col rounded-md",
    )
```

```python eval
rx.el.div(render_image())
```

## 添加密钥

### 1. 单独添加
- **描述：** 通过提供键和值来设置单个密钥。
- **示例：**
  - 键：`OPENAI_API_KEY`
  - 值：`sk-xxxxxx`
- **行为：** 密钥经过加密，你的应用在运行时可以访问它。

### 2. 批量添加（原始编辑器）
- **描述：** 使用简单的 `VAR=VALUE` 格式一次上传多个密钥。
- **示例：**

```text
DATABASE_URL=postgresql://user:pass@host:5432/db
STRIPE_SECRET_KEY=sk_test_xxxxx
OPENAI_API_KEY=sk-xxxxxx
```

```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/features/secret_bulk_light.avif",
        alt="Adding secrets in bulk with the raw editor",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```


- **行为：** 每个密钥都经过安全存储，并可在应用环境中立即使用。
