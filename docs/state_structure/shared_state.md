```python exec
import reflex as rx
```

# 共享状态（Shared State）

_0.8.23 版本新增_。

定义 `rx.SharedState` 的子类会创建一种特殊类型的状态，可以由多个客户端共享。共享状态对于创建实时协作应用程序非常有用，其中多个用户需要同时查看和交互相同的数据。

## 使用 SharedState

`rx.SharedState` 子类的行为类似于普通的 `rx.State` 子类，并且在显式链接到给定令牌之前对每个客户端都是私有的。一旦链接，一个客户端对共享状态所做的任何更改都将传播到共享相同令牌的所有其他客户端。

```md alert info
# 什么应该用作令牌？

令牌可以是唯一标识应共享相同状态的一组客户端的任何字符串。常见选择包括房间 ID、文档 ID 或用户组 ID。确保令牌安全生成和管理，以防止对共享状态的未授权访问。
```

```md alert warning
# 链接的令牌不能包含下划线（\_）字符。

下划线字符当前用作令牌的内部定界符，如果用于链接状态将引发异常。

这是一个临时限制，将在未来版本中移除。

从用户输入（如查询参数）派生令牌时，在链接之前对其进行清理——例如 `raw_token.replace("_", "-")`——并在参数为空或缺失时回退到默认值。
```

### 链接共享状态

`rx.SharedState` 子类可以使用 `_link_to` 方法链接到令牌，该方法是异步的并返回链接的状态实例。链接后，针对共享状态触发的后续事件将在链接状态的上下文中执行。要取消与令牌的链接，返回等待 `_unlink` 方法的结果。

```md alert warning
# 常见链接陷阱

- `_link_to` 是一个协程——忘记 `await` 它会静默跳过链接。在共享状态链接之前对其所做的任何更改都不会共享；它们仅适用于客户端的私有实例。
- `await self._link_to(...)` 返回新链接的实例。之后立即在 `self` 上调用方法仍会改变链接前的状态，因此保存返回的实例并将其用于链接后应发生的任何操作。
- `_link_to` 仅在 `rx.SharedState` 子类上定义。要从常规 `rx.State` 事件处理程序链接，首先使用 `await self.get_state(SharedStateCls)` 获取共享状态，然后在该实例上调用 `_link_to`。
```

要尝试协作计数器示例，在第二个或第三个浏览器选项卡中打开此页面并点击 "Link" 按钮。当你在任何选项卡中点击 "Increment" 按钮时，你应该看到所有选项卡中的计数递增。

```python demo exec
class CollaborativeCounter(rx.SharedState):
    count: int = 0

    @rx.event
    async def toggle_link(self):
        if self._linked_to:
            return await self._unlink()
        else:
            linked_state = await self._link_to("shared-global-counter")
            linked_state.count += 1  # 链接时递增计数

    @rx.event
    def set_count(self, count: int):
        self.count = count

    @rx.var
    def is_linked(self) -> bool:
        return bool(self._linked_to)


def shared_state_example():
    return rx.vstack(
        rx.text(f"Collaborative Count: {CollaborativeCounter.count}"),
        rx.cond(
            CollaborativeCounter.is_linked,
            rx.button("Unlink", on_click=CollaborativeCounter.toggle_link),
            rx.button("Link", on_click=CollaborativeCounter.toggle_link),
        ),
        rx.button(
            "Increment",
            on_click=CollaborativeCounter.set_count(CollaborativeCounter.count + 1),
        ),
    )
```

```md alert info
# 计算变量可以引用 SharedState

其他状态中的计算变量可以使用 `get_state` 引用共享状态数据，就像私有状态一样。这允许私有状态提供共享数据的个性化视图。

每当共享状态更新时，依赖于它的任何计算变量都将在每个客户端私有状态的上下文中重新评估。

注意，仅依赖于共享状态变量的计算变量属于 `rx.SharedState` 本身——为也依赖于每个用户数据的值保留私有状态计算变量。
```

### 识别客户端

链接到共享状态的每个客户端都可以通过其 `self.router.session.client_token` 唯一标识。共享状态事件_永远不应_依赖于作为参数传递的标识符，因为这些可以从客户端伪造。相反，始终使用 `client_token` 来识别触发事件的客户端。

```python demo exec
import uuid


class SharedRoom(rx.SharedState):
    shared_room: str = rx.LocalStorage()
    _users: dict[str, str] = {}

    @rx.var
    def user_list(self) -> str:
        return ", ".join(self._users.values())

    @rx.event
    async def join(self, username: str):
        if not self.shared_room:
            self.shared_room = f"shared-room-{uuid.uuid4()}"
        linked_state = await self._link_to(self.shared_room)
        linked_state._users[self.router.session.client_token] = username

    @rx.event
    async def leave(self):
        if self._linked_to:
            return await self._unlink()


class PrivateState(rx.State):
    @rx.event
    def handle_submit(self, form_data: dict):
        return SharedRoom.join(form_data["username"])

    @rx.var
    async def user_in_room(self) -> bool:
        shared_state = await self.get_state(SharedRoom)
        return self.router.session.client_token in shared_state._users


def shared_room_example():
    return rx.vstack(
        rx.text("Shared Room"),
        rx.text(f"Users: {SharedRoom.user_list}"),
        rx.cond(
            PrivateState.user_in_room,
            rx.button("Leave Room", on_click=SharedRoom.leave),
            rx.form(
                rx.input(placeholder="Enter your name", name="username"),
                rx.button("Join Room"),
                on_submit=PrivateState.handle_submit,
            ),
        ),
    )
```

```md alert warning
# 将敏感数据存储在带有下划线前缀的仅后端变量中

共享状态数据同步到所有链接的客户端，因此避免在前端变量中存储敏感信息（例如 client_tokens、用户凭据、个人数据），这将使它们暴露给所有用户并允许在显式事件处理程序之外修改它们。相反，使用仅后端变量（以下划线为前缀）将敏感数据安全地保存在服务器端，并通过事件处理程序和计算变量提供受控访问。
```

### 内省链接的客户端

`rx.SharedState` 子类有两个属性用于确定链接状态和对等方，它们在链接和取消链接期间更新，并带有一些注意事项。

**`_linked_to: str`**

提供状态当前链接到的令牌，如果未链接则为空字符串。

此属性仅在 `_link_to` 返回的链接状态实例上设置。在任何未链接的共享状态实例上，它将是空字符串。但是，如果另一个状态链接到客户端的私有令牌，则 `_linked_to` 属性将设置为客户端的令牌而不是空字符串。

当 `_linked_to` 等于 `self.router.session.client_token` 时，假定当前客户端未链接，但另一个客户端已链接到此客户端的私有状态。虽然这是可能的，但通常不鼓励将共享状态链接到私有客户端令牌。

**`_linked_from: set[str]`**

当前链接到此共享状态实例的客户端令牌集合。

此属性仅在 `_link_to` 和 `_unlink` 调用期间更新。在取消链接以其他方式发生的情况下，例如客户端断开连接、调用 `self.reset()` 或状态在后端过期，`_linked_from` 可能包含不再链接的过时客户端令牌。这些可以通过检查令牌是否仍存在于 `app.event_namespace.token_to_sid` 中来定期清理。

## 指南和最佳实践

### 保持共享状态最小化

定义共享状态时， aim 保持尽可能最小化。只包含需要在客户端之间共享的数据和方法。这有助于减少复杂性和潜在的同步问题。

链接的状态总是加载到每个链接客户端上每个事件的树中，大状态需要更长的时间来序列化和通过网络传输。由于链接的状态经常在许多客户端的上下文中加载，它们会产生更高的锁争用，因此最小化加载时间也减少了其他客户端的锁等待时间。

### 在共享状态中优先使用仅后端变量

共享状态应主要使用仅后端变量（以下划线为前缀）来存储共享数据。通常，并非共享状态的所有用户都需要看到共享状态中的所有数据。根据需要使用计算变量提供对共享数据的清理访问。

```python
from typing import Literal


class SharedGameState(rx.SharedState):
    # 敏感的用户元数据存储在仅后端变量中。
    _players: dict[str, Literal["X", "O"]] = {}

    @rx.event
    def make_move(self, x: int, y: int):
        # 通过 client_token 识别用户，永远不要通过传递给事件的参数。
        player_token = self.router.session.client_token
        player_piece = self._players.get(player_token)
```

```md alert warning
# 不要信任事件处理程序参数

客户端可以向事件处理程序发送任何它想要的数据，因此永远不要依赖传递给事件处理程序的参数来获取敏感信息，如用户身份或权限。始终使用安全的标识符（如 `self.router.session.client_token`）来识别触发事件的客户端。
```

### 通过私有状态暴露每个用户的数据

如果共享状态中的某些数据需要为每个用户个性化，最好通过在私有状态中定义的计算变量来暴露该数据。这允许每个用户拥有自己的共享数据视图，而不会向其他用户暴露敏感信息。它还减少了发送到每个客户端的不相关数据量，并通过将每个用户的视图缓存在他们自己的私有状态中而不是总是为需要更新信息的每个用户重新计算共享状态变量来提高缓存性能。

使用带有 `get_state` 的异步计算变量从私有状态访问共享状态数据。

```python
class UserGameState(rx.State):
    @rx.var
    async def player_piece(self) -> str | None:
        shared_state = await self.get_state(SharedGameState)
        return shared_state._players.get(self.router.session.client_token)
```

### 对链接的令牌使用动态路由

定义包含链接令牌作为 URL 路径一部分的动态路由通常很方便。这允许用户轻松共享指向特定共享状态实例的链接。动态路由可以使用 `on_load` 将共享状态链接到从 URL 提取的令牌。

```python
class SharedRoom(rx.SharedState):
    async def on_load(self):
        # `self.room_id` 是自动定义的动态路由变量。
        await self._link_to(self.room_id.replace("_", "-") or "default-room")


def room_page(): ...


app.add_route(
    room_page,
    path="/room/[room_id]",
    on_load=SharedRoom.on_load,
)
```
