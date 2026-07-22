---
components:
  - rx.lucide.Icon
---

```python exec
import reflex as rx

from reflex_components_lucide.icon import LUCIDE_ICON_LIST
from reflex.experimental.client_state import ClientStateVar

icon_search_cs = ClientStateVar.create("icon_search", default="")


@rx.memo
def lucide_icons() -> rx.Component:
    return rx.box(
        rx.box(
            rx.box(
                rx.icon(
                    tag="search",
                    size=20,
                    class_name="shrink-0 !text-secondary-11",
                ),
                class_name="absolute left-2 top-1/2 transform -translate-y-1/2 z-[1] shrink-0 flex items-center justify-center pb-2",
            ),
            rx.el.input(
                placeholder="Search icons...",
                on_change=icon_search_cs.set_value,
                class_name="relative box-border border-secondary-4 focus:border-violet-9 focus:border-1 bg-secondary-2 p-[0.5rem_0.75rem] border rounded-xl font-base text-secondary-11 placeholder:text-secondary-9 outline-none focus:outline-none w-full mb-2 pl-10",
            ),
            class_name="relative flex items-center",
        ),
        rx.el.div(
            rx.box(
                rx.foreach(
                    LUCIDE_ICON_LIST,
                    lambda icon: rx.cond(
                        icon.startswith(icon_search_cs.value),
                        rx.tooltip(
                            rx.box(
                                rx.icon(tag=icon),
                                class_name="flex items-center justify-center rounded-md hover:bg-secondary-3 transition-bg p-2 cursor-pointer",
                                on_click=[
                                    rx.set_clipboard(icon),
                                    rx.toast.success(f"Copied {icon} to clipboard"),
                                ],
                                key=icon,
                            ),
                            content=icon,
                        ),
                        None,
                    ),
                ),
                class_name="flex flex-wrap justify-start gap-4 mb-4",
            ),
            class_name="overflow-y-auto max-h-[500px]",
        ),
        class_name="flex flex-col gap-4",
    )
```

# 图标（Icon）

图标（Icon）组件用于从图标库中显示图标。此实现基于 [Lucide Icons](https://lucide.dev/icons)，你可以在该网站找到所有可用图标的列表。

## 图标列表

```python eval
lucide_icons()
```

## 基本示例

要显示图标，请从可用图标列表中指定 `tag` 属性。
也支持将 tag 作为第一个子元素传入，它会被赋值给 `tag` 属性。

`tag` 预期为 `snake_case` 格式，但也支持 `kebab-case` 格式，以便从 [https://lucide.dev/icons](https://lucide.dev/icons) 直接复制粘贴。

```python demo
rx.flex(
    rx.icon("calendar"),
    rx.icon(tag="calendar"),
    gap="2",
)
```

## 动态图标

在 Reflex 中有两种使用动态图标的方式：

### 使用 rx.match

如果你有一组特定的图标想要动态使用，可以用 `rx.match` 来定义它们：

```python
def dynamic_icon_with_match(icon_name):
    return rx.match(
        icon_name,
        ("plus", rx.icon("plus")),
        ("minus", rx.icon("minus")),
        ("equal", rx.icon("equal")),
    )
```

```python exec
def dynamic_icon_with_match(icon_name):
    return rx.match(
        icon_name,
        ("plus", rx.icon("plus")),
        ("minus", rx.icon("minus")),
        ("equal", rx.icon("equal")),
    )
```

### 使用动态图标标签

Reflex 还支持直接在 `rx.icon()` 中使用动态值作为 `tag` 属性。这允许你在运行时动态使用 Lucide 库中的任何图标。

```python exec
class DynamicIconState(rx.State):
    current_icon: str = "heart"

    def change_icon(self):
        icons = ["heart", "star", "bell", "calendar", "settings"]
        import random

        self.current_icon = random.choice(icons)
```

```python demo
rx.vstack(
    rx.heading("Dynamic Icon Example", as_="h2"),
    rx.icon(DynamicIconState.current_icon, size=30, color="red"),
    rx.button("Change Icon", on_click=DynamicIconState.change_icon),
    spacing="4",
    align="center",
)
```

在底层实现中，当动态值作为 `tag` 属性传入 `rx.icon()` 时，Reflex 会自动使用一个特殊的 `DynamicIcon` 组件，该组件可以在运行时加载图标。

```md alert
使用动态图标时，请确保图标名称有效。无效的图标名称会导致运行时错误。
```

## 样式

Lucide 图标可以通过 `stroke_width`、`size` 和 `color` 属性进行自定义。

### 描边宽度

```python demo
rx.flex(
    rx.icon("moon", stroke_width=1),
    rx.icon("moon", stroke_width=1.5),
    rx.icon("moon", stroke_width=2),
    rx.icon("moon", stroke_width=2.5),
    gap="2",
)
```

### 尺寸

```python demo
rx.flex(
    rx.icon("zoom_in", size=15),
    rx.icon("zoom_in", size=20),
    rx.icon("zoom_in", size=25),
    rx.icon("zoom_in", size=30),
    align="center",
    gap="2",
)
```

### 颜色

以下是在图标中使用基本颜色的示例。

```python demo
rx.flex(
    rx.icon("zoom_in", size=18, color="indigo"),
    rx.icon("zoom_in", size=18, color="cyan"),
    rx.icon("zoom_in", size=18, color="orange"),
    rx.icon("zoom_in", size=18, color="crimson"),
    gap="2",
)
```

也可以使用 `rx.color()` 指定带有色阶的 Radix 颜色，如下所示。

```python demo
rx.flex(
    rx.icon("zoom_in", size=18, color=rx.color("purple", 1)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 2)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 3)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 4)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 5)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 6)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 7)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 8)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 9)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 10)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 11)),
    rx.icon("zoom_in", size=18, color=rx.color("purple", 12)),
    gap="2",
)
```

以下是另一个使用带色阶的 `accent` 颜色的示例。`accent` 是主题中最主要的颜色。

```python demo
rx.flex(
    rx.icon("zoom_in", size=18, color=rx.color("accent", 1)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 2)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 3)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 4)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 5)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 6)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 7)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 8)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 9)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 10)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 11)),
    rx.icon("zoom_in", size=18, color=rx.color("accent", 12)),
    gap="2",
)
```

## 综合示例

图标可以作为许多其他组件的子组件使用。例如，在搜索栏中添加一个放大镜图标。

```python demo
rx.badge(
    rx.flex(
        rx.icon("search", size=18),
        rx.text("Search documentation...", size="3", weight="medium"),
        direction="row",
        gap="1",
        align="center",
    ),
    size="2",
    radius="full",
    color_scheme="gray",
)
```
