---
components:
  - rx.segmented_control.root
  - rx.segmented_control.item

SegmentedControlRoot: |
  lambda **props: rx.segmented_control.root(
      rx.segmented_control.item("Inbox", value="inbox"),
      rx.segmented_control.item("Drafts", value="drafts"),
      rx.segmented_control.item("Sent", value="sent"),
      default_value="inbox",
      **props,
  )

SegmentedControlItem: |
  lambda **props: rx.segmented_control.root(
      rx.segmented_control.item("Inbox", value="inbox", **props),
      rx.segmented_control.item("Drafts", value="drafts"),
      default_value="inbox",
  )
---

```python exec
import reflex as rx


class SegmentedState(rx.State):
    """The app state."""

    control: str = "test"

    @rx.event
    def set_control(self, value: str | list[str]):
        self.control = value
```

# 分段控制器（Segmented Control）

分段控制器（Segmented Control）提供了一种清晰且无障碍的方式，用于在预定义的值和视图之间切换，例如"收件箱"、"草稿"和"已发送"。

使用分段控制器，你可以进行互斥选择，即一次只能激活一个选项，清晰且无障碍。如果没有分段控制器，最终用户可能不得不使用下拉菜单或多个按钮等控件，这些控件无法清晰地传达状态或在视觉上对选项进行分组。

## 基本示例

`Segmented Control` 组件由 `rx.segmented_control.root` 组成，它将 `rx.segmented_control.item` 组合在一起。

`rx.segmented_control.item` 组件定义了控件的各个分段，每个分段都有一个标签和一个唯一值。

```python demo
rx.vstack(
    rx.segmented_control.root(
        rx.segmented_control.item("Home", value="home"),
        rx.segmented_control.item("About", value="about"),
        rx.segmented_control.item("Test", value="test"),
        on_change=SegmentedState.set_control,
        value=SegmentedState.control,
    ),
    rx.card(
        rx.text(SegmentedState.control, align="left"),
        rx.text(SegmentedState.control, align="center"),
        rx.text(SegmentedState.control, align="right"),
        width="100%",
    ),
)
```

**在上面的示例中：**

`on_change` 用于指定一个回调函数，当用户选择不同的分段时会调用该函数。在此示例中，`SegmentedState.set_control` 事件处理程序用于在用户更改所选分段时更新 `control` 状态变量。

`value` 属性用于指定当前选中的分段，它绑定到 `SegmentedState.control` 状态变量。
