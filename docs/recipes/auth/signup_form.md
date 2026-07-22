```python exec
import reflex as rx
```

# 注册表单（Sign up Form）

注册表单是 Web 应用程序中的常见组件。它允许用户创建账户并访问应用程序的功能。此页面提供了一些你可以在应用程序中使用的注册表单示例。

## 默认

```python demo exec toggle
def signup_default() -> rx.Component:
    return rx.card(
        rx.vstack(
            rx.center(
                rx.image(
                    src="https://web.reflex-assets.dev/other/logo.jpg",
                    alt="Reflex logo",
                    width="2.5em",
                    height="auto",
                    border_radius="25%",
                ),
                rx.heading(
                    "Create an account",
                    size="6",
                    as_="h2",
                    text_align="center",
                    width="100%",
                ),
                direction="column",
                spacing="5",
                width="100%",
            ),
            rx.vstack(
                rx.text(
                    "Email address",
                    size="3",
                    weight="medium",
                    text_align="left",
                    width="100%",
                ),
                rx.input(
                    placeholder="user@reflex.dev", type="email", size="3", width="100%"
                ),
                justify="start",
                spacing="2",
                width="100%",
            ),
            rx.vstack(
                rx.text(
                    "Password",
                    size="3",
                    weight="medium",
                    text_align="left",
                    width="100%",
                ),
                rx.input(
                    placeholder="Enter your password",
                    type="password",
                    size="3",
                    width="100%",
                ),
                justify="start",
                spacing="2",
                width="100%",
            ),
            rx.box(
                rx.checkbox(
                    "Agree to Terms and Conditions", default_checked=True, spacing="2"
                ),
                width="100%",
            ),
            rx.button("Register", size="3", width="100%"),
            rx.center(
                rx.text("Already registered?", size="3"),
                rx.link("Sign in", href="#", size="3"),
                opacity="0.8",
                spacing="2",
                direction="row",
            ),
            spacing="6",
            width="100%",
        ),
        size="4",
        max_width="28em",
        width="100%",
    )
```

## 图标

```python demo exec toggle
def signup_default_icons() -> rx.Component:
    return rx.card(
        rx.vstack(
            rx.center(
                rx.image(
                    src="https://web.reflex-assets.dev/other/logo.jpg",
                    alt="Reflex logo",
                    width="2.5em",
                    height="auto",
                    border_radius="25%",
                ),
                rx.heading(
                    "Create an account",
                    size="6",
                    as_="h2",
                    text_align="center",
                    width="100%",
                ),
                direction="column",
                spacing="5",
                width="100%",
            ),
            rx.vstack(
                rx.text(
                    "Email address",
                    size="3",
                    weight="medium",
                    text_align="left",
                    width="100%",
                ),
                rx.input(
                    rx.input.slot(rx.icon("user")),
                    placeholder="user@reflex.dev",
                    type="email",
                    size="3",
                    width="100%",
                ),
                justify="start",
                spacing="2",
                width="100%",
            ),
            rx.vstack(
                rx.text(
                    "Password",
                    size="3",
                    weight="medium",
                    text_align="left",
                    width="100%",
                ),
                rx.input(
                    rx.input.slot(rx.icon("lock")),
                    placeholder="Enter your password",
                    type="password",
                    size="3",
                    width="100%",
                ),
                justify="start",
                spacing="2",
                width="100%",
            ),
            rx.box(
                rx.checkbox(
                    "Agree to Terms and Conditions", default_checked=True, spacing="2"
                ),
                width="100%",
            ),
            rx.button("Register", size="3", width="100%"),
            rx.center(
                rx.text("Already registered?", size="3"),
                rx.link("Sign in", href="#", size="3"),
                opacity="0.8",
                spacing="2",
                direction="row",
                width="100%",
            ),
            spacing="6",
            width="100%",
        ),
        max_width="28em",
        size="4",
        width="100%",
    )
```

## 第三方认证

```python demo exec toggle
def signup_single_thirdparty() -> rx.Component:
    return rx.card(
        rx.vstack(
            rx.flex(
                rx.image(
                    src="https://web.reflex-assets.dev/other/logo.jpg",
                    alt="Reflex logo",
                    width="2.5em",
                    height="auto",
                    border_radius="25%",
                ),
                rx.heading(
                    "Create an account",
                    size="6",
                    as_="h2",
                    text_align="left",
                    width="100%",
                ),
                rx.hstack(
                    rx.text("Already registered?", size="3", text_align="left"),
                    rx.link("Sign in", href="#", size="3"),
                    spacing="2",
                    opacity="0.8",
                    width="100%",
                ),
                direction="column",
                justify="start",
                spacing="4",
                width="100%",
            ),
            rx.vstack(
                rx.text(
                    "Email address",
                    size="3",
                    weight="medium",
                    text_align="left",
                    width="100%",
                ),
                rx.input(
                    rx.input.slot(rx.icon("user")),
                    placeholder="user@reflex.dev",
                    type="email",
                    size="3",
                    width="100%",
                ),
                justify="start",
                spacing="2",
                width="100%",
            ),
            rx.vstack(
                rx.text(
                    "Password",
                    size="3",
                    weight="medium",
                    text_align="left",
                    width="100%",
                ),
                rx.input(
                    rx.input.slot(rx.icon("lock")),
                    placeholder="Enter your password",
                    type="password",
                    size="3",
                    width="100%",
                ),
                justify="start",
                spacing="2",
                width="100%",
            ),
            rx.box(
                rx.checkbox(
                    "Agree to Terms and Conditions", default_checked=True, spacing="2"
                ),
                width="100%",
            ),
            rx.button("Register", size="3", width="100%"),
            rx.hstack(
                rx.divider(margin="0"),
                rx.text("Or continue with", white_space="nowrap", weight="medium"),
                rx.divider(margin="0"),
                align="center",
                width="100%",
            ),
            rx.button(
                rx.text("GH", weight="bold"),
                "Sign in with GitHub",
                variant="outline",
                size="3",
                width="100%",
            ),
            spacing="6",
            width="100%",
        ),
        size="4",
        max_width="28em",
        width="100%",
    )
```

## 多个第三方认证

```python demo exec toggle
def signup_multiple_thirdparty() -> rx.Component:
    return rx.card(
        rx.vstack(
            rx.flex(
                rx.image(
                    src="https://web.reflex-assets.dev/other/logo.jpg",
                    alt="Reflex logo",
                    width="2.5em",
                    height="auto",
                    border_radius="25%",
                ),
                rx.heading("Create an account", size="6", as_="h2", width="100%"),
                rx.hstack(
                    rx.text("Already registered?", size="3", text_align="left"),
                    rx.link("Sign in", href="#", size="3"),
                    spacing="2",
                    opacity="0.8",
                    width="100%",
                ),
                justify="start",
                direction="column",
                spacing="4",
                width="100%",
            ),
            rx.vstack(
                rx.text(
                    "Email address",
                    size="3",
                    weight="medium",
                    text_align="left",
                    width="100%",
                ),
                rx.input(
                    rx.input.slot(rx.icon("user")),
                    placeholder="user@reflex.dev",
                    type="email",
                    size="3",
                    width="100%",
                ),
                justify="start",
                spacing="2",
                width="100%",
            ),
            rx.vstack(
                rx.text(
                    "Password",
                    size="3",
                    weight="medium",
                    text_align="left",
                    width="100%",
                ),
                rx.input(
                    rx.input.slot(rx.icon("lock")),
                    placeholder="Enter your password",
                    type="password",
                    size="3",
                    width="100%",
                ),
                justify="start",
                spacing="2",
                width="100%",
            ),
            rx.box(
                rx.checkbox(
                    "Agree to Terms and Conditions", default_checked=True, spacing="2"
                ),
                width="100%",
            ),
            rx.button("Register", size="3", width="100%"),
            rx.hstack(
                rx.divider(margin="0"),
                rx.text("Or continue with", white_space="nowrap", weight="medium"),
                rx.divider(margin="0"),
                align="center",
                width="100%",
            ),
            rx.center(
                rx.icon_button(rx.text("GH", weight="bold"), variant="soft", size="3"),
                rx.icon_button(rx.text("f", weight="bold"), variant="soft", size="3"),
                rx.icon_button(rx.text("X", weight="bold"), variant="soft", size="3"),
                spacing="4",
                direction="row",
                width="100%",
            ),
            spacing="6",
            width="100%",
        ),
        size="4",
        max_width="28em",
        width="100%",
    )
```
