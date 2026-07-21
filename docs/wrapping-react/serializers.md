---
title: Serializers
---

# 序列化器

变量（Vars）可以是任何可以序列化为 JSON 的类型。这包括字符串、数字和布尔值等基本类型，以及列表、字典和数据框（dataframes）等更复杂的类型。

如果你需要序列化更复杂的类型，可以使用 `serializer` 装饰器将该类型转换为可以存储在状态中的基本类型。只需定义一个方法，将复杂类型作为参数并返回一个基本类型。我们使用类型注解来确定你要序列化的类型。

例如，Plotly 组件将 plotly 图形序列化为 JSON 字符串，该字符串可以存储在状态中。

```python
import json
import reflex as rx
from plotly.graph_objects import Figure
from plotly.io import to_json


# Use the serializer decorator to convert the figure to a JSON string.
# Specify the type of the argument as an annotation.
@rx.serializer
def serialize_figure(figure: Figure) -> list:
    # Use Plotly's to_json method to convert the figure to a JSON string.
    return json.loads(to_json(figure))["data"]
```

然后，我们可以将此类型的变量定义为组件中的 prop。

```python
import reflex as rx
from plotly.graph_objects import Figure


class Plotly(rx.Component):
    """Display a plotly graph."""

    library = "react-plotly.js@2.6.0"
    lib_dependencies: List[str] = ["plotly.js@2.22.0"]

    tag = "Plot"

    is_default = True

    # Since a serialize is defined now, we can use the Figure type directly.
    data: rx.Var[Figure]
```
