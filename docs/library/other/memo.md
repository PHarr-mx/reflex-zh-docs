```python exec
import reflex as rx
```

# Memo（记忆化组件）

`@rx.memo` 装饰器将函数转换为记忆化的 React 组件。编译器将该函数作为独立模块输出，React 的 `memo` 仅在其声明的 props 发生变化时才重新渲染它。当子树渲染开销较大且仅依赖于少量状态时，可以使用它。

## 要求

每个参数都必须使用 `rx.Var[...]` 或 `rx.RestProp` 进行类型标注。编译器读取这些标注来生成 prop 名称、prop 转发和 JS 函数签名。

1. **`rx.Var[T]` 用于 props** — 将每个 prop 标注为 `rx.Var[T]`，其中 `T` 是 prop 的运行时类型（`str`、`int`、TypedDict 等）。在函数体内，该参数是一个 `Var`，你可以将其组合到渲染树中。
2. **`rx.RestProp` 用于展开 props** — 最多一个参数可以标注为 `rx.RestProp`，它会将未识别的 kwargs 转发到渲染的根元素。
3. **`rx.Var[rx.Component]` 用于插槽子元素** — 名为 `children` 且标注为 `rx.Var[rx.Component]` 的参数接受调用方渲染的子元素。
4. **调用处使用关键字参数** — 按名称传递 props，而非按位置。

默认值必须是 `rx.Var` 值。对于常见的空值情况，使用模块级常量 `rx.EMPTY_VAR_STR`（空字符串）、`rx.EMPTY_VAR_INT`（零）和 `rx.EMPTY_VAR_COMPONENT`（空组件）：`class_name: rx.Var[str] = rx.EMPTY_VAR_STR` 在调用方省略该 prop 时回退为 `""`，`children: rx.Var[rx.Component] = rx.EMPTY_VAR_COMPONENT` 使插槽变为可选。

## 基本用法

```python
class DemoState(rx.State):
    count: int = 0

    @rx.event
    def increment(self):
        self.count += 1


@rx.memo
def expensive_component(label: rx.Var[str]) -> rx.Component:
    return rx.vstack(
        rx.heading(label, as_="h2"),
        rx.text("This component only re-renders when props change."),
        rx.divider(),
    )


def index():
    return rx.vstack(
        rx.text(f"Count: {DemoState.count}"),
        rx.button("Increment", on_click=DemoState.increment),
        expensive_component(label="Memoized Component"),
    )
```

`expensive_component` 仅在 `label` 变化时重新渲染——增加 `DemoState.count` 不会使其失效。

## 配合状态变量使用

Props 可以是普通的 Var。当这些 Var 发生变化时，记忆化组件会重新渲染：

```python
class AppState(rx.State):
    name: str = "World"


@rx.memo
def greeting(name: rx.Var[str]) -> rx.Component:
    return rx.heading("Hello, " + name, as_="h2")


def index():
    return rx.vstack(
        greeting(name=AppState.name),
        rx.input(value=AppState.name, on_change=AppState.set_name),
    )
```

## 使用 `rx.RestProp` 转发 Props

使用 `rx.RestProp` 来接受并转发任意 props（类似于 JSX 中的 `...rest`）。适用于对基础组件进行重新样式设置而无需重新声明每个 prop 的轻量包装器。

```python
@rx.memo
def primary_button(
    rest: rx.RestProp,
    *,
    label: rx.Var[str],
) -> rx.Component:
    return rx.button(label, rest, class_name="bg-primary-9 text-white")


def index():
    return primary_button(
        label="Save",
        on_click=rx.console_log("clicked"),
        id="save",
    )
```

每个 memo 最多允许一个 `rx.RestProp` 参数。

`rest` 参数应被视为不透明值，并以位置参数方式传递给使用它的任何组件。

你可以使用 `.merge` var 操作将任意 props 与另一个对象 Var 或 Python 字典组合。memo 函数体可以读取类似 `rest.get("class_name", "")` 的占位符，但实际值在编译时不可用，因此你不能基于它进行分支判断或用其值执行 Python 操作，只能使用会被转换为 JavaScript 表达式的 var 操作。

与上面相同的示例，但现在允许调用方可选地传入一个 `class_name`，它会与默认样式合并：

```python
@rx.memo
def primary_button(
    rest: rx.RestProp,
    *,
    label: rx.Var[str],
) -> rx.Component:
    class_name = rest.get("class_name", "") + " bg-primary-9 text-white"
    return rx.button(label, rest.merge({"class_name": class_name}))
```


## 接受子元素

声明一个名为 `children`、类型为 `rx.Var[rx.Component]` 的参数来接收子树。

```python
@rx.memo
def card(
    children: rx.Var[rx.Component],
    *,
    title: rx.Var[str],
) -> rx.Component:
    return rx.box(
        rx.heading(title, as_="h2"),
        children,
        class_name="border border-secondary-5 rounded-lg p-4",
    )


def index():
    return card(
        rx.text("Body copy goes here."),
        title="Memoized card",
    )
```

## 返回 `Var` 而非组件

memo 函数可以返回 `rx.Var[T]` 而非 `rx.Component`。编译器会输出一个纯 JavaScript 函数，调用处只是一个可以组合到页面中的 `Var`。

```python
class PriceState(rx.State):
    amount: int = 100
    currency: str = "USD"


@rx.memo
def format_price(amount: rx.Var[int], currency: rx.Var[str]) -> rx.Var[str]:
    return currency.to(str) + ": $" + amount.to(str)


def index():
    formatted = format_price(amount=PriceState.amount, currency=PriceState.currency)
    return rx.vstack(
        rx.text(formatted),
    )
```

返回 `Var` 的 memo 函数体在编译时运行，仅限于 Var 操作——不能使用 hooks，也不能对 Var 进行 Python 分支判断。

## 自定义 JavaScript 包装器

默认情况下，编译后的函数组件会被 React 的 [`memo`](https://react.dev/reference/react/memo) 辅助函数包裹。传入 `wrapper=` 可以将其替换为其他函数——任何 JavaScript 表达式可调用的 `Var`，通常是带有自身导入的 `rx.vars.FunctionStringVar`——或传入 `wrapper=None` 以导出没有任何包装器的裸函数组件。

```python
observer = rx.vars.FunctionStringVar(
    "observer",
    _var_data=rx.vars.VarData(imports={"mobx-react-lite": "observer"}),
)


@rx.memo(wrapper=observer)
def observed_panel(label: rx.Var[str]) -> rx.Component:
    return rx.text(label)


@rx.memo(wrapper=None)
def custom_sankey_node(x: rx.Var[int], y: rx.Var[int]) -> rx.Component:
    return rx.box(width=x.to_string(), height=y.to_string())
```

包装器的导入会随 `Var` 一起携带，因此自定义包装器会自带其导入语句，而 `wrapper=None` 不会引入任何导入。`wrapper=` 仅适用于返回组件的 memo——返回 `Var` 的 memo 会编译为纯函数并拒绝该参数。

## 性能考量

在以下情况下使用 `rx.memo`：

- 组件渲染开销较大。
- 其输出是少量 props 的稳定函数。
- 频繁更新的祖先组件会迫使其重新渲染。

在以下情况下跳过它：

- 组件本身很轻量，记忆化的开销不值得。
- props 在每次渲染时都会变化——memo 永远无法短路。

## 从旧版 `rx.memo` 迁移

旧版 `rx.memo` 接受普通类型参数（`def card(title: str)`）。新版要求使用 `rx.Var[...]`。迁移方法：

```python
# 迁移前
@rx.memo
def card(title: str) -> rx.Component: ...


# 迁移后
@rx.memo
def card(title: rx.Var[str]) -> rx.Component: ...
```

旧的 `rx._x.memo` 别名仍然解析为新版 memo，并会打印一次性的 `was promoted to rx.memo` 提示。

## API 参考

### `rx.memo`

```python
rx.memo(component_fn)
rx.memo(wrapper=...)(component_fn)
```

包装一个所有参数均为 `rx.Var[...]` 或 `rx.RestProp` 的函数。返回一个可调用对象，用于构造记忆化组件（如果函数的返回标注为 `rx.Var[T]`，则返回一个 `Var`）。仅使用关键字参数调用时，返回一个应用这些参数的装饰器。

| 参数 | 类型 | 描述 |
| --- | --- | --- |
| `component_fn` | `Callable[..., rx.Component \| rx.Var]` | 要记忆化的函数。所有参数必须为 `rx.Var[...]` 或 `rx.RestProp`。 |
| `wrapper` | `rx.Var \| None` | 编译后的函数组件所包裹的 JS 函数。默认为 React 的 `memo`；传入 `None` 可省略包装器。仅支持返回组件的 memo。 |
