```python exec
import reflex as rx
```

# 去中心化事件处理器

## 概述

去中心化事件处理器（Decentralized event handlers）允许你在状态类外部定义事件处理器，从而提供更灵活的代码组织方式。此功能在 Reflex v0.7.10 中引入，实现了更加模块化的事件处理方法。

使用去中心化事件处理器，你可以：

- 按功能组织事件处理器，而不是按状态类
- 将 UI 逻辑与状态管理分离
- 创建更易维护和可扩展的应用

## 基本用法

要创建去中心化事件处理器，请使用 `@rx.event` 装饰器修饰一个函数，并将状态实例作为其第一个参数：

```python demo exec
import reflex as rx


class MyState(rx.State):
    count: int = 0


@rx.event
def increment(state: MyState, amount: int):
    state.count += amount


def decentralized_event_example():
    return rx.vstack(
        rx.heading(f"Count: {MyState.count}", as_="h2"),
        rx.hstack(
            rx.button("Increment by 1", on_click=increment(1)),
            rx.button("Increment by 5", on_click=increment(5)),
            rx.button("Increment by 10", on_click=increment(10)),
        ),
        spacing="4",
        align="center",
    )
```

在此示例中：

1. 我们定义了一个包含 `count` 变量的 `MyState` 类
2. 我们创建了一个去中心化事件处理器 `increment`，它接受一个 `MyState` 实例作为第一个参数
3. 我们在按钮中使用该事件处理器，传入不同的递增值

## 与传统事件处理器对比

以下是定义在状态类中的传统事件处理器与去中心化事件处理器的对比：

```python box
# Traditional event handler within a state class
class TraditionalState(rx.State):
    count: int = 0

    @rx.event
    def increment(self, amount: int = 1):
        self.count += amount


# Usage in components
rx.button("Increment", on_click=TraditionalState.increment(5))


# Decentralized event handler outside the state class
class DecentralizedState(rx.State):
    count: int = 0


@rx.event
def increment(state: DecentralizedState, amount: int = 1):
    state.count += amount


# Usage in components
rx.button("Increment", on_click=increment(5))
```

主要区别：

- 传统事件处理器使用 `self` 来引用状态实例
- 去中心化事件处理器显式地将状态实例作为第一个参数
- 两种方法在组件中触发事件时使用相同的语法
- 两者都可以分别使用 `@rx.event` 装饰

## 最佳实践

### 何时使用去中心化事件处理器

去中心化事件处理器在以下场景中特别有用：

1. **大型应用**，拥有大量事件处理器，受益于更好的组织方式
2. **基于功能的组织**，希望将相关事件处理器分组在一起
3. **关注点分离**，希望保持状态定义清晰且聚焦

### 类型注解

始终为状态参数和任何额外参数使用正确的类型注解：

```python box
@rx.event
def update_user(state: UserState, name: str, age: int):
    state.name = name
    state.age = age
```

### 命名约定

请遵循以下命名约定以确保清晰：

1. 使用描述性名称来指示正在执行的操作
2. 使用状态类名作为第一个参数的类型注解
3. 在代码库中统一命名状态参数（例如，始终使用 `state` 或状态类名的首字母）

### 组织方式

考虑以下组织去中心化事件处理器的方法：

1. 将相关的事件处理器放在同一文件中
2. 将事件处理器放在它们所修改的状态类附近
3. 对于大型应用，创建一个专用的 `events` 目录，按功能组织文件

```python box
# Example organization in a larger application
# events/user_events.py
@rx.event
def update_user(state: UserState, name: str, age: int):
    state.name = name
    state.age = age


@rx.event
def delete_user(state: UserState):
    state.name = ""
    state.age = 0
```

### 与其他事件特性结合

去中心化事件处理器可以与 Reflex 其他事件特性无缝配合：

```python box
# Background event
@rx.event(background=True)
async def long_running_task(state: AppState):
    # Long-running task implementation
    pass


# Event chaining
@rx.event
def process_form(state: FormState, data: dict):
    # Process form data
    return validate_data  # Chain to another event
```
