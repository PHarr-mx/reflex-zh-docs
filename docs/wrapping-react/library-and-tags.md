---
title: Library and Tags
---


# 查找组件

有两种方法可以找到要封装的组件：

1. 自己本地编写组件。
2. 在 [npm](https://www.npmjs.com/) 上找到一个维护良好的 React 库，其中包含你需要的组件。

在这两种情况下，封装组件的过程是相同的，只是 `library` 字段有所不同。

## 封装组件

要开始封装你的 React 组件，第一步是在 Reflex 应用中创建一个新组件。这可以通过创建一个继承自 `rx.Component` 或 `rx.NoSSRComponent` 的新类来实现。

有关 `rx.Component` 类的更多详情，请参见 [API 参考](https://reflex.dev/docs/api-reference/component/)。

此时，我们将定义组件最重要的属性：

1. **library**：包含该组件的 npm 包名称。
2. **tag**：从包中导入的组件名称。
3. **alias**：（可选）要为组件使用的别名。如果来自不同包的多个组件同名，这很有用。如果未指定 `alias`，将使用 `tag`。
4. **lib_dependencies**：使用该组件所需的任何额外库。
5. **is_default**：（可选）如果组件是模块的默认导出，请设置为 `True`。默认为 `False`。

（可选）你可以通过实现 `create` 类方法来覆盖默认的组件创建行为。当 props 是从 Python 到 JavaScript 的简单转换时，大多数组件不需要这样做。但是，当你需要添加自定义初始化逻辑、转换 props 或在组件创建时处理特殊情况时，这很有用。

```md alert warning
# 设置 `library` 属性时，建议包含指定版本的包。这样，只有在你刻意更新版本时包才会更改，避免意外的破坏性变更。
```

```python
class MyBaseComponent(rx.Component):
    """MyBaseComponent."""

    # The name of the npm package.
    library = "my-library@x.y.z"

    # The name of the component to use from the package.
    tag = "MyComponent"

    # Any additional libraries needed to use the component.
    lib_dependencies: list[str] = ["package-deps@x.y.z"]

    # The name of the alias to use for the component.
    alias = "MyComponentAlias"

    # If the component is a default export from the module, set this to True.
    is_default = True / False

    @classmethod
    def create(cls, *children, **props):
        """Create an instance of MyBaseComponent.

        Args:
            *children: The children of the component.
            **props: The props of the component.

        Returns:
            The component instance.
        """
        # Your custom creation logic here
        return super().create(*children, **props)
```

## 封装动态导入组件

在封装某些库时，你可能希望使用动态导入。这是因为它们可能与服务端渲染（SSR）不兼容。

要在 Reflex 中处理这种情况，请在定义组件时继承 `NoSSRComponent`。它的使用方式与 `rx.Component` 相同，但会自动添加用于动态导入的正确自定义代码。

当你看到类似这样的导入时：

```javascript
import dynamic from "next/dynamic";

const MyLibraryComponent = dynamic(() => import("./MyLibraryComponent"), {
  ssr: false,
});
```

你可以在 Reflex 中这样封装：

```python
from reflex.components.component import NoSSRComponent


class MyLibraryComponent(NoSSRComponent):
    """A component that wraps a lib needing dynamic import."""

    library = "my-library@x.y.z"

    tag = "MyLibraryComponent"
```

有时并不清楚库何时需要动态导入。需要注意的几点是：如果组件非常侧重客户端，即其视图和结构依赖于运行时获取的内容，或者它直接使用 `window` 或 `document` 对象，则需要封装为 `NoSSRComponent`。

一些示例包括：

1. 视频和音频播放器
2. 地图
3. 绘图画布
4. 3D 图形
5. 二维码扫描器
6. Reactflow

原因在于，让服务器渲染这些组件没有意义，因为服务器无法访问你的摄像头，无法在你的画布上绘图，也无法从文件渲染视频。

此外，如果组件文档中提到了 nextJS 兼容性或服务端渲染兼容性，那就是一个很好的信号，表明它需要动态导入。

## 进阶 - 使用 JS 函数解析状态变量

在封装组件时，你可能需要通过应用 JS 函数来解析状态变量。

## 定义解析函数

首先，你需要通过在 `add_custom_code` 中编写代码来定义解析函数。

```python
def add_custom_code(self) -> list[str]:
    """Add custom code to the component."""
    # Define the parsing function
    return [
        """
        function myParsingFunction(inputProp) {
            // Your parsing logic here
            return parsedProp;
        }"""
    ]
```

## 将解析函数应用于 props

然后，你可以在 `create` 方法中将解析函数应用于你的 props。

```python
from reflex.vars.base import Var
from reflex.vars.function import FunctionStringVar

    ...
    @classmethod
    def create(cls, *children, **props):
        """Create an instance of MyBaseComponent.

        Args:
            *children: The children of the component.
            **props: The props of the component.

        Returns:
            The component instance.
        """
        # Apply the parsing function to the props
        if (prop_to_parse := props.get("propsToParse")) is not None:
            if isinstance(prop_to_parse, Var):
                props["propsToParse"] = FunctionStringVar.create("myParsingFunction").call(prop_to_parse)
            else:
                # This is not a state Var, so you can parse the value directly in python
                parsed_prop = python_parsing_function(prop_to_parse)
                props["propsToParse"] = parsed_prop
        return super().create(*children, **props)
    ...
```
