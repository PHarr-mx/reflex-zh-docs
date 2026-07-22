---
components:
  - rx.text.kbd
---

```python exec
import reflex as rx
```

# rx.text.kbd（键盘输入）

表示键盘输入或快捷键。

```python demo
rx.text.kbd("Shift + Tab")
```

## 尺寸

使用 `size` prop 来控制文本大小。该 prop 还提供正确的行高和修正的字间距——随着文本尺寸增大，相对行高和字间距会减小。

```python demo
rx.flex(
    rx.text.kbd("Shift + Tab", size="1"),
    rx.text.kbd("Shift + Tab", size="2"),
    rx.text.kbd("Shift + Tab", size="3"),
    rx.text.kbd("Shift + Tab", size="4"),
    rx.text.kbd("Shift + Tab", size="5"),
    rx.text.kbd("Shift + Tab", size="6"),
    rx.text.kbd("Shift + Tab", size="7"),
    rx.text.kbd("Shift + Tab", size="8"),
    rx.text.kbd("Shift + Tab", size="9"),
    direction="column",
    spacing="3",
)
```
