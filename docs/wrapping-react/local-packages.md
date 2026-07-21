---
title: Wrapping Local Packages
---

```python exec
import reflex as rx
```

# 资源文件

如果封装的组件依赖于图片、脚本或样式表等资源，这些资源可以放在组件代码旁边，并使用 `rx.asset` 函数包含在最终构建中。

`rx.asset` 返回一个相对路径，引用编译输出中的资源。目标文件会基于其首次使用的模块，被复制到 `assets/external` 的子目录中。这使得第三方组件可以拥有同名的外部资源而不会互相冲突。

例如，如果与此组件相同目录下有一个名为 `wave.svg` 的 SVG 文件，可以使用 `rx.image` 和 `rx.asset` 来渲染它。

```python
class Hello(rx.Component):
    @classmethod
    def create(cls, *children, **props) -> rx.Component:
        props.setdefault("align", "center")
        return rx.hstack(
            rx.image(
                src=rx.asset("wave.svg", shared=True),
                alt="Wave SVG asset",
                width="50px",
                height="50px",
            ),
            rx.heading("Hello ", *children, as_="h2"),
            **props,
        )
```

## 本地组件

你也可以封装自己编写的组件。对于本地组件（当代码源直接在项目中时），我们建议将其放在封装它的文件旁边。

如果同一目录下有一个 `hello.jsx` 文件，内容如下：

```javascript
// /path/to/components/hello.jsx
import React from "react";

export function Hello({ name, onGreet }) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <button onClick={() => onGreet(name)}>Greet</button>
    </div>
  );
}
```

Python 应用可以使用 `rx.asset` 辅助函数将组件源复制到生成的前端中，之后 `rx.Component` 中的 `library` 路径可以通过在 `rx.asset` 返回的路径前加上 `$/public` 前缀来指定。

```python
import reflex as rx

hello_path = rx.asset("./hello.jsx", shared=True)
hello_css_path = rx.asset("./hello.css", shared=True)


class Hello(rx.Component):
    # Use an absolute path starting with $/public
    library = f"$/public{hello_path}"

    # Define everything else as normal.
    tag = "Hello"

    name: rx.Var[str] = rx.Var.create("World")
    on_greet: rx.EventHandler[rx.event.passthrough_event_spec(str)]

    # Include any related CSS files with rx.asset to ensure they are copied.
    def add_imports(self):
        return {"": f"$/public/{hello_css_path}"}
```

## 注意事项

在封装本地组件时，请牢记以下几点：

1. **文件扩展名**：确保文件扩展名正确（例如，React 组件使用 `.jsx`，TypeScript 组件使用 `.tsx`）。
2. **资源管理**：使用带有 `shared=True` 的 `rx.asset` 来管理组件所依赖的任何资源（如图片、样式）。
3. **事件处理**：将任何事件处理器（如 `on_greet`）定义为组件 API 的一部分，并**从 Reflex 应用**将其传递给组件。不要尝试直接从 JavaScript 接入 Reflex 的事件系统。

## 使用场景

本地组件在以下情况下很有用：当需要实现小块功能，而这些功能直接在 JavaScript 中实现更简单或性能更高时，例如：

- 高频率事件：按键、触摸、鼠标、滚动——这些通常在客户端处理效果更好。
- 使用 Canvas、图形或 WebGPU
- 与其他 Web API（如存储、屏幕捕获、音频/ MIDI）配合使用
- 与复杂的第三方库集成
  - 对于特定于应用的使用场景，编写一个本地组件来封装库中所需的功能子集，以提供更简单的 API 供 Reflex 使用，可能更容易。

## 本地包

如果组件是本地包的一部分，可在 GitHub 上获取，或可通过 Web URL 下载，它也可以封装在 Reflex 中。在包名后的 `@` 后面指定路径或 URL。

任何本地路径都是相对于 `.web` 文件夹的，因此你可以使用 `../` 前缀来引用 Reflex 项目根目录。

以下是一个名为 [`@masenf/hello-react`](https://github.com/masenf/hello-react) 的包的有效说明符示例：

- GitHub：`@masenf/hello-react@github:masenf/hello-react`
- URL：`@masenf/hello-react@https://github.com/masenf/hello-react/archive/refs/heads/main.tar.gz`
- 本地压缩包：`@masenf/hello-react@../hello-react.tgz`
- 本地目录：`@masenf/hello-react@../hello-react`

包名必须与 `package.json` 中的名称匹配，这样 Reflex 才能在生成的 JavaScript 代码中生成正确的导入语句。

这些包说明符可用于 `library` 或 `lib_dependencies`。

```python demo exec toggle
class GithubComponent(rx.Component):
    library = "@masenf/hello-react@github:masenf/hello-react"
    tag = "Counter"

    def add_imports(self):
        return {"": ["@masenf/hello-react/dist/style.css"]}


def github_component_example():
    return GithubComponent.create()
```

虽然这种方法更复杂，但它在本地组件有额外依赖或需要构建设置来准备组件以供使用时很有用。

关于此方法的一些重要说明：

- 仓库或压缩包必须包含 `package.json` 文件。
- `prepare` 或 `build` 脚本将**不会**被执行。分发压缩包、目录或仓库必须已经包含构建好的 JavaScript 文件（这很常见）。

````md alert
# 确保在 `package.json` 中导出 CSS 文件

除了导出包含组件的模块之外，任何意图被封装的组件导入的 CSS 文件也必须在 `package.json` 的 `exports` 键中列出。

```json
{
  // ...,
  "exports": {
    ".": {
      "import": "./dist/index.js",
      "require": "./dist/index.umd.cjs"
    },
    "./dist/style.css": {
      "import": "./dist/style.css",
      "require": "./dist/style.css"
    }
  }
  // ...
}
```
````
