```python exec box
import reflex as rx

cell_style = {
    "font_family": "Instrument Sans",
    "font_style": "normal",
    "font_weight": "500",
    "font_size": "14px",
    "line_height": "1.5",
    "letter_spacing": "-0.0125em",
    "color": "var(--secondary-11)",
}


class RouterState(rx.State):
    pass


router_data = [
    {"name": "rx.State.router.url", "value": RouterState.router.url},
    {"name": "rx.State.router.url.scheme", "value": RouterState.router.url.scheme},
    {"name": "rx.State.router.url.netloc", "value": RouterState.router.url.netloc},
    {"name": "rx.State.router.url.origin", "value": RouterState.router.url.origin},
    {"name": "rx.State.router.url.path", "value": RouterState.router.url.path},
    {"name": "rx.State.router.url.query", "value": RouterState.router.url.query},
    {
        "name": "rx.State.router.url.query_parameters",
        "value": RouterState.router.url.query_parameters.to_string(),
    },
    {"name": "rx.State.router.url.fragment", "value": RouterState.router.url.fragment},
    {"name": "rx.State.router.route_id", "value": RouterState.router.route_id},
    {
        "name": "rx.State.router.session.client_token",
        "value": RouterState.router.session.client_token,
    },
    {
        "name": "rx.State.router.session.session_id",
        "value": RouterState.router.session.session_id,
    },
    {
        "name": "rx.State.router.session.client_ip",
        "value": RouterState.router.session.client_ip,
    },
    {"name": "rx.State.router.headers.host", "value": RouterState.router.headers.host},
    {
        "name": "rx.State.router.headers.origin",
        "value": RouterState.router.headers.origin,
    },
    {
        "name": "rx.State.router.headers.upgrade",
        "value": RouterState.router.headers.upgrade,
    },
    {
        "name": "rx.State.router.headers.connection",
        "value": RouterState.router.headers.connection,
    },
    {
        "name": "rx.State.router.headers.cookie",
        "value": RouterState.router.headers.cookie,
    },
    {
        "name": "rx.State.router.headers.pragma",
        "value": RouterState.router.headers.pragma,
    },
    {
        "name": "rx.State.router.headers.cache_control",
        "value": RouterState.router.headers.cache_control,
    },
    {
        "name": "rx.State.router.headers.user_agent",
        "value": RouterState.router.headers.user_agent,
    },
    {
        "name": "rx.State.router.headers.sec_websocket_version",
        "value": RouterState.router.headers.sec_websocket_version,
    },
    {
        "name": "rx.State.router.headers.sec_websocket_key",
        "value": RouterState.router.headers.sec_websocket_key,
    },
    {
        "name": "rx.State.router.headers.sec_websocket_extensions",
        "value": RouterState.router.headers.sec_websocket_extensions,
    },
    {
        "name": "rx.State.router.headers.accept_encoding",
        "value": RouterState.router.headers.accept_encoding,
    },
    {
        "name": "rx.State.router.headers.accept_language",
        "value": RouterState.router.headers.accept_language,
    },
    {
        "name": "rx.State.router.headers.raw_headers",
        "value": RouterState.router.headers.raw_headers.to_string(),
    },
]
```

# 状态实用方法

状态对象有几个方法和属性，用于返回关于当前页面、会话或状态的信息。

## 路由属性（Router Attributes）

`self.router` 属性有几个子属性，提供各种信息：

- `router.url`：当前页面的 URL，解析为其各个组成部分（参见下面的 [URL 属性](#url-attributes)）。
- `router.route_id`：与当前请求匹配的路由模式（例如 `/posts/[id]`）。对于[动态页面](/docs/pages/dynamic-routing)，这包含 slug 而非用于加载页面的实际值。

- `router.session`：关于当前会话的数据
  - `client_token`：与当前选项卡的 token 关联的 UUID。每个选项卡都有唯一的 token。
  - `session_id`：与客户端 WebSocket 连接关联的 ID。每个选项卡都有唯一的会话 ID。
  - `client_ip`：客户端的 IP 地址。多个用户可能共享同一个 IP 地址。

- `router.headers`：与 WebSocket 连接关联的头部信息。这些值只能在 WebSocket 重新建立时更改（例如，在页面刷新时）。
  - `host`：提供 WebSocket 服务的主机名和端口（后端）。
  - `origin`：请求的来源。
  - `upgrade`：WebSocket 连接的 upgrade 头部。
  - `connection`：connection 头部。
  - `cookie`：cookie 头部。
  - `pragma`：pragma 头部。
  - `cache_control`：cache control 头部。
  - `user_agent`：客户端的 user agent 字符串。
  - `sec_websocket_version`：WebSocket 版本。
  - `sec_websocket_key`：WebSocket 密钥。
  - `sec_websocket_extensions`：WebSocket 扩展。
  - `accept_encoding`：接受的编码方式。
  - `accept_language`：接受的语言。
  - `raw_headers`：所有 HTTP 头部作为冻结字典（frozen dictionary）的映射。这提供了对请求中发送的任何头部的访问，而不仅仅是上面列出的常见头部。

## URL 属性

`self.router.url` 是浏览器中当前显示的页面的完整 URL，使用 Python 标准库的 `urllib.parse.urlsplit` 解析为其组成部分。它是一个字符串子类，因此可以在任何期望字符串的地方使用（例如，传递给 `rx.text(self.router.url)` 来渲染整个 URL），此外还暴露了以下属性：

- `scheme`：URL 方案（例如 `"http"` 或 `"https"`）。
- `netloc`：网络位置，包括主机名和可选端口（例如 `"example.com:3000"`）。
- `origin`：方案和网络位置组合在一起（例如 `"https://example.com:3000"`）。相当于 `f"{scheme}://{netloc}"`。
- `path`：浏览器中显示的 URL 路径，包括任何已填充的动态段，但不包括查询字符串和片段（例如 `"/posts/123"`）。
- `query`：原始查询字符串，不带前导 `?`（例如 `"tab=comments&sort=new"`）。
- `query_parameters`：解析为冻结的、不可变的 `Mapping[str, str]` 的查询字符串。使用此属性而不是手动解析 `query`。
- `fragment`：URL 片段，不带前导 `#`（例如 `"section-2"`）。客户端路由器通过 WebSocket 发送当前片段，因此这反映了浏览器 URL 栏中显示的内容。

### 示例

对于匹配路由 `/posts/[id]` 的请求 `https://example.com:3000/posts/123?tab=comments#top`：

| 属性                                | 值                                                    |
| :----------------------------------- | :----------------------------------------------------- |
| `self.router.url`                    | `"https://example.com:3000/posts/123?tab=comments#top"`|
| `self.router.url.scheme`             | `"https"`                                              |
| `self.router.url.netloc`             | `"example.com:3000"`                                   |
| `self.router.url.origin`             | `"https://example.com:3000"`                           |
| `self.router.url.path`               | `"/posts/123"`                                         |
| `self.router.url.query`              | `"tab=comments"`                                       |
| `self.router.url.query_parameters`   | `{"tab": "comments"}`                                  |
| `self.router.url.fragment`           | `"top"`                                                |
| `self.router.route_id`               | `"/posts/[id]"`                                        |

### 读取查询参数

`query_parameters` 是从查询字符串中读取值的推荐方式。它是一个冻结映射（不可变且可哈希），因此在 `@rx.var` 计算变量（computed vars）和事件处理程序（event handlers）内部使用是安全的：

```python
class State(rx.State):
    @rx.var
    def selected_tab(self) -> str:
        return self.router.url.query_parameters.get("tab", "overview")

    def on_load(self):
        page = self.router.url.query_parameters.get("page", "1")
        # ... 加载该页面的适当数据 ...
```

对于如 `[id]` 或 `[[...splat]]` 这样的动态路径段，请参见[动态路由](/docs/pages/dynamic-routing) — 这些值作为状态变量暴露在根状态上（例如 `rx.State.id`、`rx.State.splat`），而不是通过 `router.url`。

## 从 `router.page` 迁移

`self.router.page` 命名空间自 Reflex 0.8.1 起已弃用，将在 1.0 中移除。其功能现在由 `self.router.url` 和 `self.router.route_id` 共同提供。使用下表更新现有代码：

| 已弃用                              | 替代方案                                                     | 说明                                                                 |
| :---------------------------------- | :------------------------------------------------------------ | :-------------------------------------------------------------------- |
| `self.router.page.path`             | `self.router.route_id`                                        | 路由模式，例如 `/posts/[id]`。                                |
| `self.router.page.raw_path`         | `self.router.url.path`                                        | 浏览器中的实际路径。如果还需要查询参数，附加 `?{url.query}`。 |
| `self.router.page.full_path`        | `f"{self.router.url.origin}{self.router.route_id}"`           | 路由模式前加上来源（origin）。很少需要。                |
| `self.router.page.full_raw_path`    | `self.router.url`                                             | `router.url` 本身就是完整的 URL 字符串。                      |
| `self.router.page.host`             | `self.router.url.origin`                                      | 包含方案的完整来源（例如 `"http://localhost:3000"`）。仅需 `host:port` 时使用 `self.router.url.netloc`。 |
| `self.router.page.params`           | `self.router.url.query_parameters`                            | 现在是冻结映射而非普通字典。                        |

### 当前页面的示例值

```python eval
rx.table.root(
    rx.table.header(
        rx.table.row(
            rx.table.column_header_cell("Name"),
            rx.table.column_header_cell("Value"),
        ),
    ),
    rx.table.body(*[
        rx.table.row(
            rx.table.cell(item["name"], style=cell_style),
            rx.table.cell(
                rx.code(
                    item["value"],
                    style={
                        "color": rx.color("violet", 11),
                        "border_radius": "0.25rem",
                        "border": f"1px solid {rx.color('violet', 5)}",
                        "background": rx.color("violet", 3),
                    },
                )
            ),
        )
        for item in router_data
    ]),
    variant="surface",
    margin_y="1em",
)
```

### 访问原始头部（Raw Headers）

`raw_headers` 属性提供了对所有 HTTP 头部的访问，作为一个冻结字典（frozen dictionary）。当需要访问未在 `HeaderData` 类中显式定义的头部时，这非常有用：

```python box
# 访问特定头部
custom_header_value = self.router.headers.raw_headers.get("x-custom-header", "")

# 访问常见头部的示例
user_agent = self.router.headers.raw_headers.get("user-agent", "")
content_type = self.router.headers.raw_headers.get("content-type", "")
authorization = self.router.headers.raw_headers.get("authorization", "")

# 你也可以检查某个头部是否存在
has_custom_header = "x-custom-header" in self.router.headers.raw_headers
```

这对于访问自定义头部或处理未作为直接属性暴露的标准 HTTP 头部特别有用。
