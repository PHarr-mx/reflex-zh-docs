---
components:
  - rx.data_list.root
  - rx.data_list.item
  - rx.data_list.label
  - rx.data_list.value
DataListRoot: |
  lambda **props: rx.data_list.root(
      rx.foreach(
          [["Status", "Authorized"], ["ID", "U-474747"], ["Name", "Developer Success"], ["Email", "foo@reflex.dev"]],
          lambda item: rx.data_list.item(rx.data_list.label(item[0]), rx.data_list.value(item[1])),
      ),
      **props,
  )
DataListItem: |
  lambda **props: rx.data_list.root(
      rx.data_list.item(
          rx.data_list.label("Status"),
          rx.data_list.value(rx.badge("Authorized", variant="soft", radius="full", size="2")),
          **props,
      ),
      rx.data_list.item(
          rx.data_list.label("Name"),
          rx.data_list.value(rx.heading("Developer Success", as_="h2", size="4")),
          **props,
      ),
      rx.data_list.item(
          rx.data_list.label("Email"),
          rx.data_list.value(rx.link("foo@reflex.dev", href="mailto:foo@reflex.dev")),
          **props,
      ),
  )
DataListLabel: |
  lambda **props: rx.data_list.root(
      rx.foreach(
          [["Status", "Authorized"], ["ID", "U-474747"], ["Name", "Developer Success"], ["Email", "foo@reflex.dev"]],
          lambda item: rx.data_list.item(rx.data_list.label(item[0], **props), rx.data_list.value(item[1])),
      ),
  )
DataListValue: |
  lambda **props: rx.data_list.root(
      rx.foreach(
          [["Status", "Authorized"], ["ID", "U-474747"], ["Name", "Developer Success"], ["Email", "foo@reflex.dev"]],
          lambda item: rx.data_list.item(rx.data_list.label(item[0]), rx.data_list.value(item[1], **props)),
      ),
  )
---

```python exec
import reflex as rx
```

# 数据列表（Data List）

`DataList` 组件用于显示键值对，特别适合展示元数据。

`DataList` 需要使用 `rx.data_list.root()` 进行初始化，目前接受数据列表项：`rx.data_list.item`

```python demo
(
    rx.card(
        rx.data_list.root(
            rx.data_list.item(
                rx.data_list.label("Status"),
                rx.data_list.value(
                    rx.badge(
                        "Authorized",
                        variant="soft",
                        radius="full",
                    )
                ),
                align="center",
            ),
            rx.data_list.item(
                rx.data_list.label("ID"),
                rx.data_list.value(rx.code("U-474747")),
            ),
            rx.data_list.item(
                rx.data_list.label("Name"),
                rx.data_list.value("Developer Success"),
                align="center",
            ),
            rx.data_list.item(
                rx.data_list.label("Email"),
                rx.data_list.value(
                    rx.link(
                        "success@reflex.dev",
                        href="mailto:success@reflex.dev",
                    ),
                ),
            ),
            rx.data_list.item(
                rx.data_list.label("Company"),
                rx.data_list.value(
                    rx.link(
                        "Reflex",
                        href="https://reflex.dev",
                    ),
                ),
            ),
        ),
    ),
)
```
