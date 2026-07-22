---
title: Tree
---

# Tree 组件

`rxe.mantine.tree` 是一个以树形结构显示层级数据的组件。它允许用户展开和折叠节点，便于浏览大型数据集。

```md alert warning
# 由于一些技术限制（pydantic），tree 组件的 `data` 属性仅支持 5 层深度。
```

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def tree_example():
    # return rx.text("Placeholder for tree example")
    return rxe.mantine.tree(
        data=[
            {
                "value": "0",
                "label": "Root",
                "children": [
                    {"value": "0-1", "label": "Child 1"},
                    {"value": "0-2", "label": "Child 2"},
                ],
            }
        ],
    )
```
