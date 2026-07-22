---
components:
  - rx.auto_scroll
---

```python exec
import reflex as rx
```

# 自动滚动（Auto Scroll）

`rx.auto_scroll` 组件是一个 div，当新内容添加时会自动滚动到底部。这对于聊天界面、日志或任何动态添加新内容且你希望确保最新内容可见的容器非常有用。

## 基本用法

```python demo exec
import reflex as rx


class AutoScrollState(rx.State):
    messages: list[str] = ["Initial message"]

    def add_message(self):
        self.messages.append(f"New message #{len(self.messages) + 1}")


def auto_scroll_example():
    return rx.vstack(
        rx.auto_scroll(
            rx.foreach(
                AutoScrollState.messages,
                lambda message: rx.box(
                    message,
                    padding="0.5em",
                    border_bottom="1px solid #eee",
                    width="100%",
                ),
            ),
            height="200px",
            width="300px",
            border="1px solid #ddd",
            border_radius="md",
        ),
        rx.button("Add Message", on_click=AutoScrollState.add_message),
        width="300px",
        align_items="center",
    )
```

`auto_scroll` 组件在新内容添加时会自动滚动以显示最新内容。在此示例中，每次点击"Add Message"时，都会向列表添加一条新消息，容器会自动滚动以显示它。

## 何时使用自动滚动

- **聊天应用**：保持聊天窗口滚动到最新消息。
- **日志查看器**：自动跟踪新出现的日志条目。
- **信息流界面**：在动态更新的信息流中保持最新内容可见。

## 属性

`rx.auto_scroll` 基于 `rx.div` 组件，继承了其所有属性。默认情况下，它设置 `overflow="auto"` 以启用滚动。

一些你可能与 `auto_scroll` 配合使用的常用属性：

- `height`：设置可滚动容器的高度。
- `width`：设置可滚动容器的宽度。
- `padding`：在容器内添加内边距。
- `border`：在容器周围添加边框。
- `border_radius`：将容器的角设为圆角。

## 工作原理

该组件会跟踪新内容的添加，并在两种情况下维护滚动位置：

1. 当用户已经接近内容底部时（50 像素以内），新内容添加时会滚动到底部。
2. 当容器之前没有滚动条但现在有了（由于新内容），它会自动滚动到底部。

这种行为确保用户可以向上滚动查看旧内容而不会被强制拉回底部，同时在大多数情况下仍然自动跟随新内容。
