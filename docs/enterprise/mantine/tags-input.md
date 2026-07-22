---
title: TagsInput
---

# TagsInput

`rxe.mantine.tags_input` 是 Mantine 组件 [TagsInput](https://mantine.dev/core/tags-input/) 的封装。它是一个实用组件，可用于显示标签或标记列表。它可以在各种场景中使用，例如在表单中或作为独立组件。

```md alert info
# 你可以使用 Mantine 文档中提到的属性，但需要以 snake_case 形式传递。
```

## 基本示例

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def tags_input_simple_page():
    """TagsInput 演示。"""
    return rxe.mantine.tags_input(
        placeholder="Enter tags",
        label="Press Enter to ad a tag",
    )
```

## 状态示例

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class TagsInputState(rx.State):
    """TagsInput 组件的状态。"""

    tags: list[str] = ["Tag1", "Tag2"]

    @rx.event
    def update_tags(self, tags: list[str]):
        """向标签列表中添加一个标签。"""
        self.tags = tags


def tags_input_page():
    """TagsInput 演示。"""
    return rxe.mantine.tags_input(
        value=TagsInputState.tags,
        on_change=TagsInputState.update_tags,
        placeholder="Enter tags",
        label="TagsInput",
        description="This is a TagsInput component",
        error="",
        size="md",
        radius="xl",
    )
```
