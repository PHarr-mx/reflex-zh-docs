---
components:
  - rx.fragment
---

# 片段（Fragment）

```python exec
import reflex as rx
```

Fragment 是一个允许你将多个组件组合在一起而无需包裹节点的组件。

有关其使用场景的更多信息，请参阅 React 文档中的 [React/Fragment](https://react.dev/reference/react/Fragment)。

```python demo
rx.fragment(rx.text("Component1"), rx.text("Component2"))
```

```md video https://youtube.com/embed/ITOZkzjtjUA?start=3196&end=3340
# Video: Fragment
```
