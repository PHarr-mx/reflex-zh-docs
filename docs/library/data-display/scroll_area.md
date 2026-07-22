---
components:
  - rx.scroll_area

ScrollArea: |
  lambda **props: rx.scroll_area(
      rx.flex(
          rx.text(
              """Three fundamental aspects of typography are legibility, readability, and aesthetics. Although in a non-technical sense "legible" and "readable"are often used synonymously, typographically they are separate but related concepts.""",
              size="5",
          ),
          rx.text(
              """Legibility describes how easily individual characters can be distinguished from one another. It is described by Walter Tracy as "the quality of being decipherable and recognisable". For instance, if a "b" and an "h", or a "3" and an "8", are difficult to distinguish at small sizes, this is a problem of legibility.""",
              size="5",
          ),
          direction="column",
          spacing="4",
          height="100px",
          width="50%",
      ),
      **props
  )
---

```python exec
import random
import reflex as rx
```

# 滚动区域（Scroll Area）

使用原生功能实现的自定义样式、跨浏览器可滚动区域。

## 基本示例

```python demo
rx.scroll_area(
    rx.flex(
        rx.text(
            """Three fundamental aspects of typography are legibility, readability, and
        aesthetics. Although in a non-technical sense "legible" and "readable"
        are often used synonymously, typographically they are separate but
        related concepts.""",
        ),
        rx.text(
            """Legibility describes how easily individual characters can be
        distinguished from one another. It is described by Walter Tracy as "the
        quality of being decipherable and recognisable". For instance, if a "b"
        and an "h", or a "3" and an "8", are difficult to distinguish at small
        sizes, this is a problem of legibility.""",
        ),
        rx.text(
            """Typographers are concerned with legibility insofar as it is their job to
        select the correct font to use. Brush Script is an example of a font
        containing many characters that might be difficult to distinguish. The
        selection of cases influences the legibility of typography because using
        only uppercase letters (all-caps) reduces legibility.""",
        ),
        direction="column",
        spacing="4",
    ),
    type="always",
    scrollbars="vertical",
    style={"height": 180},
)
```

## 控制可滚动轴

使用 `scrollbars` 属性来限制可滚动的轴。该属性可取值为 `"vertical" | "horizontal" | "both"`。

```python demo
rx.grid(
    rx.scroll_area(
        rx.flex(
            rx.text(
                """Three fundamental aspects of typography are legibility, readability, and
        aesthetics. Although in a non-technical sense "legible" and "readable"
        are often used synonymously, typographically they are separate but
        related concepts.""",
                size="2",
                trim="both",
            ),
            rx.text(
                """Legibility describes how easily individual characters can be
        distinguished from one another. It is described by Walter Tracy as "the
        quality of being decipherable and recognisable". For instance, if a "b"
        and an "h", or a "3" and an "8", are difficult to distinguish at small
        sizes, this is a problem of legibility.""",
                size="2",
                trim="both",
            ),
            padding="8px",
            padding_right="48px",
            direction="column",
            spacing="4",
        ),
        type="always",
        scrollbars="vertical",
        style={"height": 150},
    ),
    rx.scroll_area(
        rx.flex(
            rx.text(
                """Three fundamental aspects of typography are legibility, readability, and
        aesthetics. Although in a non-technical sense "legible" and "readable"
        are often used synonymously, typographically they are separate but
        related concepts.""",
                size="2",
                trim="both",
            ),
            rx.text(
                """Legibility describes how easily individual characters can be
        distinguished from one another. It is described by Walter Tracy as "the
        quality of being decipherable and recognisable". For instance, if a "b"
        and an "h", or a "3" and an "8", are difficult to distinguish at small
        sizes, this is a problem of legibility.""",
                size="2",
                trim="both",
            ),
            padding="8px",
            spacing="4",
            style={"width": 700},
        ),
        type="always",
        scrollbars="horizontal",
        style={"height": 150},
    ),
    rx.scroll_area(
        rx.flex(
            rx.text(
                """Three fundamental aspects of typography are legibility, readability, and
        aesthetics. Although in a non-technical sense "legible" and "readable"
        are often used synonymously, typographically they are separate but
        related concepts.""",
                size="2",
                trim="both",
            ),
            rx.text(
                """Legibility describes how easily individual characters can be
        distinguished from one another. It is described by Walter Tracy as "the
        quality of being decipherable and recognisable". For instance, if a "b"
        and an "h", or a "3" and an "8", are difficult to distinguish at small
        sizes, this is a problem of legibility.""",
                size="2",
                trim="both",
            ),
            padding="8px",
            spacing="4",
            style={"width": 400},
        ),
        type="always",
        scrollbars="both",
        style={"height": 150},
    ),
    columns="3",
    spacing="2",
)
```

## 设置滚动条类型

`type` 属性描述滚动条可见性的行为。

`auto` 表示当内容在对应方向上溢出时，滚动条可见。

`always` 表示无论内容是否溢出，滚动条始终可见。

`scroll` 表示当用户沿对应方向滚动时，滚动条可见。

`hover` 表示当用户沿对应方向滚动以及当用户将鼠标悬停在滚动区域上时，滚动条可见。

```python demo
rx.grid(
    rx.scroll_area(
        rx.flex(
            rx.text("type = 'auto'", weight="bold"),
            rx.text(
                """Legibility describes how easily individual characters can be
        distinguished from one another. It is described by Walter Tracy as "the
        quality of being decipherable and recognisable". For instance, if a "b"
        and an "h", or a "3" and an "8", are difficult to distinguish at small
        sizes, this is a problem of legibility.""",
                size="2",
                trim="both",
            ),
            padding="8px",
            direction="column",
            spacing="4",
        ),
        type="auto",
        scrollbars="vertical",
        style={"height": 150},
    ),
    rx.scroll_area(
        rx.flex(
            rx.text("type = 'always'", weight="bold"),
            rx.text(
                """Legibility describes how easily individual characters can be
        distinguished from one another. It is described by Walter Tracy as "the
        quality of being decipherable and recognisable". For instance, if a "b"
        and an "h", or a "3" and an "8", are difficult to distinguish at small
        sizes, this is a problem of legibility.""",
                size="2",
                trim="both",
            ),
            padding="8px",
            direction="column",
            spacing="4",
        ),
        type="always",
        scrollbars="vertical",
        style={"height": 150},
    ),
    rx.scroll_area(
        rx.flex(
            rx.text("type = 'scroll'", weight="bold"),
            rx.text(
                """Legibility describes how easily individual characters can be
        distinguished from one another. It is described by Walter Tracy as "the
        quality of being decipherable and recognisable". For instance, if a "b"
        and an "h", or a "3" and an "8", are difficult to distinguish at small
        sizes, this is a problem of legibility.""",
                size="2",
                trim="both",
            ),
            padding="8px",
            direction="column",
            spacing="4",
        ),
        type="scroll",
        scrollbars="vertical",
        style={"height": 150},
    ),
    rx.scroll_area(
        rx.flex(
            rx.text("type = 'hover'", weight="bold"),
            rx.text(
                """Legibility describes how easily individual characters can be
        distinguished from one another. It is described by Walter Tracy as "the
        quality of being decipherable and recognisable". For instance, if a "b"
        and an "h", or a "3" and an "8", are difficult to distinguish at small
        sizes, this is a problem of legibility.""",
                size="2",
                trim="both",
            ),
            padding="8px",
            direction="column",
            spacing="4",
        ),
        type="hover",
        scrollbars="vertical",
        style={"height": 150},
    ),
    columns="4",
    spacing="2",
)
```
