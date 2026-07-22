---
title: Event Handler API
---

_reflex-enterprise v0.7.1 新增。_

# 事件处理器 API 插件（Event Handler API Plugin）

`rxe.EventHandlerAPIPlugin` 将 Reflex 状态上所有已注册的事件处理器（Event Handlers）暴露为 HTTP `POST` 端点，并自动生成 OpenAPI 3 规范。这使得任何 Reflex 应用无需手动编写路由即可成为机器可驱动的 API——非常适合 LLM 代理、CLI 脚本、端到端测试或需要驱动前端相同逻辑的外部集成。

```md alert info
# 需要 `reflex >= 0.9.0` 和 `reflex-enterprise`。该插件仅适用于 `rxe.App`。
```

## 端点

启用插件后，以下路由将添加到后端：

| 路径 | 用途 |
| --- | --- |
| `POST /_reflex/event/<state_full_name>/<handler_name>` | 每个状态类上的每个 `@rx.event` 处理器对应一个端点。以换行分隔的 JSON 流式传输状态增量。 |
| `POST /_reflex/retrieve_state` | 返回会话令牌对应的完整根状态 `.dict()`，无需重新注入客户端存储。 |
| `GET /_reflex/events/openapi.yaml` | 自动生成的 OpenAPI 3 规范，描述上述所有端点。 |
| `GET,HEAD /.well-known/api-catalog` | RFC 9727 API 目录，指向 OpenAPI 规范（RFC 9264 Linkset）。 |

处理器参数名称和类型注解会被内省以构建每个 `requestBody` 模式，文档字符串的第一行成为端点的 `summary`。注册为页面 `on_load` 触发器的处理器会列在规范的 `description` 字段中，以便 API 使用者了解访问特定页面时会调用哪个端点。

## 配置

在 `rxconfig.py` 中将插件添加到 `rxe.Config` 的 `plugins` 列表：

```python
import reflex as rx
import reflex_enterprise as rxe

config = rxe.Config(
    app_name="my_app",
    plugins=[
        rxe.EventHandlerAPIPlugin(
            # 所有三个参数都是可选的。
            api_version="1.0.0",
            contact={"name": "Ops", "email": "ops@example.com"},
            license_info={
                "name": "Apache 2.0",
                "url": "https://opensource.org/licenses/Apache-2.0",
            },
        )
    ],
)
```

你的应用必须使用 `rxe.App()`（而非 `rx.App()`）：

```python
import reflex_enterprise as rxe

app = rxe.App()
```

```md alert warning
# 后端在 Reflex 后端端口上提供 API（开发环境默认为 `http://localhost:8000`，生产环境为 `deploy_url`）。如果你使用 `--single-port` 运行生产环境，则 API 可通过前端端口访问（默认 `http://localhost:3000`）。
```

## 认证

每个端点都需要在 `Authorization` 头中携带 Bearer 令牌。该令牌是一个随机 UUID，用于标识**客户端会话**：

```
Authorization: Bearer <random-uuid>
```

使用相同令牌的所有调用共享状态——该令牌的作用与浏览器使用的按标签页会话 cookie 相同。使用任何 UUID 库生成一个：

```bash
TOKEN=$(python -c 'import uuid; print(uuid.uuid4())')
```

如果你希望后续请求能看到先前请求的效果（例如创建一个工单，然后列出工单），请在多次调用间重用 `$TOKEN`。选择新的 UUID 可获得全新的独立会话。

## 发现 API

该插件按照 RFC 9727 在已知位置发布 OpenAPI 规范。任何兼容的客户端都可以从目录中发现它：

```bash
curl http://localhost:8000/.well-known/api-catalog
```

响应（RFC 9264 Linkset）：

```json
{
  "linkset": [
    {
      "anchor": "http://localhost:8000/",
      "service-desc": [
        {
          "href": "http://localhost:8000/_reflex/events/openapi.yaml",
          "type": "application/vnd.oai.openapi"
        }
      ]
    }
  ]
}
```

直接获取规范：

```bash
curl http://localhost:8000/_reflex/events/openapi.yaml
```

使用任何 OpenAPI 查看器（Swagger UI、Redoc、Scalar、JetBrains HTTP 客户端等）打开该 URL 即可浏览。

## 响应格式

事件处理器端点以**换行分隔的 JSON**（`application/x-ndjson`）返回处理器产生的状态增量。每行是一个增量；处理器完成时流结束：

```
{"state.TicketState": {"tickets": [...], "total_count": 3}}
{"state.TicketState": {"open_count": 2}}
```

对于只需要最终状态的一次性客户端，只需消费完整个流，然后（可选地）获取完整状态：

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     http://localhost:8000/_reflex/retrieve_state
```

```md alert info
# 与内置的 `hydrate` 事件不同，`/_reflex/retrieve_state` **不会**重置客户端存储变量（`rx.Cookie`、`rx.LocalStorage`、`rx.SessionStorage`）。当你想读取状态而不修改它时使用它。
```

## 工单演示应用

`reflex-enterprise` 仓库在 `demos/tickets/` 下包含一个可直接运行的 IT 工单演示，涵盖了插件的所有功能。其 `rxconfig.py` 是最小参考配置：

```python
import reflex as rx
import reflex_enterprise as rxe

config = rxe.Config(
    app_name="tickets",
    async_db_url="sqlite+aiosqlite:///tickets.db",
    db_url="sqlite:///tickets.db",
    plugins=[
        rxe.EventHandlerAPIPlugin(
            contact={"name": "Reflex Maintainers", "email": "info@reflex.dev"},
            license_info={
                "name": "Apache 2.0",
                "url": "https://opensource.org/licenses/Apache-2.0",
            },
        )
    ],
    disable_plugins=[rx.plugins.SitemapPlugin],
)
```

状态类暴露了典型的 CRUD 处理器——`create_ticket`、`update_ticket`、`set_status`、`delete_ticket`、`seed`、`clear_all`，以及列表/过滤/排序/分页辅助函数和 `load_tickets` 加载处理器。以下是精简摘录：

```python
class TicketState(rx.State):
    tickets: list[TicketRecord] = []
    total_count: int = 0
    open_count: int = 0

    @rx.event
    async def create_ticket(
        self,
        title: str,
        description: str = "",
        priority: str = "medium",
        assignee: str = "",
    ) -> None:
        """Create a new IT support ticket.

        Args:
            title: Short summary of the issue.
            description: Optional long-form description.
            priority: One of "low", "medium", "high".
            assignee: Username of the person handling the ticket.
        """
        await self._create_ticket_record(
            title=title,
            description=description,
            priority=priority,
            assignee=assignee,
        )
        await self._reload_from_db()

    @rx.event
    async def set_status(self, ticket_id: str, status: str) -> None:
        """Set the status of a ticket.

        Args:
            ticket_id: The id of the ticket.
            status: One of "open", "in_progress", "closed".
        """
        ...
```

由于状态的完整名称是 `tickets___tickets____ticket_state`，生成的处理器路由位于：

```
POST /_reflex/event/tickets___tickets____ticket_state/<handler_name>
```

状态完整名称由 Python 模块路径（点分隔符变为 `___`）加类名构成——如果你不确定某个处理器的确切路径，请检查生成的 `openapi.yaml`。

### curl 示例

假设开发服务器运行在 `http://localhost:8000`，令牌存储在 `$TOKEN` 中：

```bash
TOKEN=$(python -c 'import uuid; print(uuid.uuid4())')
BASE=http://localhost:8000
TICKET_STATE=$BASE/_reflex/event/tickets___tickets____ticket_state
```

**发现 API。**

```bash
curl $BASE/.well-known/api-catalog
curl $BASE/_reflex/events/openapi.yaml
```

**获取完整状态字典。**

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     $BASE/_reflex/retrieve_state
```

**填充一些示例工单。**

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     $TICKET_STATE/seed
```

**将第一页工单加载到会话中。** 这模拟了浏览器访问 `/` 时前端运行的 `on_load` 处理器：

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     $TICKET_STATE/load_tickets
```

**创建工单。** `title` 是必填的；`description`、`priority` 和 `assignee` 是可选的（服务器应用与 Python 签名中相同的默认值）：

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     -H "Content-Type: application/json" \
     -d '{"title":"VPN is down","priority":"high","assignee":"alice"}' \
     $TICKET_STATE/create_ticket
```

**更改工单状态。**

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     -H "Content-Type: application/json" \
     -d '{"ticket_id":"<uuid>","status":"in_progress"}' \
     $TICKET_STATE/set_status
```

**部分更新。** `update_ticket` 将空字符串视为"保持不变"：

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     -H "Content-Type: application/json" \
     -d '{"ticket_id":"<uuid>","assignee":"bob","priority":"low"}' \
     $TICKET_STATE/update_ticket
```

**删除工单。**

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     -H "Content-Type: application/json" \
     -d '{"ticket_id":"<uuid>"}' \
     $TICKET_STATE/delete_ticket
```

**清空看板。**

```bash
curl -X POST -H "Authorization: Bearer $TOKEN" \
     $TICKET_STATE/clear_all
```

### OpenAPI 摘录示例

生成的规范将处理器按与状态类名匹配的 OpenAPI `tag` 分组。以下是 `create_ticket` 的条目：

```yaml
/_reflex/event/tickets___tickets____ticket_state/create_ticket:
  post:
    summary: Create a new IT support ticket.
    description: |
      title: Short summary of the issue.
      description: Optional long-form description.
      priority: One of "low", "medium", "high".
      assignee: Username of the person handling the ticket.
    operationId: TicketState_create_ticket
    tags: [TicketState]
    requestBody:
      required: true
      content:
        application/json:
          schema:
            type: object
            required: [title]
            properties:
              title:       {type: string}
              description: {type: string, default: ""}
              priority:    {type: string, default: medium}
              assignee:    {type: string, default: ""}
    responses:
      "200": {$ref: "#/components/responses/StreamedDelta"}
      "401": {$ref: "#/components/responses/Unauthorized"}
```

## 从 LLM 驱动应用

由于 OpenAPI 规范是自描述的（摘要、参数类型、默认值、on-load 引用），大多数具有 HTTP 工具访问能力的 LLM 代理无需任何额外的胶水代码即可端到端地驱动 Reflex 应用。只需给它们规范 URL 和自然语言任务：

> Use the API exposed at `http://localhost:8000/_reflex/events/openapi.yaml` to drive the application.
>
> Create a new ticket assigned to Masen for investigating RegistrationContext issues in reflex CI.

一个装备良好的代理将会：

1. `GET /_reflex/events/openapi.yaml` 并解析操作。
2. 生成一个会话令牌（`uuid4`）用作 Bearer 凭证。
3. 调用 `POST /_reflex/event/.../create_ticket`，请求体如
   `{"title": "Investigate RegistrationContext issues in Reflex CI", "assignee": "Masen", "priority": "medium"}`。
4. 可选地调用 `/_reflex/retrieve_state` 确认工单已创建。

其他适用于工单演示的提示：

> Using the Reflex API at `http://localhost:8000`, seed the database, then close every ticket currently assigned to `bob`.

> Via `http://localhost:8000/_reflex/events/openapi.yaml`, page through every ticket and summarize which assignees have the largest open backlog.

> Using the Reflex API at `http://localhost:8000`, create three high-priority tickets for the following issues, then show me the resulting state: <list of issues>

```md alert info
# 对于无法自动跟踪 `api-catalog` 的代理，直接将它们指向 `/_reflex/events/openapi.yaml`。对于大多数使用工具的模型来说，一个 URL 就足以让它们继续工作。
```

## 动态路由变量

如果你的任何页面使用动态路由段（例如 `/tickets/[ticket_id]`），插件会将它们作为**可选查询参数**暴露在每个端点上，以便状态可以通过 `self.router` 读取它们：

```
POST /_reflex/event/.../load_ticket_detail?ticket_id=<uuid>
```

它们出现在 OpenAPI 规范的 `components.parameters.route_<name>` 下，并从每个操作的 `parameters` 列表中引用。

## 安全注意事项

- **Bearer 令牌只是会话 ID，不是认证凭证。** 任何能访问后端并生成 UUID 的人都可以驱动应用。在将 API 暴露到受信任网络之外之前，请将其置于正常的认证层之后（反向代理、OIDC、VPN 等）。
- **每个状态上的每个事件处理器默认都会暴露。** 如果你有特权处理器，要么将它们拆分到不想暴露的状态类上，要么仅在 API 访问适当的环境中运行插件。
- **返回 `rx.redirect(...)` 的处理器通过 API 也能工作**，但重定向会作为状态增量发出而非 HTTP 3xx——客户端看到的是 URL 变化，而非浏览器重定向。对于编程客户端来说，这通常是你想要的行为。
