# 异常处理程序

_在 v0.5.7 中添加_

异常处理程序（Exception handlers）是可以分配给应用的函数，用于处理应用运行时发生的异常。
它们对于自定义发生错误时的响应、记录错误以及执行清理任务非常有用。

## 类型

Reflex 支持两种类型的异常处理程序：`frontend_exception_handler`（前端异常处理程序）和 `backend_exception_handler`（后端异常处理程序）。

它们分别用于处理发生在 `前端（frontend）` 和 `后端（backend）` 的异常。

`前端` 错误来自应用的 JavaScript 端，而 `后端` 错误来自 Python 端的事件处理程序（event handlers）。

## 注册异常处理程序

要注册异常处理程序，将其赋值给 `app.frontend_exception_handler` 或 `app.backend_exception_handler`，以指定一个函数来处理异常。

错误处理程序的预期签名是 `def handler(exception: Exception)`。

```md alert warning
# 只有命名函数（named functions）才能作为异常处理程序使用。
```

## 示例

```python
import reflex as rx


def custom_frontend_handler(exception: Exception) -> None:
    # 我的前端错误自定义逻辑
    print("Frontend Error: " + str(exception))


def custom_backend_handler(exception: Exception) -> Optional[rx.event.EventSpec]:
    # 我的后端错误自定义逻辑
    print("Backend Error: " + str(exception))


app = rx.App(
    frontend_exception_handler=custom_frontend_handler,
    backend_exception_handler=custom_backend_handler,
)
```
