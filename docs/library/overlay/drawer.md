---
components:
  - rx.drawer.root
  - rx.drawer.trigger
  - rx.drawer.overlay
  - rx.drawer.portal
  - rx.drawer.content
  - rx.drawer.close

only_low_level:
  - True

DrawerRoot: |
  lambda **props: rx.drawer.root(
      rx.drawer.trigger(rx.button("Open Drawer")),
      rx.drawer.overlay(z_index="5"),
      rx.drawer.portal(
          rx.drawer.content(
              rx.flex(
                  rx.drawer.close(rx.button("Close")),
              ),
              height="100%",
              width="20em",
              background_color="#FFF"
          ),
      ),
      **props,
  )
---

```python exec
import reflex as rx
```

# Drawer（抽屉）

```python demo
rx.drawer.root(
    rx.drawer.trigger(rx.button("Open Drawer")),
    rx.drawer.overlay(z_index="5"),
    rx.drawer.portal(
        rx.drawer.content(
            rx.flex(
                rx.drawer.close(rx.box(rx.button("Close"))),
                align_items="start",
                direction="column",
            ),
            top="auto",
            right="auto",
            height="100%",
            width="20em",
            padding="2em",
            background_color="#FFF",
            # background_color=rx.color("green", 3)
        )
    ),
    direction="left",
)
```

## 使用 Drawer 和 State 创建侧边栏菜单

此示例展示了如何使用抽屉创建侧边栏菜单。点击按钮即可打开抽屉。抽屉包含指向页面不同部分的链接。点击链接时，抽屉会关闭，页面会滚动到对应的部分。

`rx.drawer.root` 组件有一个 `open` prop，由状态变量 `is_open` 设置。将 `modal` prop 设置为 `False` 允许用户在抽屉打开时与页面的其余部分交互，并允许用户点击链接时滚动页面。

```python demo exec
class DrawerState(rx.State):
    is_open: bool = False

    @rx.event
    def toggle_drawer(self):
        self.is_open = not self.is_open


def drawer_content():
    return rx.drawer.content(
        rx.flex(
            rx.drawer.close(rx.button("Close", on_click=DrawerState.toggle_drawer)),
            rx.link("Link 1", href="#test1", on_click=DrawerState.toggle_drawer),
            rx.link("Link 2", href="#test2", on_click=DrawerState.toggle_drawer),
            align_items="start",
            direction="column",
        ),
        height="100%",
        width="20%",
        padding="2em",
        background_color=rx.color("grass", 7),
    )


def lateral_menu():
    return rx.drawer.root(
        rx.drawer.trigger(rx.button("Open Drawer", on_click=DrawerState.toggle_drawer)),
        rx.drawer.overlay(),
        rx.drawer.portal(drawer_content()),
        open=DrawerState.is_open,
        direction="left",
        modal=False,
    )


def drawer_sidebar():
    return rx.vstack(
        lateral_menu(),
        rx.section(
            rx.heading("Test1", as_="h2", size="8"),
            id="test1",
            height="400px",
        ),
        rx.section(
            rx.heading("Test2", as_="h2", size="8"),
            id="test2",
            height="400px",
        ),
    )
```
