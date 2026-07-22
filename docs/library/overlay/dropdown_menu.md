---
components:
  - rx.dropdown_menu.root
  - rx.dropdown_menu.content
  - rx.dropdown_menu.trigger
  - rx.dropdown_menu.item
  - rx.dropdown_menu.separator
  - rx.dropdown_menu.sub_content

only_low_level:
  - True

DropdownMenuRoot: |
  lambda **props: rx.menu.root(
      rx.menu.trigger(
          rx.button(
              "Click to open Dropdown Menu",
              color_scheme=props.get("color_scheme"),
              variant=props.get("variant"),
          ),
      ),
      rx.menu.content(
          rx.menu.item("Edit", shortcut="⌘ E"),
          rx.menu.item("Share"),
          rx.menu.item("Delete", shortcut="⌘ ⌫", color="red"),
          rx.menu.sub(
              rx.menu.sub_trigger("More"),
              rx.menu.sub_content(
                  rx.menu.item("Eradicate"),
                  rx.menu.item("Duplicate"),
                  rx.menu.item("Archive"),
              ),
          ),
      ),
      **props,
  )

DropdownMenuContent: |
  lambda **props: rx.menu.root(
      rx.menu.trigger(
          rx.button(
              "Click to open Dropdown Menu",
              color_scheme=props.get("color_scheme"),
              variant=props.get("variant"),
          ),
      ),
      rx.menu.content(
          rx.menu.item("Edit", shortcut="⌘ E"),
          rx.menu.item("Share"),
          rx.menu.item("Delete", shortcut="⌘ ⌫", color="red"),
          rx.menu.sub(
              rx.menu.sub_trigger("More"),
              rx.menu.sub_content(
                  rx.menu.item("Eradicate"),
                  rx.menu.item("Duplicate"),
                  rx.menu.item("Archive"),
              ),
          ),
          **props,
      ),
  )

DropdownMenuItem: |
  lambda **props: rx.menu.root(
      rx.menu.trigger(
          rx.button(
              "Click to open Dropdown Menu",
              color_scheme=props.get("color_scheme"),
              variant=props.get("variant"),
          ),
      ),
      rx.menu.content(
          rx.menu.item("Edit", shortcut="⌘ E", **props),
          rx.menu.item("Share", **props),
          rx.menu.item("Delete", shortcut="⌘ ⌫", color="red", **props),
          rx.menu.sub(
              rx.menu.sub_trigger("More"),
              rx.menu.sub_content(
                  rx.menu.item("Eradicate", **props),
                  rx.menu.item("Duplicate", **props),
                  rx.menu.item("Archive", **props),
              ),
          ),
      ),
  )

DropdownMenuSub: |
  lambda **props: rx.menu.root(
      rx.menu.trigger(
          rx.button(
              "Dropdown Menu",
              color_scheme=props.get("color_scheme"),
              variant=props.get("variant"),
          ),
      ),
      rx.menu.content(
          rx.menu.item("Edit", shortcut="⌘ E"),
          rx.menu.item("Share"),
          rx.menu.item("Delete", shortcut="⌘ ⌫", color="red"),
          rx.menu.sub(
              rx.menu.sub_trigger("More"),
              rx.menu.sub_content(
                  rx.menu.item("Eradicate"),
                  rx.menu.item("Duplicate"),
                  rx.menu.item("Archive"),
              ),
              **props,
          ),
      ),
  )

DropdownMenuSubTrigger: |
  lambda **props: rx.menu.root(
      rx.menu.trigger(
          rx.button(
              "Dropdown Menu",
              color_scheme=props.get("color_scheme"),
              variant=props.get("variant"),
          ),
      ),
      rx.menu.content(
          rx.menu.item("Edit", shortcut="⌘ E"),
          rx.menu.item("Share"),
          rx.menu.item("Delete", shortcut="⌘ ⌫", color="red"),
          rx.menu.sub(
              rx.menu.sub_trigger("More", **props),
              rx.menu.sub_content(
                  rx.menu.item("Eradicate"),
                  rx.menu.item("Duplicate"),
                  rx.menu.item("Archive"),
              ),
          ),
      ),
  )

DropdownMenuSubContent: |
  lambda **props: rx.menu.root(
      rx.menu.trigger(
          rx.button(
              "Click to open Dropdown Menu",
              color_scheme=props.get("color_scheme"),
              variant=props.get("variant"),
          ),
      ),
      rx.menu.content(
          rx.menu.item("Edit", shortcut="⌘ E"),
          rx.menu.item("Share"),
          rx.menu.item("Delete", shortcut="⌘ ⌫", color="red"),
          rx.menu.sub(
              rx.menu.sub_trigger("More"),
              rx.menu.sub_content(
                  rx.menu.item("Eradicate"),
                  rx.menu.item("Duplicate"),
                  rx.menu.item("Archive"),
                  **props,
              ),
          ),
      ),
  )
---

```python exec
import reflex as rx
```

# Dropdown Menu（下拉菜单）

下拉菜单（Dropdown Menu）是提供一系列选项供用户选择的菜单。它通常位于控制其显示和隐藏的按钮附近。

下拉菜单由 `menu.root`、`menu.trigger` 和 `menu.content` 组成。`menu.trigger` 是用户与之交互以打开菜单的元素。它包裹用于打开下拉菜单的元素。`menu.content` 是下拉菜单打开时弹出的组件。

`menu.item` 包含实际的下拉菜单项，位于 `menu.content` 之下。`shortcut` prop 是显示在菜单项文本旁边的可选快捷键命令。

`menu.sub` 包含子菜单的所有部分。其中有一个 `menu.sub_trigger`，它是用于打开子菜单的项。它必须渲染在 `menu.sub` 组件内部。`menu.sub_component` 是子菜单打开时弹出的组件。它也必须渲染在 `menu.sub` 组件内部。

`menu.separator` 用于在视觉上分隔下拉菜单中的项。

```python demo
rx.menu.root(
    rx.menu.trigger(
        rx.button("Options", variant="soft"),
    ),
    rx.menu.content(
        rx.menu.item("Edit", shortcut="⌘ E"),
        rx.menu.item("Duplicate", shortcut="⌘ D"),
        rx.menu.separator(),
        rx.menu.item("Archive", shortcut="⌘ N"),
        rx.menu.sub(
            rx.menu.sub_trigger("More"),
            rx.menu.sub_content(
                rx.menu.item("Move to project…"),
                rx.menu.item("Move to folder…"),
                rx.menu.separator(),
                rx.menu.item("Advanced options…"),
            ),
        ),
        rx.menu.separator(),
        rx.menu.item("Share"),
        rx.menu.item("Add to favorites"),
        rx.menu.separator(),
        rx.menu.item("Delete", shortcut="⌘ ⌫", color="red"),
    ),
)
```

## 下拉菜单打开或关闭时的事件

来自 `menu.root` 的 `on_open_change` 事件在下拉菜单的 `open` 状态发生变化时被调用。它与 `open` prop 配合使用，`open` 的值会传递给事件处理器。

```python demo exec
class DropdownMenuState(rx.State):
    num_opens: int = 0
    opened: bool = False

    @rx.event
    def count_opens(self, value: bool):
        self.opened = value
        self.num_opens += 1


def dropdown_menu_example():
    return rx.flex(
        rx.heading(
            f"Number of times Dropdown Menu opened or closed: {DropdownMenuState.num_opens}",
            as_="h2",
        ),
        rx.heading(f"Dropdown Menu open: {DropdownMenuState.opened}", as_="h2"),
        rx.menu.root(
            rx.menu.trigger(
                rx.button("Options", variant="soft", size="2"),
            ),
            rx.menu.content(
                rx.menu.item("Edit", shortcut="⌘ E"),
                rx.menu.item("Duplicate", shortcut="⌘ D"),
                rx.menu.separator(),
                rx.menu.item("Archive", shortcut="⌘ N"),
                rx.menu.separator(),
                rx.menu.item("Delete", shortcut="⌘ ⌫", color="red"),
            ),
            on_open_change=DropdownMenuState.count_opens,
        ),
        direction="column",
        spacing="3",
    )
```

## 使用 State 从菜单打开对话框

从一个覆盖层组件内部访问另一个覆盖层组件是常见的用例，但并不总是能按预期工作。

下面的代码不会按预期工作，因为对话框位于菜单内部，对话框只有在菜单打开时才能打开，这使得对话框无法使用。

```python
rx.menu.root(
    rx.menu.trigger(rx.icon("ellipsis-vertical")),
    rx.menu.content(
        rx.menu.item(
            rx.dialog.root(
            rx.dialog.trigger(rx.text("Edit")),
            rx.dialog.content(....),
            .....
            ),
        ),
    ),
)
```

在此示例中，我们将展示如何从下拉菜单打开对话框，菜单会关闭，对话框会打开并正常工作。

```python demo exec
class DropdownMenuState2(rx.State):
    which_dialog_open: str = ""

    @rx.event
    def set_which_dialog_open(self, value: str):
        self.which_dialog_open = value

    @rx.event
    def delete(self):
        yield rx.toast("Deleted item")

    @rx.event
    def save_settings(self):
        yield rx.toast("Saved settings")


def delete_dialog():
    return rx.alert_dialog.root(
        rx.alert_dialog.content(
            rx.alert_dialog.title("Are you Sure?"),
            rx.alert_dialog.description(
                rx.text(
                    "This action cannot be undone. Are you sure you want to delete this item?",
                ),
                margin_bottom="20px",
            ),
            rx.hstack(
                rx.alert_dialog.action(
                    rx.button(
                        "Delete",
                        color_scheme="red",
                        on_click=DropdownMenuState2.delete,
                    ),
                ),
                rx.spacer(),
                rx.alert_dialog.cancel(rx.button("Cancel")),
            ),
        ),
        open=DropdownMenuState2.which_dialog_open == "delete",
        on_open_change=DropdownMenuState2.set_which_dialog_open(""),
    )


def settings_dialog():
    return rx.dialog.root(
        rx.dialog.content(
            rx.dialog.title("Settings"),
            rx.dialog.description(
                rx.text("Set your settings in this settings dialog."),
                margin_bottom="20px",
            ),
            rx.dialog.close(
                rx.button("Close", on_click=DropdownMenuState2.save_settings),
            ),
        ),
        open=DropdownMenuState2.which_dialog_open == "settings",
        on_open_change=DropdownMenuState2.set_which_dialog_open(""),
    )


def menu_call_dialog() -> rx.Component:
    return rx.vstack(
        rx.menu.root(
            rx.menu.trigger(rx.icon("menu")),
            rx.menu.content(
                rx.menu.item(
                    "Delete",
                    on_click=DropdownMenuState2.set_which_dialog_open("delete"),
                ),
                rx.menu.item(
                    "Settings",
                    on_click=DropdownMenuState2.set_which_dialog_open("settings"),
                ),
            ),
        ),
        rx.cond(
            DropdownMenuState2.which_dialog_open,
            rx.heading(
                f"{DropdownMenuState2.which_dialog_open} dialog is open", as_="h2"
            ),
        ),
        delete_dialog(),
        settings_dialog(),
        align="center",
    )
```
