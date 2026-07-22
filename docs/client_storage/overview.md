```python exec
import reflex as rx
```

# 客户端存储（Client-storage）

你可以使用浏览器的本地存储（local storage）在会话之间持久化状态。
这允许用户偏好、认证 cookie 以及其他信息
存储在客户端，并从不同的浏览器选项卡中访问。

客户端存储变量（var）看起来和行为都像普通的 `str` 变量，只是
默认值根据值应存储的位置不同，分别是 `rx.Cookie` 或 `rx.LocalStorage`。键名将基于变量名，但可以通过传递 `name="my_custom_name"` 作为关键字参数来覆盖。

有关更多信息，请参阅[浏览器存储](/docs/api-reference/browser-storage/)。

尝试在下面的文本框中输入一些值，然后在单独的选项卡中加载页面，或检查浏览器开发者工具的存储部分，以查看保存在浏览器中的值。

```python demo exec
class ClientStorageState(rx.State):
    my_cookie: str = rx.Cookie("")
    my_local_storage: str = rx.LocalStorage("")
    custom_cookie: str = rx.Cookie(name="CustomNamedCookie", max_age=3600)

    @rx.event
    def set_my_cookie(self, value: str):
        self.my_cookie = value

    @rx.event
    def set_my_local_storage(self, value: str):
        self.my_local_storage = value

    @rx.event
    def set_custom_cookie(self, value: str):
        self.custom_cookie = value


def client_storage_example():
    return rx.vstack(
        rx.hstack(
            rx.text("my_cookie"),
            rx.input(
                value=ClientStorageState.my_cookie,
                on_change=ClientStorageState.set_my_cookie,
            ),
        ),
        rx.hstack(
            rx.text("my_local_storage"),
            rx.input(
                value=ClientStorageState.my_local_storage,
                on_change=ClientStorageState.set_my_local_storage,
            ),
        ),
        rx.hstack(
            rx.text("custom_cookie"),
            rx.input(
                value=ClientStorageState.custom_cookie,
                on_change=ClientStorageState.set_custom_cookie,
            ),
        ),
    )
```
