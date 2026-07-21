```python exec
import reflex as rx
from typing import Any
```

# 封装 React

Reflex 最强大的功能之一就是能够封装 React 组件，并利用庞大的 React 库生态系统。

如果你的应用需要某个特定组件但 Reflex 未提供，那么它很可能有可用的 React 组件。在 [npm](https://www.npmjs.com/) 上搜索一下，如果存在，你就可以在 Reflex 应用中使用它。你也可以创建自己的本地 React 组件并将其封装到 Reflex 中。

封装完成后，你可以将其[发布](/docs/custom-components/overview)到 Reflex 库中，以便其他人使用。

## 简单示例

没有交互的简单组件只需几行代码即可封装。

下面展示如何封装 [Spline](https://github.com/splinetool/react-spline) 库，该库可用于创建 3D 场景和动画。

```python demo exec
import reflex as rx


class Spline(rx.Component):
    """Spline component."""

    # The name of the npm package.
    library = "@splinetool/react-spline@4.1.0"

    # Any additional libraries needed to use the component.
    lib_dependencies: list[str] = ["@splinetool/runtime@1.5.5"]

    # The name of the component to use from the package.
    tag = "Spline"

    # Spline is a default export from the module.
    is_default = True

    # Any props that the component takes.
    scene: rx.Var[str]


# Convenience function to create the Spline component.
spline = Spline.create


# Use the Spline component in your app.
def index():
    return spline(scene="https://prod.spline.design/joLpOOYbGL-10EJ4/scene.splinecode")
```

## ColorPicker 示例

与 Spline 示例类似，首先定义 library 和 tag。在此示例中，library 是 `react-colorful`，tag 是 `HexColorPicker`。

我们还有一个变量 `color`，它是颜色选择器的当前颜色。

由于该组件有交互，我们必须指定组件接受的任何事件触发器。颜色选择器有一个触发器 `on_change`，用于指定颜色变化时的事件。该触发器接受一个参数 `color`，即新颜色。

```python exec
from reflex.experimental.client_state import ClientStateVar
from reflex.components.component import NoSSRComponent


class ColorPicker(NoSSRComponent):
    library = "react-colorful@5.7.0"
    tag = "HexColorPicker"
    color: rx.Var[str]
    on_change: rx.EventHandler[lambda color: [color]]


color_picker = ColorPicker.create

ColorPickerState = ClientStateVar.create(default="#db114b", var_name="color")
```

```python eval
rx.box(
    ColorPickerState,
    rx.vstack(
        rx.heading(ColorPickerState.value, as_="h2", color="white"),
        color_picker(on_change=ColorPickerState.set_value),
    ),
    background_color=ColorPickerState.value,
    padding="5em",
    border_radius="12px",
    margin_bottom="1em",
)
```

```python
from reflex.components.component import NoSSRComponent


class ColorPicker(NoSSRComponent):
    library = "react-colorful@5.7.0"
    tag = "HexColorPicker"
    color: rx.Var[str]
    on_change: rx.EventHandler[lambda color: [color]]


color_picker = ColorPicker.create


class ColorPickerState(rx.State):
    color: str = "#db114b"

    @rx.event
    def set_color(self, value: str):
        self.color = value


def index():
    return rx.box(
        rx.vstack(
            rx.heading(ColorPickerState.color, as_="h2", color="white"),
            color_picker(on_change=ColorPickerState.set_color),
        ),
        background_color=ColorPickerState.color,
        padding="5em",
        border_radius="1em",
    )
```

## 不适合封装的情况

npm 上有些库不暴露 React 组件，因此很难用 Reflex 封装。

像下面的 [spline](https://www.npmjs.com/package/@splinetool/runtime) 这样的库很难用 Reflex 封装，因为它不暴露 React 组件。

```javascript
import { Application } from '@splinetool/runtime';

// make sure you have a canvas in the body
const canvas = document.getElementById('canvas3d');

// start the application and load the scene
const spline = new Application(canvas);
spline.load('https://prod.spline.design/6Wq1Q7YGyM-iab9i/scene.splinecode');
```

你应该留意 JSX（JavaScript 的语法扩展），它使用尖括号 `(<h1>Hello, world!</h1>)`。如果你看到 JSX，很可能该库是一个 React 组件，可以用 Reflex 封装。

如果该库不暴露 React 组件，你需要尝试寻找该库的 JS React 封装，例如 [react-spline](https://www.npmjs.com/package/@splinetool/react-spline)。

```javascript
import Spline from "@splinetool/react-spline";

export default function App() {
  return (
    <div>
      <Spline scene="https://prod.spline.design/6Wq1Q7YGyM-iab9i/scene.splinecode" />
    </div>
  );
}
```

在下一页中，我们将逐步介绍一个更复杂的封装 React 组件的示例。
