```python exec
import asyncio
from typing import Any
import reflex as rx
```

# 浏览器 JavaScript（Browser Javascript）

Reflex 将你的前端代码（定义为 Python 函数）编译成一个在用户浏览器中运行的 JavaScript Web 应用。在某些情况下，你可能需要提供自定义 JavaScript 代码来与 Web API 交互、使用某些第三方库，或封装未通过 Reflex Python API 暴露的底层功能。

```md alert
# 避免自定义 JavaScript

Reflex 应用中的自定义 JavaScript 代码会带来维护挑战，因为它更难调试，并且可能在不同的 Reflex 版本之间不稳定。

尽可能优先使用 Python API，如果你需要当前未提供的额外功能，请提交 issue。
```

## 执行脚本

有四种方式可以在你的 Reflex 应用中执行自定义 JavaScript 代码：

- `rx.script` - 通过 `next/script` 注入脚本，以高效加载内联和外部 JavaScript 代码。在[组件库](/docs/library/other/script)中有进一步描述。
  - 这些组件可以直接包含在页面主体中，或者可以
    传递给 `rx.App(head_components=[rx.script(...)])` 以包含在
    所有页面的 `<Head>` 标签中。
- `rx.call_script` - 一个事件处理程序，用于计算任意 JavaScript 代码，
  并可选地将结果返回给另一个事件处理程序。

以上两种方法可以协同工作，先加载外部脚本，然后
在响应用户事件时调用其中定义的函数。

以下两种方法面向封装组件，在[封装 React](/docs/wrapping-react/overview)
部分中有示例描述。

- `rx.Component` 子类中的 `_get_hooks` 和 `_get_custom_code`
- 带有 `_var_is_local=False` 的 `Var.create`

## 内联脚本

`rx.script` 组件是加载内联 JavaScript 以更好地控制前端行为的推荐方式。

脚本中的函数和变量可以通过 `rx.call_script` 接口从后端事件
处理程序或前端事件触发器访问。

```python demo exec
class SoundEffectState(rx.State):
    @rx.event(background=True)
    async def delayed_play(self):
        await asyncio.sleep(1)
        return rx.call_script("playFromStart(button_sfx)")


def sound_effect_demo():
    return rx.hstack(
        rx.script("""
            var button_sfx = new Audio("/vintage-button-sound-effect.mp3")
            function playFromStart (sfx) {sfx.load(); sfx.play()}"""),
        rx.button(
            "Play Immediately", on_click=rx.call_script("playFromStart(button_sfx)")
        ),
        rx.button("Play Later", on_click=SoundEffectState.delayed_play),
    )
```

## 外部脚本

外部脚本可以从 `assets` 目录或 CDN URL 加载，然后通过
`rx.call_script` 控制。

```python demo
rx.vstack(
    rx.script(
        src="https://cdn.jsdelivr.net/gh/scottschiller/snowstorm@snowstorm_20131208/snowstorm-min.js",
    ),
    rx.script("""
        window.addEventListener('load', function() {
            if (typeof snowStorm !== 'undefined') {
                snowStorm.autoStart = false;
                snowStorm.snowColor = '#111';
            }
        });
    """),
    rx.button("Start Duststorm", on_click=rx.call_script("snowStorm.start()")),
    rx.button("Toggle Duststorm", on_click=rx.call_script("snowStorm.toggleSnow()")),
)
```

## 访问客户端值

`rx.call_script` 函数接受一个 `callback` 参数，该参数期望一个
带有一个参数的 Event Handler，它将接收计算 JavaScript 代码的结果。这可以用于访问客户端值，例如
`window.location` 或当前滚动位置，或任何先前定义的值。

```python demo exec
class WindowState(rx.State):
    location: dict[str, str] = {}
    scroll_position: dict[str, int] = {}

    def update_location(self, location):
        self.location = location

    def update_scroll_position(self, scroll_position):
        self.scroll_position = {
            "x": scroll_position[0],
            "y": scroll_position[1],
        }

    @rx.event
    def get_client_values(self):
        return [
            rx.call_script("window.location", callback=WindowState.update_location),
            rx.call_script(
                "[window.scrollX, window.scrollY]",
                callback=WindowState.update_scroll_position,
            ),
        ]


def window_state_demo():
    return rx.vstack(
        rx.button("Update Values", on_click=WindowState.get_client_values),
        rx.text(f"Scroll Position: {WindowState.scroll_position.to_string()}"),
        rx.text("window.location:"),
        rx.text_area(value=WindowState.location.to_string(), is_read_only=True),
        on_mount=WindowState.get_client_values,
    )
```

```md alert
# 允许的回调值

`callback` 参数可以是带有一个参数的 `EventHandler`，或带有一个参数并返回 `EventHandler` 的 lambda。
如果 callback 为 None，则不会触发任何事件。
```

## 使用 React Hooks

要在 Reflex 应用中直接使用 React Hooks，你必须继承 `rx.Component` 的子类，
通常当 hook 功能没有视觉元素时使用 `rx.Fragment`。hook 代码由 `add_hooks` 方法返回，该方法期望
返回一个 `list[str]`，包含将插入到页面组件（即 render 函数本身）中的 JavaScript 代码。

对于必须在组件 render 函数之外定义的支持代码，使用 `_get_custom_code`。

以下示例使用 `useEffect` 在 `document` 对象上注册全局热键，
然后在按下特定键时触发事件。

```python demo exec
import dataclasses

from reflex.utils import imports


@dataclasses.dataclass
class KeyEvent:
    """Interface of Javascript KeyboardEvent"""

    key: str = ""


def key_event_spec(ev: rx.Var[KeyEvent]) -> tuple[rx.Var[str]]:
    # Takes the event object and returns the key pressed to send to the state
    return (ev.key,)


class GlobalHotkeyState(rx.State):
    key: str = ""

    @rx.event
    def update_key(self, key):
        self.key = key


class GlobalHotkeyWatcher(rx.Fragment):
    """A component that listens for key events globally."""

    # The event handler that will be called
    on_key_down: rx.EventHandler[key_event_spec]

    def add_imports(self) -> imports.ImportDict:
        """Add the imports for the component."""
        return {
            "react": [imports.ImportVar(tag="useEffect")],
        }

    def add_hooks(self) -> list[str | rx.Var]:
        """Add the hooks for the component."""
        return [
            """
            useEffect(() => {
                const handle_key = %s;
                document.addEventListener("keydown", handle_key, false);
                return () => {
                    document.removeEventListener("keydown", handle_key, false);
                }
            })
            """
            % str(rx.Var.create(self.event_triggers["on_key_down"]))
        ]


def global_key_demo():
    return rx.vstack(
        GlobalHotkeyWatcher.create(
            keys=["a", "s", "d", "w"],
            on_key_down=lambda key: rx.cond(
                rx.Var.create(["a", "s", "d", "w"]).contains(key),
                GlobalHotkeyState.update_key(key),
                rx.console_log(key),
            ),
        ),
        rx.text("Press a, s, d or w to trigger an event"),
        rx.heading(f"Last watched key pressed: {GlobalHotkeyState.key}", as_="h2"),
    )
```

此代码片段也可以通过 pip 导入：[reflex-global-hotkey](https://pypi.org/project/reflex-global-hotkey/)。
