```python exec
import reflex as rx
```

# 状态混入（State Mixins）

状态混入允许你定义可以在多个 State 类之间重用的共享功能。这对于创建可重用组件、共享业务逻辑或公共状态模式非常有用。

## 什么是状态混入？

状态混入是一个标记为 `mixin=True` 的 State 类，不能直接实例化，但可以被其他 State 类继承。混入提供了一种共享方式：

- 基础变量
- 计算变量
- 事件处理程序
- 后端变量

## 基本混入定义

要创建状态混入，继承 `rx.State` 并传递 `mixin=True`：

```python demo exec
class CounterMixin(rx.State, mixin=True):
    count: int = 0

    @rx.var
    def count_display(self) -> str:
        return f"Count: {self.count}"

    @rx.event
    def increment(self):
        self.count += 1


class MyState(CounterMixin, rx.State):
    name: str = "App"


def counter_example():
    return rx.vstack(
        rx.heading(MyState.name, as_="h2"),
        rx.text(MyState.count_display),
        rx.button("Increment", on_click=MyState.increment),
        spacing="4",
        align="center",
    )
```

在此示例中，`MyState` 自动从 `CounterMixin` 继承 `count` 变量、`count_display` 计算变量和 `increment` 事件处理程序。

## 多混入继承

你可以从多个混入继承以组合不同的功能：

```python demo exec
class TimestampMixin(rx.State, mixin=True):
    last_updated: str = ""

    @rx.event
    def update_timestamp(self):
        import datetime

        self.last_updated = datetime.datetime.now().strftime("%H:%M:%S")


class LoggingMixin(rx.State, mixin=True):
    log_messages: list[str] = []

    @rx.event
    def log_message(self, message: str):
        self.log_messages.append(message)


class CombinedState(CounterMixin, TimestampMixin, LoggingMixin, rx.State):
    app_name: str = "Multi-Mixin App"

    @rx.event
    def increment_with_log(self):
        self.increment()
        self.update_timestamp()
        self.log_message(f"Count incremented to {self.count}")


def multi_mixin_example():
    return rx.vstack(
        rx.heading(CombinedState.app_name, as_="h2"),
        rx.text(CombinedState.count_display),
        rx.text(f"Last updated: {CombinedState.last_updated}"),
        rx.button("Increment & Log", on_click=CombinedState.increment_with_log),
        rx.cond(
            CombinedState.log_messages.length() > 0,
            rx.vstack(
                rx.foreach(CombinedState.log_messages[-3:], rx.text), spacing="1"
            ),
            rx.text("No logs yet"),
        ),
        spacing="4",
        align="center",
    )
```

## 混入中的后端变量

混入还可以包含不发送到客户端的后端变量（以 `_` 为前缀）：

```python demo exec
class DatabaseMixin(rx.State, mixin=True):
    _db_connection: dict = {}  # 仅后端
    user_count: int = 0  # 发送到客户端

    @rx.event
    def fetch_user_count(self):
        # 模拟数据库查询
        self.user_count = len(self._db_connection.get("users", []))


class AppState(DatabaseMixin, rx.State):
    app_title: str = "User Management"


def database_example():
    return rx.vstack(
        rx.heading(AppState.app_title, as_="h2"),
        rx.text(f"User count: {AppState.user_count}"),
        rx.button("Fetch Users", on_click=AppState.fetch_user_count),
        spacing="4",
        align="center",
    )
```

后端变量对于存储敏感数据、数据库连接或其他不应暴露给客户端的服务器端状态非常有用。

## 混入中的计算变量

混入中的计算变量与常规 State 类中的工作方式相同：

```python demo exec
class FormattingMixin(rx.State, mixin=True):
    value: float = 0.0

    @rx.var
    def formatted_value(self) -> str:
        return f"${self.value:.2f}"

    @rx.var
    def is_positive(self) -> bool:
        return self.value > 0


class PriceState(FormattingMixin, rx.State):
    product_name: str = "Widget"

    @rx.event
    def set_price(self, price: str):
        try:
            self.value = float(price)
        except ValueError:
            self.value = 0.0


def formatting_example():
    return rx.vstack(
        rx.heading(f"Product: {PriceState.product_name}", as_="h2"),
        rx.text(f"Price: {PriceState.formatted_value}"),
        rx.text(f"Positive: {PriceState.is_positive}"),
        rx.input(
            placeholder="Enter price",
            on_blur=PriceState.set_price,
        ),
        spacing="4",
        align="center",
    )
```

## 嵌套混入继承

混入可以从其他混入继承以创建分层功能：

```python demo exec
class BaseMixin(rx.State, mixin=True):
    base_value: str = "base"


class ExtendedMixin(BaseMixin, mixin=True):
    extended_value: str = "extended"

    @rx.var
    def combined_value(self) -> str:
        return f"{self.base_value}-{self.extended_value}"


class FinalState(ExtendedMixin, rx.State):
    final_value: str = "final"


def nested_mixin_example():
    return rx.vstack(
        rx.text(f"Base: {FinalState.base_value}"),
        rx.text(f"Extended: {FinalState.extended_value}"),
        rx.text(f"Combined: {FinalState.combined_value}"),
        rx.text(f"Final: {FinalState.final_value}"),
        spacing="4",
        align="center",
    )
```

此模式允许你通过组合更简单的混入来构建复杂功能。

## 最佳实践

```md alert info
# 混入设计指南

- **单一职责**：每个混入应有一个专注的目的
- **避免深度继承**：保持混入层次结构浅显以保持清晰
- **记录依赖关系**：如果混入依赖于特定变量，请记录它们
- **测试混入**：为混入功能创建测试用例
- **命名约定**：使用以 "Mixin" 结尾的描述性名称
```

## 限制

```md alert warning
# 重要限制

- 混入不能直接实例化——它们必须由具体的 State 类继承
- 混入之间的变量名冲突通过方法解析顺序（MRO）解决
- 混入不能覆盖基础 State 类的方法
- 定义混入时需要 `mixin=True` 参数
```

## 常见用例

状态混入特别适用于：

- **表单验证**：跨表单的共享验证逻辑
- **UI 状态管理**：公共模态框、加载或通知模式
- **日志记录**：集中式日志记录和调试
- **API 集成**：共享 HTTP 客户端功能
- **数据格式化**：跨组件的一致数据呈现

```python demo exec
import asyncio


class ValidationMixin(rx.State, mixin=True):
    errors: dict[str, str] = {}
    is_loading: bool = False

    @rx.event
    def validate_email(self, email: str) -> bool:
        if "@" not in email or "." not in email:
            self.errors["email"] = "Invalid email format"
            return False
        self.errors.pop("email", None)
        return True

    @rx.event
    def validate_required(self, field: str, value: str) -> bool:
        if not value.strip():
            self.errors[field] = f"{field.title()} is required"
            return False
        self.errors.pop(field, None)
        return True

    @rx.event
    def clear_errors(self):
        self.errors = {}


class ContactFormState(ValidationMixin, rx.State):
    name: str = ""
    email: str = ""
    message: str = ""

    def set_name(self, value: str):
        self.name = value

    def set_email(self, value: str):
        self.email = value

    def set_message(self, value: str):
        self.message = value

    @rx.event
    async def submit_form(self):
        self.clear_errors()
        valid_name = self.validate_required("name", self.name)
        valid_email = self.validate_email(self.email)
        valid_message = self.validate_required("message", self.message)

        if valid_name and valid_email and valid_message:
            self.is_loading = True
            yield
            await asyncio.sleep(1)
            self.is_loading = False
            self.name = ""
            self.email = ""
            self.message = ""


def validation_example():
    return rx.vstack(
        rx.heading("Contact Form", as_="h2"),
        rx.input(
            placeholder="Name",
            value=ContactFormState.name,
            on_change=ContactFormState.set_name,
        ),
        rx.cond(
            ContactFormState.errors.contains("name"),
            rx.text(ContactFormState.errors["name"], color="red"),
        ),
        rx.input(
            placeholder="Email",
            value=ContactFormState.email,
            on_change=ContactFormState.set_email,
        ),
        rx.cond(
            ContactFormState.errors.contains("email"),
            rx.text(ContactFormState.errors["email"], color="red"),
        ),
        rx.text_area(
            placeholder="Message",
            value=ContactFormState.message,
            on_change=ContactFormState.set_message,
        ),
        rx.cond(
            ContactFormState.errors.contains("message"),
            rx.text(ContactFormState.errors["message"], color="red"),
        ),
        rx.button(
            "Submit",
            on_click=ContactFormState.submit_form,
            loading=ContactFormState.is_loading,
        ),
        spacing="4",
        align="center",
        width="300px",
    )
```

通过使用状态混入，你可以创建模块化、可重用的状态逻辑，使你的应用程序保持组织性并减少代码重复。
