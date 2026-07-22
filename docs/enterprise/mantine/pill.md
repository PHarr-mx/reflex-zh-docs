---
title: Pill
---

```python exec
import reflex as rx
import reflex_enterprise as rxe
```

# Pill

`rxe.mantine.pill` 是 Mantine 组件 [Pill](https://mantine.dev/core/pill/) 的封装。它是一个简单的组件，可用于显示一小段信息，例如标签或标记。它可以在各种场景中使用，例如在标签列表中或作为独立组件。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def pill_page():
    """Pill 演示。"""
    return rxe.mantine.pill(
        "Pill",
        color="blue",
        size="md",
        variant="outline",
        radius="xl",
        with_remove_button=True,
        on_remove=lambda: rx.toast("Pill on_remove triggered"),
    )
```

## Pill 分组
`rxe.mantine.pill.group` 允许将多个 `rxe.mantine.pill` 组件组合在一起，并使用预定义的布局。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def pill_group_page():
    """Pill 演示。"""
    return rxe.mantine.pill.group(
        rxe.mantine.pill("Pill 1"),
        rxe.mantine.pill("Pill 2"),
    )
```


## PillsInput

`rxe.mantine.pills_input` 是 Mantine 组件 [PillsInput](https://mantine.dev/core/pills-input/) 的封装。它是一个实用组件，可用于显示标签或标记列表。它可以在各种场景中使用，例如在表单中或作为独立组件。
它本身不包含任何逻辑，仅渲染给定的子元素。

```md alert info
# 如需一个开箱即用的完整功能组件，请考虑使用 [`rxe.mantine.tags_input`](/docs/enterprise/mantine/tags-input/)。
```

## 示例

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class PillInputState(rx.State):
    """PillsInput 演示的状态。"""

    tags: set[str] = {"Foo", "Bar"}

    @rx.event
    def add_tag(self, tag: str):
        """向标签列表中添加一个标签。"""
        self.tags.add(tag)

    @rx.event
    def remove_tag(self, tag: str):
        """从标签列表中移除一个标签。"""
        self.tags.remove(tag)


def pills_input_page():
    """PillsInput 演示。"""
    return rxe.mantine.pills_input(
        rxe.mantine.pill.group(
            rx.foreach(
                PillInputState.tags,
                lambda tag: rxe.mantine.pill(
                    tag,
                    with_remove_button=True,
                    on_remove=PillInputState.remove_tag(tag),
                ),
            ),
            rxe.mantine.pills_input.field(
                placeholder="Enter tags",
                # on_blur=PillInputState.add_tag,
            ),
        ),
        label="PillsInput",
        id="pills-input",
        value=["tag1", "tag2"],
    )
```
