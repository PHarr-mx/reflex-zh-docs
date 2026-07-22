---
meta_description: "Wire AG Grid to a pandas DataFrame or a Reflex database model in Python. The model wrapper renders and edits your data in an interactive data grid, all in pure Python."
order: 6
---

# 模型包装器（Model Wrapper）

模型包装器是一个用于包装数据库模型并在其上提供一致接口的实用工具。它允许自动向数据库添加新行、更新现有行和删除行。

## 默认模型包装器

你可以使用 `rxe.model_wrapper` 函数来使用模型包装器的基本功能。此函数接受一个数据库模型并返回一个可用于与模型交互的包装器对象。

```python
import reflex_enterprise as rxe


def index_page():
    return rxe.model_wrapper(class_model=MyModel)
```

默认情况下，model_wrapper 使用 AgGrid 的无限行模型（Infinite Rows Model）。

## 自定义模型包装器

如果默认模型包装器不能满足你的需求，你可以通过继承 `rxe.ModelWrapper` 类来创建自定义模型包装器。这允许你自定义模型包装器的行为以适应你的特定用例。

```python
import reflex_enterprise as rxe


class MyCustomWrapper(rxe.ModelWrapper[MyModel]):
    pass
```

在自定义模型包装器中，你可以重写以下方法：
- `_get_columns_defs`
- `_get_data`
- `_row_count`
- `on_value_setter`

来修改模型包装器的行为方式。


## SSRM 模型包装器

SSRM 模型包装器，通过 `rxe.model_wrapper_ssrm` 使用，是模型包装器的一个版本，允许你使用 AgGrid 的服务端行模型（ServerSideRowModel）。

```python
import reflex_enterprise as rxe


def index_page():
    return rxe.model_wrapper_ssrm(class_model=MyModel)
```

## SSRM 自定义模型包装器

与扩展默认模型包装器的方式相同，你可以通过继承 `rxe.ModelWrapperSSRM` 类来扩展 SSRM 自定义模型包装器。这允许你自定义模型包装器的行为以适应你的特定用例。

```python
import reflex_enterprise as rxe


class MyCustomSSRMWrapper(rxe.ModelWrapperSSRM[MyModel]):
    pass
```

可重写的方法与标准模型包装器相同。
