---
meta_description: "Define AG Grid columns in Python with Reflex. Configure column definitions — field, header, type, sorting, filtering, and width — all in pure Python."
order: 1
---

# 列定义（Column Definitions）

## 基本列

AgGrid 允许你定义网格的列，传递给 `column_defs` 属性。每个字典代表一列。

```md alert warning
# 如果你是从其他 AG Grid 实现迁移过来的，我们也支持属性名使用 camelCase。
```

这里我们定义一个包含 3 列的网格：
```python
column_defs = [
    {"field": "direction"},
    {"field": "strength"},
    {"field": "frequency"},
]
```

要为所有列设置默认属性，你可以在网格中定义 `default_col_def`：
```python
default_col_def = {
    "sortable": True,
    "filter": True,
    "resizable": True,
}
```
