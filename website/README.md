# Reflex 文档

## 快速开始

1. 安装依赖：

```bash
uv sync
```

2. 运行开发服务器：

```bash
uv run reflex run
```

3. 打开 [http://localhost:3000/docs/](http://localhost:3000/docs/) 查看运行中的应用。

## 编辑文档

Markdown 文档位于仓库根目录的 `docs/` 目录中。编辑其中任何 `.md` 文件，开发服务器会自动获取更改，你可以在应用中实时预览。

## 页面白名单（加速开发构建）

默认情况下，开发服务器会编译**所有**页面，这可能很慢。为了加快速度，你可以只白名单你正在工作的页面，这样只有这些页面会被编译。

编辑 `reflex_docs/whitelist.py` 并将路径添加到 `WHITELISTED_PAGES` 列表中。路径是**应用路由**（相对于 `frontend_path`，在 `rxconfig.py` 中默认为 `/docs`）。不要在白名单中重复 `/docs` 挂载段，否则不会匹配任何内容。

```python
WHITELISTED_PAGES = [
    "/getting-started/introduction",
    "/components/props",
]
```

**规则：**
- 每个路径必须以正斜杠 `/` 开头。
- **不要**包含尾部斜杠（例如 `/getting-started/introduction`，而不是 `/getting-started/introduction/`）。
- 空列表（`[]`）会构建所有页面（默认行为）。
- 路径是前缀匹配的，因此 `"/components"` 将包含该部分下的所有页面。

编辑白名单后，重启开发服务器以使更改生效。
