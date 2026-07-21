# 自定义代码和 Hooks

在封装 React 组件时，你可能需要定义组件特定的自定义代码或 hooks。这是通过在组件类中定义 `add_custom_code` 或 `add_hooks` 方法来实现的。

## 自定义代码

自定义代码是任何需要包含在页面中，但不一定在组件本身的 JS 代码。这可以包括 CSS 样式、JS 库或任何其他需要包含在页面中的代码。

```python
class CustomCodeComponent(MyBaseComponent):
    """MyComponent."""

    def add_custom_code(self) -> list[str]:
        """Add custom code to the component."""
        code1 = """const customVariable = "Custom code1";"""
        code2 = """console.log(customVariable);"""

        return [code1, code2]
```

上面的示例将在页面中渲染以下 JS 代码：

```javascript
/* import here */

const customVariable = "Custom code1";
console.log(customVariable);

/* rest of the page code */
```

## 自定义 Hooks

自定义 hooks 是任何需要包含在组件中的 hooks。这可以包括 `useEffect`、`useState` 或来自你所封装的库的任何其他 hooks。

- 简单的 hooks 可以作为字符串添加。
- 需要特殊导入或按特定顺序编写的更复杂的 hooks，可以添加为带有 `VarData` 对象的 `rx.Var`，以指定 hook 的位置。
  - `VarData` 对象的 `imports` 属性可用于指定需要包含在组件中的任何导入。
  - `VarData` 对象的 `position` 属性可以设置为 `Hooks.HookPosition.PRE_TRIGGER` 或 `Hooks.HookPosition.POST_TRIGGER`，以指定 hook 在组件中的位置。

```md alert info
# `position` 属性仅用于需要按特定顺序编写的 hooks。

- 如果事件处理器需要引用 hook 中定义的变量，则 hook 应写在事件处理器之前。
- 如果 hook 需要按名称引用记忆化的事件处理器，则 hook 应写在事件处理器之后。
```

```python
from reflex.vars.base import Var, VarData
from reflex_base.constants import Hooks
from reflex.components.el.elements import Div


class ComponentWithHooks(Div, MyBaseComponent):
    """MyComponent."""

    def add_hooks(self) -> list[str | Var]:
        """Add hooks to the component."""
        hooks = []
        hooks1 = """const customHookVariable = "some value";"""
        hooks.append(hooks1)

        # A hook that need to be written before the memoized event handlers.
        hooks2 = Var(
            """useEffect(() => {
                console.log("PreTrigger: " + customHookVariable);
            }, []);
            """,
            _var_data=VarData(
                imports={
                    "react": ["useEffect"],
                },
                position=Hooks.HookPosition.PRE_TRIGGER,
            ),
        )
        hooks.append(hooks2)

        hooks3 = Var(
            """useEffect(() => {
                console.log("PostTrigger: " + customHookVariable);
            }, []);
            """,
            _var_data=VarData(
                imports={
                    "react": ["useEffect"],
                },
                position=Hooks.HookPosition.POST_TRIGGER,
            ),
        )
        hooks.append(hooks3)
        return hooks
```

`ComponentWithHooks` 将在组件中以如下方式渲染：

```javascript
export function Div_7178f430b7b371af8a12d8265d65ab9b() {
  const customHookVariable = "some value";

  useEffect(() => {
    console.log("PreTrigger: " + customHookVariable);
  }, []);

  /* memoized triggers such as on_click, on_change, etc will render here */

  useEffect(() => {
    console.log("PostTrigger: "+ customHookVariable);
  }, []);

  return jsx("div", {});
}
```

```md alert info
# 你可以在同一组件中混合使用自定义代码和 hooks。Hooks 可以访问自定义代码中定义的变量，但自定义代码无法访问 hook 中定义的变量。
```
