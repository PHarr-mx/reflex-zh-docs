```python exec
import reflex as rx
```

# 属性（Props）

属性（Props）修改组件的行为和外观。它们作为关键字参数传递给组件。

## 组件属性

有些属性在所有组件之间共享，但每个组件也可以定义自己的属性。

例如，`rx.image` 组件有一个 `src` 属性，用于指定要显示的图片的 URL，还有一个 `alt` 属性，用于指定图片的替代文本。

```python demo
rx.image(
    src="https://web.reflex-assets.dev/other/logo.jpg",
    alt="Reflex Logo",
)
```

查看你正在使用的组件的文档，了解有哪些可用属性以及它们如何影响组件（例如，参见 `rx.image` [参考](/docs/library/media/image#api-reference)页面）。

## 通用属性

组件支持许多标准 HTML 属性作为 props。例如：HTML [id](https://developer.mozilla.org/en-US/docs/Web/API/Element/id) 属性直接作为 prop `id` 暴露。HTML [className](https://developer.mozilla.org/en-US/docs/Web/API/Element/className) 属性作为 prop `class_name` 暴露（注意 Python 风格的 snake_case！）。

```python demo
rx.box(
    "Hello World",
    id="box-id",
    class_name=[
        "class-name-1",
        "class-name-2",
    ],
)
```

在上面的示例中，`rx.box` 组件的 `class_name` 属性被赋予了一个类名列表。这意味着 `rx.box` 组件将使用 CSS 类 `class-name-1` 和 `class-name-2` 进行样式设置。

## 样式属性

除了组件特定的属性外，大多数内置组件支持完整的样式属性范围。你可以使用任何 [CSS 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties)来为组件设置样式。

```python demo
rx.button(
    "Fancy Button",
    border_radius="1em",
    box_shadow="rgba(151, 65, 252, 0.8) 0 15px 30px -10px",
    background_image="linear-gradient(144deg,#AF40FF,#5B42F3 50%,#00DDEB)",
    box_sizing="border-box",
    color="white",
    opacity=1,
)
```

参见[样式文档](/docs/styling/overview)以了解更多关于自定义应用外观的信息。

## 将属性绑定到状态

```md alert warning
# 可选：先了解所有关于[状态（State）](/docs/state/overview)的内容。
```

Reflex 应用定义[状态（State）](/docs/state/overview)类，其中包含可以随时间变化的变量。

状态可以响应诸如点击按钮之类的用户输入，或响应诸如加载页面之类的事件而被修改。

状态变量（State vars）可以绑定到组件属性，以便 UI 始终反映应用的当前状态。

尝试点击下面的徽章以更改其颜色。

```python demo exec
class PropExampleState(rx.State):
    text: str = "Hello World"
    color: str = "red"

    @rx.event
    def flip_color(self):
        if self.color == "red":
            self.color = "blue"
        else:
            self.color = "red"


def index():
    return rx.button(
        PropExampleState.text,
        color_scheme=PropExampleState.color,
        on_click=PropExampleState.flip_color,
    )
```

在此示例中，`color_scheme` 属性绑定到 `color` 状态变量。

当调用 `flip_color` 事件处理程序时，`color` 变量被更新，`color_scheme` 属性也随之更新以匹配。
