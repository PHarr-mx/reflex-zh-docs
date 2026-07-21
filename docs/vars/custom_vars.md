```python exec
import reflex as rx
import dataclasses
from typing import TypedDict
```

# 自定义变量（Custom Vars）

如 [vars 页面](/docs/vars/base-vars)中所述，Reflex 变量必须是 JSON 可序列化的。

这意味着我们可以支持任何 Python 基本类型，以及列表、字典和元组。但是，你也可以使用数据类（dataclass，推荐）、TypedDict 或 Pydantic 模型来创建更复杂的变量类型。

## 定义类型

在此示例中，我们将使用数据类创建一个自定义变量类型，用于存储转换后的文本。

定义完成后，我们可以将其作为状态变量使用，并在组件中引用它。

```python demo exec
import dataclasses


@dataclasses.dataclass
class TextEntry:
    original_text: str
    upper_text: str


class TextEntryState(rx.State):
    input_text: str = "hello world"
    current_entry: TextEntry = TextEntry(original_text="", upper_text="")

    # Explicitly define the setter method
    def set_input_text(self, value: str):
        self.input_text = value

    @rx.event
    def transform(self):
        self.current_entry.original_text = self.input_text
        self.current_entry.upper_text = self.input_text.upper()


def text_entry_example():
    return rx.vstack(
        rx.input(
            on_blur=TextEntryState.set_input_text,
            default_value=TextEntryState.input_text,
            placeholder="Text to transform...",
        ),
        rx.button("Transform", on_click=TextEntryState.transform),
        rx.text(TextEntryState.current_entry.upper_text),
    )
```

## 替代方案

### 使用 TypedDict

你也可以使用 TypedDict 来定义自定义变量类型：

```python
from typing import TypedDict


class TextEntry(TypedDict):
    original_text: str
    upper_text: str
```

### 使用 Pydantic 模型

Pydantic 模型是复杂数据结构的另一种选择：

```python
from pydantic import BaseModel


class TextEntry(BaseModel):
    original_text: str
    upper_text: str
```

对于复杂的数据结构，推荐使用数据类（dataclasses），因为它们提供了一种简洁、类型安全的方式来定义自定义变量类型，并具有良好的 IDE 支持。
