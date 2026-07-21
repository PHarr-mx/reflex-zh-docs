```python exec
import reflex as rx
```

# API 转换器

除了你的前端应用之外，Reflex 还使用 FastAPI 后端来为你的应用提供服务。API 转换器（API transformer）功能允许你转换或扩展为 Reflex 应用提供服务的 ASGI 应用。

## 概述

API 转换器提供了一种方式：

1. 将现有的 FastAPI 或 Starlette 应用与你的 Reflex 应用集成
2. 对 ASGI 应用应用中间件或转换
3. 用额外的 API 端点扩展你的 Reflex 应用

这对于创建可用于 Reflex 应用之外的后端 API，或者将 Reflex 与现有后端服务集成非常有用。

## 使用 API 转换器

你可以在初始化 Reflex 应用时设置 `api_transformer` 参数：

```python
import reflex as rx
from fastapi import FastAPI, Depends
from fastapi.security import OAuth2PasswordBearer

# Create a FastAPI app
fastapi_app = FastAPI(title="My API")


# Add routes to the FastAPI app
@fastapi_app.get("/api/items")
async def get_items():
    return dict(items=["Item1", "Item2", "Item3"])


# Create a Reflex app with the FastAPI app as the API transformer
app = rx.App(api_transformer=fastapi_app)
```

## API 转换器的类型

`api_transformer` 参数可以接受：

1. 一个 Starlette 或 FastAPI 实例
2. 一个可调用对象，接收 ASGIApp 并返回 ASGIApp
3. 以上类型的序列

### 使用 FastAPI 或 Starlette 实例

当你提供一个 FastAPI 或 Starlette 实例作为 API 转换器时，Reflex 会将其内部 API 挂载到你的应用中，允许你定义额外的路由：

```python
import reflex as rx
from fastapi import FastAPI, Depends
from fastapi.security import OAuth2PasswordBearer

# Create a FastAPI app with authentication
fastapi_app = FastAPI(title="Secure API")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")


# Add a protected route
@fastapi_app.get("/api/protected")
async def protected_route(token: str = Depends(oauth2_scheme)):
    return dict(message="This is a protected endpoint")


# Create a token endpoint
@fastapi_app.post("/token")
async def login(username: str, password: str):
    # In a real app, you would validate credentials
    if username == "user" and password == "password":
        return dict(access_token="example_token", token_type="bearer")
    return dict(error="Invalid credentials")


# Create a Reflex app with the FastAPI app as the API transformer
app = rx.App(api_transformer=fastapi_app)
```

### 使用可调用转换器

你也可以提供一个转换 ASGI 应用的可调用对象：

```python
import reflex as rx
from starlette.middleware.cors import CORSMiddleware


# Create a transformer function that returns a transformed ASGI app
def add_cors_middleware(app):
    # Wrap the app with CORS middleware and return the wrapped app
    return CORSMiddleware(
        app=app,
        allow_origins=["https://example.com"],
        allow_methods=["*"],
        allow_headers=["*"],
    )


# Create a Reflex app with the transformer
app = rx.App(api_transformer=add_cors_middleware)
```

### 使用多个转换器

你可以通过提供一个序列来应用多个转换器：

```python
import reflex as rx
from fastapi import FastAPI
from starlette.middleware import Middleware
from starlette.middleware.cors import CORSMiddleware

# Create a FastAPI app
fastapi_app = FastAPI(title="My API")


# Add routes to the FastAPI app
@fastapi_app.get("/api/items")
async def get_items():
    return dict(items=["Item1", "Item2", "Item3"])


# Create a transformer function
def add_logging_middleware(app):
    # This is a simple example middleware that logs requests
    async def middleware(scope, receive, send):
        # Log the request path
        path = scope["path"]
        print("Request:", path)
        await app(scope, receive, send)

    return middleware


# Create a Reflex app with multiple transformers
app = rx.App(api_transformer=[fastapi_app, add_logging_middleware])
```

## 保留路由

后端上的一些路由是保留给 Reflex 运行时使用的，除非你清楚自己在做什么，否则不应覆盖它们。

### Ping

`localhost:8000/ping/`：你可以使用此路由来检查后端的健康状态。

预期返回值为 `"pong"`。

### Event

`localhost:8000/_event`：前端将使用此路由通知后端发生了某个事件。

```md alert error
# 覆盖此路由将破坏事件通信
```

### Upload

`localhost:8000/_upload`：此路由用于在使用 `rx.upload()` 时的文件上传，包括 `rx.upload_files(...)` 和 `rx.upload_files_chunk(...)`。
