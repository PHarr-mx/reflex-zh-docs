---
components:
  - rx.context_menu.root
  - rx.context_menu.item
  - rx.context_menu.separator
  - rx.context_menu.trigger
  - rx.context_menu.content
  - rx.context_menu.sub
  - rx.context_menu.sub_trigger
  - rx.context_menu.sub_content

only_low_level:
  - True

ContextMenuRoot: |
  lambda **props: rx.context_menu.root(
          rx.context_menu.trigger(
              rx.button(
                  "Context Menu (right click)",
                  color_scheme=props.get("color_scheme"),
                  variant=props.get("variant"),
              ),
          ),
          rx.context_menu.content(
              rx.context_menu.item("Copy", shortcut="⌘ C"),
              rx.context_menu.item("Share"),
              rx.context_menu.item("Delete", shortcut="⌘ ⌫", color="red"),
              rx.context_menu.sub(
                  rx.context_menu.sub_trigger("More"),
                  rx.context_menu.sub_content(
                      rx.context_menu.item("Eradicate"),
                      rx.context_menu.item("Duplicate"),
                      rx.context_menu.item("Archive"),
                  ),
              ),
          ),
          **props
      )

ContextMenuTrigger: |
  lambda **props: rx.context_menu.root(
          rx.context_menu.trigger(
              rx.button(
                  "Context Menu (right click)",
                  color_scheme=props.get("color_scheme"),
                  variant=props.get("variant"),
              ),
              **props,
          ),
          rx.context_menu.content(
              rx.context_menu.item("Copy", shortcut="⌘ C"),
              rx.context_menu.item("Share"),
              rx.context_menu.item("Delete", shortcut="⌘ ⌫", color="red"),
              rx.context_menu.sub(
                  rx.context_menu.sub_trigger("More"),
                  rx.context_menu.sub_content(
                      rx.context_menu.item("Eradicate"),
                      rx.context_menu.item("Duplicate"),
                      rx.context_menu.item("Archive"),
                  ),
              ),
          ),
      )

ContextMenuContent: |
  lambda **props: rx.context_menu.root(
          rx.context_menu.trigger(
              rx.button(
                  "Context Menu (right click)",
                  color_scheme=props.get("color_scheme"),
                  variant=props.get("variant"),
              ),
          ),
          rx.context_menu.content(
              rx.context_menu.item("Copy", shortcut="⌘ C"),
              rx.context_menu.item("Share"),
              rx.context_menu.item("Delete", shortcut="⌘ ⌫", color="red"),
              rx.context_menu.sub(
                  rx.context_menu.sub_trigger("More"),
                  rx.context_menu.sub_content(
                      rx.context_menu.item("Eradicate"),
                      rx.context_menu.item("Duplicate"),
                      rx.context_menu.item("Archive"),
                  ),
              ),
              **props
          ),
      )

ContextMenuSub: |
  lambda **props: rx.context_menu.root(
          rx.context_menu.trigger(
              rx.button(
                  "Context Menu (right click)",
                  color_scheme=props.get("color_scheme"),
                  variant=props.get("variant"),
              ),
          ),
          rx.context_menu.content(
              rx.context_menu.item("Copy", shortcut="⌘ C"),
              rx.context_menu.item("Share"),
              rx.context_menu.item("Delete", shortcut="⌘ ⌫", color="red"),
              rx.context_menu.sub(
                  rx.context_menu.sub_trigger("More"),
                  rx.context_menu.sub_content(
                      rx.context_menu.item("Eradicate"),
                      rx.context_menu.item("Duplicate"),
                      rx.context_menu.item("Archive"),
                  ),
              **props
              ),
          ),
      )

ContextMenuSubTrigger: |
  lambda **props: rx.context_menu.root(
          rx.context_menu.trigger(
              rx.button(
                  "Context Menu (right click)",
                  color_scheme=props.get("color_scheme"),
                  variant=props.get("variant"),
              ),
          ),
          rx.context_menu.content(
              rx.context_menu.item("Copy", shortcut="⌘ C"),
              rx.context_menu.item("Share"),
              rx.context_menu.item("Delete", shortcut="⌘ ⌫", color="red"),
              rx.context_menu.sub(
                  rx.context_menu.sub_trigger("More", **props),
                  rx.context_menu.sub_content(
                      rx.context_menu.item("Eradicate"),
                      rx.context_menu.item("Duplicate"),
                      rx.context_menu.item("Archive"),
                  ),
              ),
          ),
      )

ContextMenuSubContent: |
  lambda **props: rx.context_menu.root(
          rx.context_menu.trigger(
              rx.button(
                  "Context Menu (right click)",
                  color_scheme=props.get("color_scheme"),
                  variant=props.get("variant"),
              ),
          ),
          rx.context_menu.content(
              rx.context_menu.item("Copy", shortcut="⌘ C"),
              rx.context_menu.item("Share"),
              rx.context_menu.item("Delete", shortcut="⌘ ⌫", color="red"),
              rx.context_menu.sub(
                  rx.context_menu.sub_trigger("More"),
                  rx.context_menu.sub_content(
                      rx.context_menu.item("Eradicate"),
                      rx.context_menu.item("Duplicate"),
                      rx.context_menu.item("Archive"),
                      **props
                  ),
              ),
          ),
      )

ContextMenuItem: |
  lambda **props: rx.context_menu.root(
          rx.context_menu.trigger(
              rx.button(
                  "Context Menu (right click)",
                  color_scheme=props.get("color_scheme"),
                  variant=props.get("variant"),
              ),
          ),
          rx.context_menu.content(
              rx.context_menu.item("Copy", shortcut="⌘ C", **props),
              rx.context_menu.item("Share", **props),
              rx.context_menu.item("Delete", shortcut="⌘ ⌫", color="red", **props),
              rx.context_menu.sub(
                  rx.context_menu.sub_trigger("More"),
                  rx.context_menu.sub_content(
                      rx.context_menu.item("Eradicate", **props),
                      rx.context_menu.item("Duplicate", **props),
                      rx.context_menu.item("Archive", **props),
                  ),
              ),
          ),
      )
---

```python exec
import reflex as rx
```

# Context Menu（上下文菜单）

上下文菜单（Context Menu）是一种在用户交互（如右键单击或悬停）时弹出的菜单。

## 基本用法

上下文菜单由 `context_menu.root`、`context_menu.trigger` 和 `context_menu.content` 组成。`context_menu_root` 包含上下文菜单的所有部分。`context_menu.trigger` 是用户与之交互以打开菜单的元素。它包裹用于打开上下文菜单的元素。`context_menu.content` 是上下文菜单打开时弹出的组件。

`context_menu.item` 包含实际的上下文菜单项，位于 `context_menu.content` 之下。

`context_menu.sub` 包含子菜单的所有部分。其中有一个 `context_menu.sub_trigger`，它是用于打开子菜单的项。它必须渲染在 `context_menu.sub` 组件内部。`context_menu.sub_content` 是子菜单打开时弹出的组件。它也必须渲染在 `context_menu.sub` 组件内部。

`context_menu.separator` 用于在视觉上分隔上下文菜单中的项。

```python demo
rx.context_menu.root(
    rx.context_menu.trigger(
        rx.button("Right click me"),
    ),
    rx.context_menu.content(
        rx.context_menu.item("Edit", shortcut="⌘ E"),
        rx.context_menu.item("Duplicate", shortcut="⌘ D"),
        rx.context_menu.separator(),
        rx.context_menu.item("Archive", shortcut="⌘ N"),
        rx.context_menu.sub(
            rx.context_menu.sub_trigger("More"),
            rx.context_menu.sub_content(
                rx.context_menu.item("Move to project…"),
                rx.context_menu.item("Move to folder…"),
                rx.context_menu.separator(),
                rx.context_menu.item("Advanced options…"),
            ),
        ),
        rx.context_menu.separator(),
        rx.context_menu.item("Share"),
        rx.context_menu.item("Add to favorites"),
        rx.context_menu.separator(),
        rx.context_menu.item("Delete", shortcut="⌘ ⌫", color="red"),
    ),
)
```

````md alert warning
# `rx.context_menu.item` 必须是 `rx.context_menu.content` 的直接子元素

例如下面的代码是不允许的：

```python
rx.context_menu.root(
    rx.context_menu.trigger(
       rx.button("Right click me"),
    ),
    rx.context_menu.content(
        rx.cond(
            State.count % 2 == 0,
            rx.vstack(
                rx.context_menu.item("Even Option 1", on_click=State.set_selected_option("Even Option 1")),
                rx.context_menu.item("Even Option 2", on_click=State.set_selected_option("Even Option 2")),
                rx.context_menu.item("Even Option 3", on_click=State.set_selected_option("Even Option 3")),
            ),
            rx.vstack(
                rx.context_menu.item("Odd Option A", on_click=State.set_selected_option("Odd Option A")),
                rx.context_menu.item("Odd Option B", on_click=State.set_selected_option("Odd Option B")),
                rx.context_menu.item("Odd Option C", on_click=State.set_selected_option("Odd Option C")),
            )
        )
    ),
)
```
````

## 使用 State 从上下文菜单打开对话框

从一个覆盖层组件内部访问另一个覆盖层组件是常见的用例，但并不总是能按预期工作。

下面的代码不会按预期工作，因为对话框位于菜单内部，对话框只有在菜单打开时才能打开，这使得对话框无法使用。

```python
rx.context_menu.root(
    rx.context_menu.trigger(rx.icon("ellipsis-vertical")),
    rx.context_menu.content(
        rx.context_menu.item(
            rx.dialog.root(
            rx.dialog.trigger(rx.text("Edit")),
            rx.dialog.content(....),
            .....
            ),
        ),
    ),
)
```

在此示例中，我们将展示如何从上下文菜单打开对话框，菜单会关闭，对话框会打开并正常工作。

```python demo exec
class ContextMenuState(rx.State):
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
                        on_click=ContextMenuState.delete,
                    ),
                ),
                rx.spacer(),
                rx.alert_dialog.cancel(rx.button("Cancel")),
            ),
        ),
        open=ContextMenuState.which_dialog_open == "delete",
        on_open_change=ContextMenuState.set_which_dialog_open(""),
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
                rx.button("Close", on_click=ContextMenuState.save_settings),
            ),
        ),
        open=ContextMenuState.which_dialog_open == "settings",
        on_open_change=ContextMenuState.set_which_dialog_open(""),
    )


def context_menu_call_dialog() -> rx.Component:
    return rx.vstack(
        rx.context_menu.root(
            rx.context_menu.trigger(rx.icon("ellipsis-vertical")),
            rx.context_menu.content(
                rx.context_menu.item(
                    "Delete",
                    on_click=ContextMenuState.set_which_dialog_open("delete"),
                ),
                rx.context_menu.item(
                    "Settings",
                    on_click=ContextMenuState.set_which_dialog_open("settings"),
                ),
            ),
        ),
        rx.cond(
            ContextMenuState.which_dialog_open,
            rx.heading(
                f"{ContextMenuState.which_dialog_open} dialog is open", as_="h2"
            ),
        ),
        delete_dialog(),
        settings_dialog(),
        align="center",
    )
```
