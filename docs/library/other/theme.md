---
components:
  - rx.theme
  - rx.theme_panel
---

# Theme（主题）

`Theme` 组件用于更改应用程序的主题。`Theme` 可以直接在 rx.App 中设置。

```python
app = rx.App(
    theme=rx.theme(
        appearance="light", has_background=True, radius="large", accent_color="teal"
    )
)
```

## Theme Panel（主题面板）

`ThemePanel` 组件是 `Theme` 组件的容器。它提供了一种更改应用程序主题的方式。

```python
rx.theme_panel()
```

主题面板默认是关闭的。你可以设置 `default_open=True` 使其默认打开。

```python
rx.theme_panel(default_open=True)
```
