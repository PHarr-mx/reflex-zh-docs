```python exec
from types import SimpleNamespace

import reflex as rx


# Styling for the live chat demos (the user-facing version lives in the `# style.py` block below).
_chat_margin = "15%"
_message_style = {
    "padding": "0.75em 1em",
    "border_radius": "18px",
    "margin_y": "0.35em",
    "max_width": "30em",
    "display": "inline-block",
    "line_height": "1.5",
    "font_size": "0.95rem",
}
style = SimpleNamespace(
    question_style=_message_style
    | {
        "background_color": rx.color("slate", 3),
        "color": rx.color("slate", 12),
        "border": f"1px solid {rx.color('slate', 4)}",
        "margin_left": _chat_margin,
        "border_bottom_right_radius": "6px",
    },
    answer_style=_message_style
    | {
        "background_color": rx.color("accent", 9),
        "color": "white",
        "margin_right": _chat_margin,
        "border_bottom_left_radius": "6px",
    },
    input_style={"border_width": "1px", "width": "350px"},
    button_style={"background_color": rx.color("accent", 9)},
)
```

# AI 聊天应用

**约 30 分钟动手实践** · 用纯 Python 构建一个流式 AI 聊天机器人——UI、状态和 OpenAI 集成尽在一个 Reflex 应用中。

你可以在 [GitHub](https://github.com/reflex-dev/reflex-chat) 上找到本应用的完整源代码。

```python eval
rx.box(
    rx.vstack(
        rx.vstack(
            rx.box(
                rx.text("What is Reflex?", style=style.question_style),
                text_align="right",
                width="100%",
            ),
            rx.box(
                rx.text(
                    "Reflex is a way to build full-stack web apps in pure Python — "
                    "frontend, backend, and state all in one place.",
                    style=style.answer_style,
                ),
                text_align="left",
                width="100%",
            ),
            rx.box(
                rx.text("Can I deploy it?", style=style.question_style),
                text_align="right",
                width="100%",
            ),
            rx.box(
                rx.text(
                    "Yes! A single `reflex deploy` ships it to production.",
                    style=style.answer_style,
                ),
                text_align="left",
                width="100%",
            ),
            spacing="0",
            width="100%",
        ),
        rx.hstack(
            rx.input(
                placeholder="Ask a question",
                style=style.input_style,
                is_disabled=True,
            ),
            rx.button("Ask", style=style.button_style, is_disabled=True),
            spacing="3",
            padding_top="1.5em",
            justify="center",
            width="100%",
        ),
        align="stretch",
        spacing="0",
        width="100%",
        padding="2.5em 4em",
    ),
    border=f"1px solid {rx.color('slate', 5)}",
    border_radius="12px",
    margin_y="1em",
)
```

## 你将学到什么

在本教程中，你将学会如何：

1. 安装 `reflex` 并搭建开发环境。
2. 创建组件来定义和设置 UI 样式。
3. 使用状态（state）为应用添加交互性。
4. 部署你的应用以与他人分享。

## 搭建项目

```md video https://youtube.com/embed/ITOZKzjtjUA?start=175&end=445
# Video: Example of Setting up the Chat App
```

我们将从创建一个新项目并搭建开发环境开始。如果你还没有安装 [uv](https://docs.astral.sh/uv/)，请参见[安装指南](/docs/getting-started/installation)。然后创建一个新的项目目录并搭建 Reflex 应用脚手架：

```bash
mkdir chatapp
cd chatapp
uv init
uv add reflex
uv run reflex init
```

```md alert info
当提示选择模板时，请选择选项 **0** 以创建空白项目。
```

你可以运行模板应用以确保一切正常。

```bash
uv run reflex run
```

你应该能看到应用运行在 [http://localhost:3000](http://localhost:3000)。

Reflex 还会启动后端服务器，负责处理所有状态管理以及与前端的通信。你可以通过访问 [http://localhost:8000/ping](http://localhost:8000/ping) 来测试后端服务器是否正在运行。

现在我们已经搭建好了项目，在下一节中我们将开始构建应用！

## 基础前端

让我们从定义聊天应用的前端开始。在 Reflex 中，前端可以拆分为独立、可复用的组件。更多信息请参见[组件文档](/docs/components/props)。

### 显示问答

我们将修改 `chatapp/chatapp.py` 文件中的 `index` 函数，使其返回一个显示单个问题和答案的组件。

```python demo box
rx.container(
    rx.box(
        "What is Reflex?",
        # The user's question is on the right.
        text_align="right",
    ),
    rx.box(
        "A way to build web apps in pure Python!",
        # The answer is on the left.
        text_align="left",
    ),
)
```

```python
# chatapp.py

import reflex as rx


def index() -> rx.Component:
    return rx.container(
        rx.box(
            "What is Reflex?",
            # The user's question is on the right.
            text_align="right",
        ),
        rx.box(
            "A way to build web apps in pure Python!",
            # The answer is on the left.
            text_align="left",
        ),
    )


# Add state and page to the app.
app = rx.App()
app.add_page(index)
```

组件可以相互嵌套以创建复杂的布局。在这里，我们创建了一个父容器，其中包含两个用于显示问题和答案的 box。

我们还为组件添加了一些基础样式。组件接收关键字参数，称为 [props](/docs/components/props)，用于修改组件的外观和功能。我们使用 `text_align` prop 将文本分别向左和向右对齐。

### 复用组件

现在我们有了一个显示单个问答的组件，可以复用它来显示多个问答。我们将把该组件移到一个独立的函数 `question_answer` 中，并从 `index` 函数中调用它。

```python exec
def qa(question: str, answer: str) -> rx.Component:
    return rx.box(
        rx.box(question, text_align="right"),
        rx.box(answer, text_align="left"),
        margin_y="1em",
    )


qa_pairs = [
    ("What is Reflex?", "A way to build web apps in pure Python!"),
    (
        "What can I make with it?",
        "Anything from a simple website to a complex web app!",
    ),
]


def chat() -> rx.Component:
    qa_pairs = [
        ("What is Reflex?", "A way to build web apps in pure Python!"),
        (
            "What can I make with it?",
            "Anything from a simple website to a complex web app!",
        ),
    ]
    return rx.box(*[qa(question, answer) for question, answer in qa_pairs])
```

```python demo box
rx.container(chat())
```

```python
def qa(question: str, answer: str) -> rx.Component:
    return rx.box(
        rx.box(question, text_align="right"),
        rx.box(answer, text_align="left"),
        margin_y="1em",
    )


def chat() -> rx.Component:
    qa_pairs = [
        ("What is Reflex?", "A way to build web apps in pure Python!"),
        (
            "What can I make with it?",
            "Anything from a simple website to a complex web app!",
        ),
    ]
    return rx.box(*[qa(question, answer) for question, answer in qa_pairs])


def index() -> rx.Component:
    return rx.container(chat())
```

### 聊天输入

现在我们希望为用户提供一种输入问题的方式。为此，我们将使用 [input](/docs/library/forms/input) 组件让用户添加文本，并使用 [button](/docs/library/forms/button) 组件来提交问题。

```python exec
def action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(placeholder="Ask a question"),
        rx.button("Ask"),
    )
```

```python demo box
rx.container(
    chat(),
    action_bar(),
)
```

```python
def action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(placeholder="Ask a question"),
        rx.button("Ask"),
    )


def index() -> rx.Component:
    return rx.container(
        chat(),
        action_bar(),
    )
```

### 设置样式

让我们为应用添加一些样式。有关样式的更多信息，请参见[样式文档](/docs/styling/overview)。为了保持代码整洁，我们将样式移到一个单独的文件 `chatapp/style.py` 中。

```python
# style.py
import reflex as rx

# Common styles for questions and answers.
shadow = "rgba(0, 0, 0, 0.15) 0px 2px 8px"
chat_margin = "20%"
message_style = dict(
    padding="1em",
    border_radius="5px",
    margin_y="0.5em",
    box_shadow=shadow,
    max_width="30em",
    display="inline-block",
)

# Set specific styles for questions and answers.
question_style = message_style | dict(
    margin_left=chat_margin, background_color=rx.color("gray", 4)
)
answer_style = message_style | dict(
    margin_right=chat_margin, background_color=rx.color("accent", 8)
)

# Styles for the action bar.
input_style = dict(
    border_width="1px", padding="0.5em", box_shadow=shadow, width="350px"
)
button_style = dict(background_color=rx.color("accent", 10), box_shadow=shadow)
```

我们将在 `chatapp.py` 中导入这些样式并在组件中使用它们。此时，应用应该看起来像这样：

```python exec
def styled_qa(question: str, answer: str) -> rx.Component:
    return rx.box(
        rx.box(rx.text(question, style=style.question_style), text_align="right"),
        rx.box(rx.text(answer, style=style.answer_style), text_align="left"),
        margin_y="1em",
        width="100%",
    )


def styled_chat() -> rx.Component:
    qa_pairs = [
        ("What is Reflex?", "A way to build web apps in pure Python!"),
        (
            "What can I make with it?",
            "Anything from a simple website to a complex web app!",
        ),
    ]
    return rx.box(*[styled_qa(question, answer) for question, answer in qa_pairs])


def styled_action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(placeholder="Ask a question", style=style.input_style),
        rx.button("Ask", style=style.button_style),
    )
```

```python demo box
rx.center(
    rx.vstack(
        styled_chat(),
        styled_action_bar(),
        align="center",
    )
)
```

```python
# chatapp.py
import reflex as rx

from chatapp import style


def qa(question: str, answer: str) -> rx.Component:
    return rx.box(
        rx.box(rx.text(question, style=style.question_style), text_align="right"),
        rx.box(rx.text(answer, style=style.answer_style), text_align="left"),
        margin_y="1em",
        width="100%",
    )


def chat() -> rx.Component:
    qa_pairs = [
        ("What is Reflex?", "A way to build web apps in pure Python!"),
        (
            "What can I make with it?",
            "Anything from a simple website to a complex web app!",
        ),
    ]
    return rx.box(*[qa(question, answer) for question, answer in qa_pairs])


def action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(placeholder="Ask a question", style=style.input_style),
        rx.button("Ask", style=style.button_style),
    )


def index() -> rx.Component:
    return rx.center(
        rx.vstack(
            chat(),
            action_bar(),
            align="center",
        )
    )


app = rx.App()
app.add_page(index)
```

应用看起来不错了，但它还不太实用！在下一节中，我们将为应用添加一些功能。

## 状态（State）

现在让我们通过添加状态让聊天应用具有交互性。状态是我们定义应用中所有可变变量以及所有能够修改这些变量的函数的地方。你可以在[状态文档](/docs/state/overview)中了解更多关于状态的内容。

### 定义状态

我们将在 `chatapp` 目录中创建一个名为 `state.py` 的新文件。我们的状态将跟踪当前正在被提问的问题以及聊天历史。我们还将定义一个事件处理器 `answer`，用于处理当前问题并将答案添加到聊天历史中。

```python
# state.py
import reflex as rx


class State(rx.State):
    # The current question being asked.
    question: str

    # Keep track of the chat history as a list of (question, answer) tuples.
    chat_history: list[tuple[str, str]]

    @rx.event
    def set_question(self, value: str):
        self.question = value

    @rx.event
    def answer(self):
        # Our chatbot is not very smart right now...
        answer = "I don't know!"
        self.chat_history.append((self.question, answer))
```

### 将状态绑定到组件

现在我们可以在 `chatapp.py` 中导入状态，并在前端组件中引用它。我们将修改 `chat` 组件，使其使用状态而非当前固定的问答内容。

```python exec
class BasicChatState(rx.State):
    question: str = ""
    chat_history: list[tuple[str, str]] = []

    @rx.event
    def set_question(self, value: str):
        self.question = value

    @rx.event
    def answer(self):
        # Our chatbot is not very smart right now...
        answer = "I don't know!"
        self.chat_history.append((self.question, answer))


def qa(question: str, answer: str) -> rx.Component:
    return rx.box(
        rx.box(rx.text(question, style=style.question_style), text_align="right"),
        rx.box(rx.text(answer, style=style.answer_style), text_align="left"),
        margin_y="1em",
        width="100%",
    )


def basic_chat() -> rx.Component:
    return rx.box(
        rx.foreach(
            BasicChatState.chat_history, lambda messages: qa(messages[0], messages[1])
        )
    )


def basic_action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(
            placeholder="Ask a question",
            on_change=BasicChatState.set_question,
            style=style.input_style,
        ),
        rx.button("Ask", on_click=BasicChatState.answer, style=style.button_style),
    )
```

```python demo box
rx.container(
    basic_chat(),
    basic_action_bar(),
)
```

```python
# chatapp.py
from chatapp.state import State


def chat() -> rx.Component:
    return rx.box(
        rx.foreach(State.chat_history, lambda messages: qa(messages[0], messages[1]))
    )


def action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(
            placeholder="Ask a question",
            on_change=State.set_question,
            style=style.input_style,
        ),
        rx.button("Ask", on_click=State.answer, style=style.button_style),
    )
```

普通的 Python `for` 循环无法用于遍历状态 vars，因为这些值会发生变化，在编译时是未知的。相反，我们使用 [foreach](/docs/library/dynamic-rendering/foreach) 组件来遍历聊天历史。

我们还将 input 的 `on_change` 事件绑定到 `set_question` 事件处理器，它会在用户输入时更新 `question` 状态 var。我们将 button 的 `on_click` 事件绑定到 `answer` 事件处理器，它会处理问题并将答案添加到聊天历史中。

### 清空输入框

目前，用户点击按钮后输入框不会被清空。我们可以通过将 input 的值绑定到 `question`（使用 `value=State.question`）来修复这个问题，并在运行 `answer` 事件处理器时清空它（使用 `self.question = ''`）。

```python exec
class ClearingChatState(rx.State):
    question: str = ""
    chat_history: list[tuple[str, str]] = []

    @rx.event
    def set_question(self, value: str):
        self.question = value

    @rx.event
    def answer(self):
        # Our chatbot is not very smart right now...
        answer = "I don't know!"
        self.chat_history.append((self.question, answer))
        # Clear the question input.
        self.question = ""


def clearing_chat() -> rx.Component:
    return rx.box(
        rx.foreach(
            ClearingChatState.chat_history,
            lambda messages: qa(messages[0], messages[1]),
        )
    )


def clearing_action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(
            value=ClearingChatState.question,
            placeholder="Ask a question",
            on_change=ClearingChatState.set_question,
            style=style.input_style,
        ),
        rx.button("Ask", on_click=ClearingChatState.answer, style=style.button_style),
    )
```

```python demo box
rx.container(
    clearing_chat(),
    clearing_action_bar(),
)
```

```python
# chatapp.py
def action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(
            value=State.question,
            placeholder="Ask a question",
            on_change=State.set_question,
            style=style.input_style,
        ),
        rx.button("Ask", on_click=State.answer, style=style.button_style),
    )
```

```python
# state.py
@rx.event
def answer(self):
    # Our chatbot is not very smart right now...
    answer = "I don't know!"
    self.chat_history.append((self.question, answer))
    self.question = ""
```

### 流式文本

通常，状态更新会在事件处理器返回时发送到前端。然而，我们希望在聊天机器人生成文本时进行流式传输。我们可以通过在事件处理器中使用 yield 来实现这一点。更多信息请参见 [yield events 文档](/docs/events/yield-events)。

```python exec
import asyncio


class StreamingChatState(rx.State):
    question: str = ""
    chat_history: list[tuple[str, str]] = []

    @rx.event
    def set_question(self, value: str):
        self.question = value

    @rx.event
    async def answer(self):
        # Our chatbot is not very smart right now...
        answer = "I don't know!"
        self.chat_history.append((self.question, ""))
        # Clear the question input.
        self.question = ""
        # Yield here to clear the frontend input before continuing.
        yield

        for i in range(len(answer)):
            await asyncio.sleep(0.1)
            self.chat_history[-1] = (self.chat_history[-1][0], answer[: i + 1])
            yield


def streaming_chat() -> rx.Component:
    return rx.box(
        rx.foreach(
            StreamingChatState.chat_history,
            lambda messages: qa(messages[0], messages[1]),
        )
    )


def streaming_action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(
            value=StreamingChatState.question,
            placeholder="Ask a question",
            on_change=StreamingChatState.set_question,
            style=style.input_style,
        ),
        rx.button("Ask", on_click=StreamingChatState.answer, style=style.button_style),
    )
```

```python demo box
rx.container(
    streaming_chat(),
    streaming_action_bar(),
)
```

```python
# state.py
import asyncio


async def answer(self):
    # Our chatbot is not very smart right now...
    answer = "I don't know!"
    self.chat_history.append((self.question, ""))

    # Clear the question input.
    self.question = ""
    # Yield here to clear the frontend input before continuing.
    yield

    for i in range(len(answer)):
        # Pause to show the streaming effect.
        await asyncio.sleep(0.1)
        # Add one letter at a time to the output.
        self.chat_history[-1] = (self.chat_history[-1][0], answer[: i + 1])
        yield
```

在下一节中，我们将通过添加 AI 来完成我们的聊天机器人！

## 最终应用

我们将使用 OpenAI 的 API 来赋予聊天机器人一些智能。

### 配置 OpenAI

首先，确保你拥有有效的 OpenAI 订阅，并安装最新的 `openai` 包：

```bash
pip install --upgrade openai
```

然后导出你的 API key，以便应用在运行时能够读取它：

```bash
export OPENAI_API_KEY="sk-..."
```

### 使用 API

让聊天机器人具备智能需要连接到语言模型 API。本节介绍如何集成 OpenAI 的 API 来驱动聊天机器人的回复。

1. 首先，用户输入一个提示词（prompt），它通过 `on_change` 事件处理器进行更新。
2. 接着，当提示词准备就绪后，用户可以选择点击 `Ask` 按钮来提交它，这会触发 `state.py` 文件中的 `State.answer` 方法。
3. 最后，如果该方法被触发，`prompt` 会通过请求发送给 OpenAI 客户端，并返回一个答案，我们可以对其进行裁剪并用于更新聊天历史！

```python
# chatapp.py
def action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(
            value=State.question,
            placeholder="Ask a question",
            # on_change event updates the input as the user types a prompt.
            on_change=State.set_question,
            style=style.input_style,
        ),
        # on_click event triggers the API to send the prompt to OpenAI.
        rx.button("Ask", on_click=State.answer, style=style.button_style),
    )
```

```python
# state.py
import os

from openai import AsyncOpenAI


@rx.event
async def answer(self):
    # Our chatbot has some brains now!
    client = AsyncOpenAI(api_key=os.environ["OPENAI_API_KEY"])

    session = await client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": self.question}],
        stop=None,
        temperature=0.7,
        stream=True,
    )

    # Add to the answer as the chatbot responds.
    answer = ""
    self.chat_history.append((self.question, answer))

    # Clear the question input.
    self.question = ""
    # Yield here to clear the frontend input before continuing.
    yield

    async for item in session:
        if hasattr(item.choices[0].delta, "content"):
            if item.choices[0].delta.content is None:
                # presence of 'None' indicates the end of the response
                break
            answer += item.choices[0].delta.content
            self.chat_history[-1] = (self.chat_history[-1][0], answer)
            yield
```

最终，我们拥有了自己的聊天机器人！

### 最终代码

完成的项目被拆分为三个文件——`chatapp.py` 负责 UI 和应用设置，`state.py` 负责状态和 API 集成，`style.py` 负责样式：

```text
chatapp/
├── chatapp.py
├── state.py
└── style.py
```

`chatapp.py` 文件：

```python
import reflex as rx
from chatapp import style
from chatapp.state import State


def qa(question: str, answer: str) -> rx.Component:
    return rx.box(
        rx.box(rx.text(question, style=style.question_style), text_align="right"),
        rx.box(rx.text(answer, style=style.answer_style), text_align="left"),
        margin_y="1em",
    )


def chat() -> rx.Component:
    return rx.box(
        rx.foreach(
            State.chat_history,
            lambda messages: qa(messages[0], messages[1]),
        )
    )


def action_bar() -> rx.Component:
    return rx.hstack(
        rx.input(
            value=State.question,
            placeholder="Ask a question",
            on_change=State.set_question,
            style=style.input_style,
        ),
        rx.button(
            "Ask",
            on_click=State.answer,
            style=style.button_style,
        ),
    )


def index() -> rx.Component:
    return rx.center(
        rx.vstack(
            chat(),
            action_bar(),
            align="center",
        )
    )


app = rx.App()
app.add_page(index)
```

`state.py` 文件：

```python
import os
from openai import AsyncOpenAI
import reflex as rx


class State(rx.State):
    question: str
    chat_history: list[tuple[str, str]] = []

    @rx.event
    def set_question(self, value: str):
        self.question = value

    async def answer(self):
        client = AsyncOpenAI(api_key=os.environ["OPENAI_API_KEY"])

        # Start streaming completion from OpenAI
        session = await client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[{"role": "user", "content": self.question}],
            temperature=0.7,
            stream=True,
        )

        # Initialize response and update UI
        answer = ""
        self.chat_history.append((self.question, answer))
        self.question = ""
        yield

        # Process streaming response
        async for item in session:
            if hasattr(item.choices[0].delta, "content"):
                if item.choices[0].delta.content is None:
                    break
                answer += item.choices[0].delta.content
                self.chat_history[-1] = (self.chat_history[-1][0], answer)
                yield
```

`style.py` 文件：

```python
import reflex as rx

# Common style base
shadow = "rgba(0, 0, 0, 0.15) 0px 2px 8px"
chat_margin = "20%"
message_style = dict(
    padding="1em",
    border_radius="5px",
    margin_y="0.5em",
    box_shadow=shadow,
    max_width="30em",
    display="inline-block",
)

# Styles for questions and answers
question_style = message_style | dict(
    margin_left=chat_margin,
    background_color=rx.color("gray", 4),
)
answer_style = message_style | dict(
    margin_right=chat_margin,
    background_color=rx.color("accent", 8),
)

# Styles for input elements
input_style = dict(
    border_width="1px", padding="0.5em", box_shadow=shadow, width="350px"
)
button_style = dict(background_color=rx.color("accent", 10), box_shadow=shadow)
```

### 下一步

恭喜你！你已经构建了自己的第一个聊天机器人。从这里开始，你可以阅读文档的其余部分，更详细地了解 Reflex。最好的学习方式就是动手构建，所以试着以本教程为起点，构建你自己的应用吧！

### 还有一件事

借助我们的托管服务，你可以在几分钟内用一条命令部署这个应用。请查看我们的[托管快速入门](https://reflex.dev/docs/hosting/deploy-quick-start/)。
