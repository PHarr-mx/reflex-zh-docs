```python exec
import reflex as rx
```

# 自托管（Self Hosting）

我们推荐使用 `reflex deploy`，但如果这不适合你的用例，你也可以自己托管应用。

将你的代码克隆到服务器并安装[要求](/docs/getting-started/installation/)。

## API URL

编辑你的 `rxconfig.py` 文件并将 `api_url` 设置为你服务器可公开访问的 IP
地址或主机名，末尾带有端口 `:8000`。正确设置
这对于前端与后端状态交互至关重要。

例如，如果你的服务器在 `app.example.com`，你的配置将如下所示：

```python
config = rx.Config(
    app_name="your_app_name",
    api_url="http://app.example.com:8000",
)
```

也可以在运行时或导出时设置环境变量 `API_URL`，以保留本地开发的默认值。

## 代理到子路径

如果你想在反向代理后面的子路径上提供后端（例如
nginx 将 `/api/*` 路由到 Reflex），请在配置上设置 `backend_path` 而不是
将前缀嵌入 `api_url`。每个后端端点（事件 websocket、
`/ping`、`/_upload`、`/_health`、`/_all_routes`）都挂载在该前缀下，
并且导出中嵌入的前端会自动在前缀 URL 上调用后端——代理中不需要请求重写。

```python
config = rx.Config(
    app_name="your_app_name",
    api_url="http://app.example.com:8000",
    backend_path="/api",
)
```

`frontend_path` 对前端起类似作用，两者是独立的。

注意：更改 `backend_path`（或 `frontend_path`）需要完全重启
`reflex run`——路由和挂载点在启动时注册，因此仅热重载
不会移动它们。

## 生产模式

然后以生产模式运行你的应用：

```bash
reflex run --env prod
```

生产模式创建应用的优化构建。默认情况下，应用的静态
前端（HTML、Javascript、CSS）将在端口 `3000` 上暴露，
后端（事件处理程序）将在端口 `8000` 上监听。

```md alert warning
# 反向代理和 Websockets
由于后端使用 websockets，一些反向代理服务器，如 [nginx](https://nginx.org/en/docs/http/websocket.html) 或 [apache](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#protoupgrade)，必须配置为传递 `Upgrade` 头以允许后端连接。
```

## 导出静态构建

导出前端的静态构建允许使用
静态托管提供商（如 Netlify 或 Github Pages）提供应用。确保在导出前端时将 `api_url` 设置为
可访问的后端 URL。

```bash
API_URL=http://app.example.com:8000 reflex export
```

这将创建一个 `frontend.zip` 文件，其中包含你应用的最小化 HTML、
Javascript 和 CSS 构建，可以上传到你的静态托管服务。

它还创建一个 `backend.zip` 文件，其中包含你应用的后端 Python 代码，
用于上传到你的服务器并运行。

你可以通过传递 `--frontend-only`
或 `--backend-only` 标志仅导出前端或后端。

也可以在不压缩的情况下导出组件。为此，
使用 `--no-zip` 参数。这在
`.web/build/client/` 目录中提供前端，后端可以在项目的根目录中找到。

## Reflex 容器服务

另一个选择是在容器中运行你的 Reflex 服务。为此
目的，Reflex 项目的 `docker-example` 目录中提供了 `Dockerfile` 和额外文档。

对于容器镜像的构建，需要编辑 `rxconfig.py`
并将 `requirements.txt` 添加到你的项目文件夹。`rxconfig.py` 中需要进行以下更改：

```python
config = rx.Config(
    app_name="app",
    api_url="http://app.example.com:8000",
)
```

注意 `api_url` 应设置为外部可访问的主机名或
IP，因为客户端浏览器必须能够直接连接到它以建立
交互性。

你可以在项目的 `docker-example` 文件夹中找到 `requirements.txt`。

项目结构应该如下所示：

```bash
hello
├── .web
├── assets
├── hello
│   ├── __init__.py
│   └── hello.py
├── rxconfig.py
├── Dockerfile
└── requirements.txt
```

完成所有更改后，现在可以按如下方式创建容器镜像。

```bash
docker build -t reflex-project:latest .
```

最后，你可以按如下方式启动你的 Reflex 容器服务。

```bash
docker run -d -p 3000:3000 -p 8000:8000 --name app reflex-project:latest
```
