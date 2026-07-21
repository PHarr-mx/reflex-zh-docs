```python exec
import reflex as rx
```

# Tailwind

Reflex 通过插件系统支持 [Tailwind CSS](https://tailwindcss.com/)，该系统提供了更好的控制能力并支持多个 Tailwind 版本。

## 基于插件的配置

使用 Tailwind CSS 的推荐方式是通过插件系统：

```python
import reflex as rx

config = rx.Config(
    app_name="myapp",
    plugins=[
        rx.plugins.TailwindV4Plugin(),
    ],
)
```

你可以通过向插件传递一个配置字典来定制 Tailwind 配置：

```python
import reflex as rx

tailwind_config = {
    "plugins": ["@tailwindcss/typography"],
    "theme": {
        "extend": {
            "colors": {
                "primary": "#3b82f6",
                "secondary": "#64748b",
            }
        }
    },
}

config = rx.Config(
    app_name="myapp",
    plugins=[
        rx.plugins.TailwindV4Plugin(tailwind_config),
    ],
)
```

```````md alert info
## 从旧版配置迁移

如果你当前正在使用旧的 `tailwind` 配置参数，应迁移到使用插件系统：

**旧方法（旧版）：**

```python
config = rx.Config(
    app_name="my_app",
    tailwind={
        "plugins": ["@tailwindcss/typography"],
        "theme": {"extend": {"colors": {"primary": "#3b82f6"}}},
    },
)
```

**新方法（基于插件）：**

```python
tailwind_config = {
    "plugins": ["@tailwindcss/typography"],
    "theme": {"extend": {"colors": {"primary": "#3b82f6"}}},
}

config = rx.Config(
    app_name="my_app",
    plugins=[
        rx.plugins.TailwindV4Plugin(tailwind_config),
    ],
)
```
```````

### 选择 Tailwind 版本

Reflex 同时支持 Tailwind CSS v3 和 v4：

- **TailwindV4Plugin**：新项目的推荐选择。包含最新功能和性能改进，并在新的 Reflex 模板中默认使用。
- **TailwindV3Plugin：** 仍为现有项目提供支持。如果你需要与旧的 Tailwind 配置兼容，请使用此选项。

```python
# 使用 Tailwind CSS v4（推荐用于新项目）
config = rx.Config(
    app_name="myapp",
    plugins=[rx.plugins.TailwindV4Plugin()],
)

# 使用 Tailwind CSS v3（现有项目）
config = rx.Config(
    app_name="myapp",
    plugins=[rx.plugins.TailwindV3Plugin()],
)
```

所有 Tailwind 配置选项都受支持。

你可以在 `class_name` 属性下使用任何[工具类](https://tailwindcss.com/docs/utility-first)：

```python demo
rx.box(
    "Hello World",
    class_name="text-4xl text-center text-blue-500",
)
```

## 禁用 Tailwind

要在项目中禁用 Tailwind，只需不在配置中包含任何 Tailwind 插件即可。这将阻止 Tailwind 样式应用于你的应用。

## 自定义主题

你也可以在 Reflex 应用中集成自定义 Tailwind 主题。设置过程类似于上面提到的 CSS 样式方法，只有一些细微的变化。

首先，在 `assets` 文件夹内创建一个 CSS 文件。在 CSS 文件中，包含以下 Tailwind 指令：

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  --background: blue;
  --foreground: green;
}

.dark {
  --background: darkblue;
  --foreground: lightgreen;
}
```

我们定义了几个自定义 CSS 变量（`--background` 和 `--foreground`），这些变量将在整个应用中用于样式设置。这些变量可以基于主题动态更新。

Tailwind 默认使用浅色模式，但要处理深色模式，你可以在 `.dark` 类下定义一组独立的 CSS 变量。

Tailwind 指令（`@tailwind base`、`@tailwind components`、`@tailwind utilities`）：这些是必需的 Tailwind CSS 导入，用于启用默认的基础样式、组件和工具类。

接下来，你需要在 `rxconfig.py` 文件中配置 Tailwind，以确保 Reflex 应用使用你的自定义 Tailwind 设置。

```python
import reflex as rx

tailwind_config = {
    "plugins": ["@tailwindcss/typography"],
    "theme": {
        "extend": {
            "colors": {
                "background": "var(--background)",
                "foreground": "var(--foreground)",
            },
        }
    },
}

config = rx.Config(
    app_name="app",
    plugins=[
        rx.plugins.TailwindV4Plugin(tailwind_config),
    ],
)
```

在 theme 部分，我们扩展了默认的 Tailwind 主题以包含自定义颜色。具体来说，我们引用了之前在 CSS 文件中定义的 CSS 变量（`--background` 和 `--foreground`）。

`rx.Config` 对象用于初始化和配置你的 Reflex 应用。这里，我们传递了 `tailwind_config` 字典，以确保 Tailwind 的自定义设置应用于应用。

最后，要应用你的自定义样式和 Tailwind 配置，你需要在 `rx.App` 设置中引用你在 `assets` 文件夹中创建的 CSS 文件。这将允许你直接在 Tailwind 类中使用自定义属性（变量）。

在你的 `app.py`（或主应用文件）中，进行以下更改：

```python
app = rx.App(
    theme=rx.theme(appearance="light"),
    stylesheets=["/style.css"],
)
app.add_page(
    rx.center(
        rx.text("Tailwind & Reflex!"),
        class_name="bg-background w-full h-[100vh]",
    ),
    "/",
)
```

`bg-background` 类使用了 `--background` 变量（在 CSS 文件中定义），该变量将作为背景色应用。

## 动态样式

你可以使用 `rx.cond` 或 `rx.match` 根据条件为组件设置样式。

```python demo exec
class TailwindState(rx.State):
    active = False

    @rx.event
    def toggle_active(self):
        self.active = not self.active


def tailwind_demo():
    return rx.el.div(
        rx.el.button(
            "Click me",
            on_click=TailwindState.toggle_active,
            class_name=(
                "px-4 py-2 text-white rounded-md",
                rx.cond(
                    TailwindState.active,
                    "bg-red-500",
                    "bg-blue-500",
                ),
            ),
        ),
    )
```

## 从状态中使用 Tailwind 类

在使用 Tailwind 和 Reflex 时，重要的是要理解类名必须在代码中静态定义，以便 Tailwind 能够正确编译它们。如果你在运行时从状态变量或函数动态生成类名，Tailwind 将无法在构建过程中检测到这些类，从而导致应用中缺少样式。

例如，以下代码将无法正常工作，因为类名是在状态中定义的：

```python demo exec
class TailwindDynamicClassState(rx.State):
    active = False

    @rx.var
    def button_class(self) -> str:
        return "bg-accent" if self.active else "bg-secondary"

    @rx.event
    def toggle_active(self):
        self.active = not self.active


def tailwind_dynamic_class_demo():
    return rx.el.button(
        f"Click me: {TailwindDynamicClassState.active}",
        class_name=TailwindDynamicClassState.button_class,
        on_click=TailwindDynamicClassState.toggle_active,
    )
```

## 在 Reflex 核心组件中使用 Tailwind

Reflex 核心组件基于 Radix Themes 构建，这意味着它们带有预定义的样式。当你对这些组件应用 Tailwind 类时，可能会遇到样式冲突或意外行为，因为 Tailwind 样式会与内置的 Radix 样式竞争。

为了在 Reflex 应用中使用 Tailwind CSS 时获得最佳体验，我们建议使用较低级别的 `rx.el` 组件。这些组件没有预应用的样式，让你可以完全控制使用 Tailwind 类进行样式设置，而不会产生任何冲突。请查看 HTML 组件列表[此处](/docs/library/html/)。
