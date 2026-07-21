# 样式和导入

在封装 React 组件时，你可能需要定义组件特定的样式和导入。这是通过在组件类中定义 `add_styles` 和 `add_imports` 方法来实现的。

### 导入

有时，你正在封装的组件需要导入其他组件或库。这是通过在组件类中定义 `add_imports` 方法来实现的。
该方法应返回一个导入字典，其中键是要导入的包名称，值是要导入的组件或库的名称。

值可以是字符串或字符串列表。如果导入需要别名，你可以使用 `ImportVar` 对象来指定别名以及是否应将导入安装为依赖项。

```python
from reflex.utils.imports import ImportVar


class ComponentWithImports(MyBaseComponent):
    def add_imports(self):
        """Add imports to the component."""
        return {
            # If you only have one import, you can use a string.
            "my-package1": "my-import1",
            # If you have multiple imports, you can pass a list.
            "my-package2": ["my-import2"],
            # If you need to control the import in a more detailed way, you can use an ImportVar object.
            "my-package3": ImportVar(
                tag="my-import3", alias="my-alias", install=False, is_default=False
            ),
            # To import a CSS file, pass the full path to the file, and use an empty string as the key.
            "": "my-package-with-css/styles.css",
        }
```

```md alert info
# 组件的 tag 和 library 将自动添加到导入中。它们不需要在 `add_imports` 中再次添加。
```

### 样式

样式是需要包含在组件中的任何 CSS 样式。样式将以内联方式添加到组件中，因此你可以使用任何在 React 中有效的 CSS 样式。

```python
class StyledComponent(MyBaseComponent):
    """MyComponent."""

    def add_style(self) -> dict[str, Any] | None:
        """Add styles to the component."""

        return rx.Style({
            "backgroundColor": "red",
            "color": "white",
            "padding": "10px",
        })
```
