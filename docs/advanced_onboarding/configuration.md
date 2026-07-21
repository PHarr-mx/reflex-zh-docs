
# 配置

Reflex 应用可以通过配置文件、环境变量和命令行参数进行配置。

## 配置文件

运行 `uv run reflex init` 会在项目根目录创建 `rxconfig.py` 文件。
你可以向 `Config` 类传递关键字参数来配置你的应用。

例如：

```python
# rxconfig.py
import reflex as rx

config = rx.Config(
    app_name="my_app_name",
    # Connect to your own database.
    db_url="postgresql://user:password@localhost:5432/my_db",
    # Change the frontend port.
    frontend_port=3001,
)
```

所有可用参数请参见[配置参考](https://reflex.dev/docs/api-reference/config/)。

## 环境变量

你可以通过设置环境变量来覆盖配置文件。
例如，要覆盖 `frontend_port` 设置，可以设置 `FRONTEND_PORT` 环境变量。

```bash
FRONTEND_PORT=3001 uv run reflex run
```

## 命令行参数

最后，你可以通过向 `uv run reflex run` 传递命令行参数来覆盖配置文件和环境变量。

```bash
uv run reflex run --frontend-port 3001
```

所有可用参数请参见 [CLI 参考](/docs/api-reference/cli)。

## 自定义应用数据目录

可以设置 `REFLEX_DIR` 环境变量，允许用户指定 Reflex 写入 Bun 和 NodeJS 等辅助工具的位置。

默认情况下，我们使用平台特定的目录：

在 Windows 上，使用 `C:/Users/<username>/AppData/Local/reflex`。

在 macOS 上，使用 `~/Library/Application Support/reflex`。

在 Linux 上，使用 `~/.local/share/reflex`。
