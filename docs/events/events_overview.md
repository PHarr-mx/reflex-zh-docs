```python exec
import reflex as rx
```

# 事件概述

事件由两部分组成：事件触发器（Event Triggers）和事件处理器（Event Handlers）。

- **事件处理器（Event Handlers）** 是 Reflex 应用状态（State）更新的方式。它们由用户与 UI 的交互触发，如点击按钮或悬停在元素上。事件也可以通过页面加载或其他事件触发。

- **事件触发器（Event triggers）** 是组件上的 props，用于创建要发送给事件处理器的事件。
  每个组件都支持一组事件触发器。它们在每个[组件文档](/docs/library)的事件触发器部分中有所描述。

## 示例

让我们看看下面的例子。尝试将鼠标悬停在标题上以改变文字。

```python demo exec
class WordCycleState(rx.State):
    # The words to cycle through.
    text: list[str] = ["Welcome", "to", "Reflex", "!"]

    # The index of the current word.
    index: int = 0

    @rx.event
    def next_word(self):
        self.index = (self.index + 1) % len(self.text)

    @rx.var
    def get_text(self) -> str:
        return self.text[self.index]


def event_triggers_example():
    return rx.heading(
        WordCycleState.get_text,
        as_="h2",
        on_mouse_over=WordCycleState.next_word,
        color="green",
    )
```

在这个示例中，标题组件具有**事件触发器** `on_mouse_over`。
每当用户将鼠标悬停在标题上时，就会调用 `next_word` **事件处理器**来切换单词。处理器返回后，UI 将被更新以反映新的状态。

强烈建议在事件处理器上方添加 `@rx.event` 装饰器。该装饰器启用正确的静态类型检查，确保事件处理器接收正确数量和类型的参数。

## 本节内容

在文档的事件部分，你将探索 Reflex 支持的不同类型的事件，以及调用它们的各种方式。
