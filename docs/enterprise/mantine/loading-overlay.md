---
title: Loading Overlay
---

# Loading Overlay 组件
`rxe.mantine.loading_overlay` 是一个在其子元素上方显示加载遮罩层的组件。它适用于指示正在进行的操作，并防止用户与底层内容进行交互。

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class LoadingOverlayState(rx.State):
    loading: bool = False

    @rx.event
    def toggle_loading(self):
        self.loading = not self.loading


def loading_overlay_example():
    return (
        rx.container(
            rxe.mantine.loading_overlay(
                rx.text(
                    "Loading Overlay Example",
                    height="200px",
                    width="100px",
                ),
                overlay_props={"radius": "sm", "blur": 2},
                visible=LoadingOverlayState.loading,
                z_index=1000,
            ),
        ),
        rx.button("Toggle Loading", on_click=LoadingOverlayState.toggle_loading),
    )
```
