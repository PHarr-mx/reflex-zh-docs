---
title: Props - Wrapping React
---

# Props

封装 React 组件时，你需要定义组件将接受的 props。这是通过定义 props 并使用 `rx.Var` 进行类型标注来完成的。

广义上讲，封装 React 组件时你会遇到三种类型的 props：

1. **简单 Props（Simple Props）**：直接传递给组件的 props。可以是任何类型，包括字符串、数字、布尔值，甚至是列表或字典。
2. **回调 Props（Callback Props）**：期望接收函数的 props。该函数通常会被组件作为回调调用。（这与事件处理器不同。）
3. **组件 Props（Component Props）**：期望接收组件本身的 props。它们可用于通过组合方式创建更复杂的组件。
4. **事件处理器（Event Handlers）**：期望接收一个在事件发生时被调用的函数的 props。它们被定义为 `rx.EventHandler`，并带有一个签名函数用于定义事件的规范。

## 简单 Props

简单 props 是封装 React 组件时最常见的一种 props。它们直接传递给组件，可以是任何类型（但最常见的是字符串、数字、布尔值和结构体）。

对于自定义类型，你可以使用 `TypedDict` 来定义自定义类型的结构。但是，如果你需要属性在编译为 JS 后自动转换为驼峰命名（camelCase），可以使用 `rx.PropsBase` 代替 `TypedDict`。

```python
class CustomReactType(TypedDict):
    """Custom React type."""

    # Define the structure of the custom type to match the Javascript structure.
    attribute1: str
    attribute2: bool
    attribute3: int


class CustomReactType2(rx.PropsBase):
    """Custom React type."""

    # Define the structure of the custom type to match the Javascript structure.
    attr_foo: str  # will be attrFoo in JS
    attr_bar: bool  # will be attrBar in JS
    attr_baz: int  # will be attrBaz in JS


class SimplePropsComponent(MyBaseComponent):
    """MyComponent."""

    # Type the props according the component documentation.

    # props annotated as `string` in javascript
    prop1: rx.Var[str]

    # props annotated as `number` in javascript
    prop2: rx.Var[int]

    # props annotated as `boolean` in javascript
    prop3: rx.Var[bool]

    # props annotated as `string[]` in javascript
    prop4: rx.Var[list[str]]

    # props annotated as `CustomReactType` in javascript
    props5: rx.Var[CustomReactType]

    # props annotated as `CustomReactType2` in javascript
    props6: rx.Var[CustomReactType2]

    # Sometimes a props will accept multiple types. You can use `|` to specify the types.
    # props annotated as `string | boolean` in javascript
    props7: rx.Var[str | bool]
```

## 回调 Props

回调 props 用于处理事件或将数据传回父组件。它们被定义为类型为 `FunctionVar` 或 `Callable` 的 `rx.Var`。

```python
from typing import Callable
from reflex.vars.function import FunctionVar


class CallbackPropsComponent(MyBaseComponent):
    """MyComponent."""

    # A callback prop that takes a single argument.
    callback_props: rx.Var[Callable]
```

## 组件 Props

某些组件偶尔会接受其他组件作为 props，通常标注为 `ReactNode`。在 Reflex 中，这些被定义为 `rx.Component`。

```python
class ComponentPropsComponent(MyBaseComponent):
    """MyComponent."""

    # A prop that takes a component as an argument.
    component_props: rx.Var[rx.Component]
```

## 事件处理器

事件处理器是期望接收一个在事件发生时被调用的函数的 props。它们被定义为 `rx.EventHandler`，并带有一个签名函数用于定义事件的规范。

```python
from reflex.vars.function import FunctionVar
from reflex.vars.object import ObjectVar


class InputEventType(TypedDict):
    """Input event type."""

    # Define the structure of the input event.
    foo: str
    bar: int


class OutputEventType(TypedDict):
    """Output event type."""

    # Define the structure of the output event.
    baz: str
    qux: int


def custom_spec1(event: ObjectVar[InputEventType]) -> tuple[str, int]:
    """Custom event spec using ObjectVar with custom type as input and tuple as output."""
    return (
        event.foo.to(str),
        event.bar.to(int),
    )


def custom_spec2(event: ObjectVar[dict]) -> tuple[Var[OutputEventType]]:
    """Custom event spec using ObjectVar with dict as input and custom type as output."""
    return Var.create(
        {
            "baz": event["foo"],
            "qux": event["bar"],
        },
    ).to(OutputEventType)


class EventHandlerComponent(MyBaseComponent):
    """MyComponent."""

    # An event handler that take no argument.
    on_event: rx.EventHandler[rx.event.no_args_event_spec]

    # An event handler that takes a single string argument.
    on_event_with_arg: rx.EventHandler[rx.event.passthrough_event_spec(str)]

    # An event handler specialized for input events, accessing event.target.value from the event.
    on_input_change: rx.EventHandler[rx.event.input_event]

    # An event handler specialized for key events, accessing event.key from the event and provided modifiers (ctrl, alt, shift, meta).
    on_key_down: rx.EventHandler[rx.event.key_event]

    # An event handler that takes a custom spec. (Event handler must expect a tuple of two values [str and int])
    on_custom_event: rx.EventHandler[custom_spec1]

    # Another event handler that takes a custom spec. (Event handler must expect a tuple of one value, being a OutputEventType)
    on_custom_event2: rx.EventHandler[custom_spec2]
```

```md alert info
# 自定义事件规范在几种场景下特别有用。如果事件返回不可序列化的数据，你可以过滤掉它们，以便事件能够发送到后端。你也可以使用它们在数据发送到后端之前对其进行转换。
```

### 在组件外部模拟事件处理器行为

在某些情况下，你可能需要在组件上下文之外复制应用于事件处理器的特殊行为。例如，如果要封装的组件要求事件回调以字典形式传递，可以通过直接实例化 `EventChain` 来实现。

一个真实世界的例子是 [`echarts-for-react`](https://www.npmjs.com/package/echarts-for-react) 库的 `onEvents` 属性，它不同于普通的事件处理器，期望一个事件处理器的映射，像这样：

```javascript
<ReactECharts
  option={this.getOption()}
  style={{ height: "300px", width: "100%" }}
  onEvents={{
    click: this.onChartClick,
    legendselectchanged: this.onChartLegendselectchanged,
  }}
/>
```

要在 Reflex 中实现这一点，你可以为每个事件处理器创建一个显式的 `EventChain`：

```python
@classmethod
def create(cls, *children, **props):
    on_events = props.pop("on_events", {})

    event_chains = {}
    for event_name, handler in on_events.items():
        # Convert the EventHandler/EventSpec/lambda to an EventChain
        event_chains[event_name] = rx.EventChain.create(
            handler,
            args_spec=rx.event.no_args_event_spec,
            key=event_name,
        )
    if on_events:
        props["on_events"] = event_chains

    # Create the component instance
    return super().create(*children, **props)
```
