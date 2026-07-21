# Reflex 工作原理

我们将以下面这个展示 Github 个人资料图片的基础应用为例，来解释架构的不同部分。

```python demo exec
import requests
import reflex as rx


class GithubState(rx.State):
    url: str = "https://github.com/reflex-dev"
    profile_image: str = "https://avatars.githubusercontent.com/u/104714959"

    @rx.event
    def set_profile(self, username: str):
        if username == "":
            return
        try:
            github_data = requests.get(
                f"https://api.github.com/users/{username}"
            ).json()
        except:
            return
        self.url = github_data["url"]
        self.profile_image = github_data["avatar_url"]


def index():
    return rx.hstack(
        rx.link(
            rx.avatar(src=GithubState.profile_image),
            href=GithubState.url,
        ),
        rx.input(
            placeholder="Your Github username",
            on_blur=GithubState.set_profile,
        ),
    )
```

## Reflex 架构

全栈 Web 应用由前端和后端组成。前端是用户界面，以网页形式在用户浏览器中运行。后端处理逻辑和状态管理（如数据库和 API），在服务器上运行。

在传统的 Web 开发中，这两者通常是两个独立的应用，并且常常使用不同的框架或语言编写。例如，你可能将 Flask 后端与 React 前端结合起来。使用这种方法，你需要维护两个独立的应用，并最终编写大量样板代码来连接前端和后端。

我们希望通过在单个代码库中同时定义前端和后端，同时使用 Python 处理所有事情，来简化这一过程。开发者只需关注应用的逻辑，而无需关心底层的实现细节。

### 概要

在底层，Reflex 应用会编译为一个 [React](https://react.dev) 前端应用和一个 [FastAPI](https://github.com/tiangolo/fastapi) 后端应用。只有 UI 被编译为 Javascript；所有的应用逻辑和状态管理都保持在 Python 中，并在服务器上运行。Reflex 使用 [WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) 将事件从前端发送到后端，以及将状态更新从后端发送到前端。

下面的图表提供了 Reflex 应用如何工作的详细概览。我们将在接下来的章节中更详细地介绍每个部分。


```python eval
rx.el.a(
    rx.image(
        src="https://web.reflex-assets.dev/other/architecture.webp",
        alt="Reflex app architecture diagram",
    ),
    href="https://web.reflex-assets.dev/other/architecture.webp",
    target="_blank",
)
```

```python eval
rx.box(height="1em")
```

## 前端

我们希望 Reflex 应用在最终用户看来感觉像传统的 Web 应用，同时对开发者来说易于构建和维护。为此，我们建立在成熟且流行的 Web 技术之上。

当你运行 `uv run reflex run` 时，Reflex 会将前端编译为一个单页 [Next.js](https://nextjs.org) 应用，并在一个（默认 `3000`）端口上提供服务，你可以通过浏览器访问它。

前端的任务是反映应用的状态，并在用户与 UI 交互时将事件发送到后端。前端不运行任何实际的逻辑。

### 组件

Reflex 前端使用组件构建，这些组件可以组合在一起来创建复杂的 UI。我们不使用混合了 HTML 和 Python 的模板语言，而是直接使用 Python 函数来定义 UI。

```python
def index():
    return rx.hstack(
        rx.link(
            rx.avatar(src=GithubState.profile_image),
            href=GithubState.url,
        ),
        rx.input(
            placeholder="Your Github username",
            on_blur=GithubState.set_profile,
        ),
    )
```

在我们的示例应用中，有像 `rx.hstack`、`rx.avatar` 和 `rx.input` 这样的组件。这些组件可以有影响其外观和功能的不同 **props**——例如 `rx.input` 组件有一个 `placeholder` prop 用于显示默认文本。

我们可以通过事件（如 `on_blur`）让组件响应用户的交互，我们将在下面进一步讨论。

在底层，这些组件编译为 React 组件。例如，上面的代码编译为以下 React 代码：

```jsx
<HStack>
    <Link href={GithubState.url}>
        <Avatar src={GithubState.profile_image}/>
    </Link>
    <Input
        placeholder="Your Github username"
        // This would actually be a websocket call to the backend.
        onBlur={GithubState.set_profile}
    >
</HStack>
```

我们的许多核心组件基于 [Radix](https://radix-ui.com/)，这是一个流行的 React 组件库。我们还有许多用于图表、数据表格等的其他组件。

我们选择 React 是因为它是一个拥有庞大生态系统的流行库。我们的目标不是重新创建 Web 生态系统，而是让 Python 开发者能够访问它。

这也允许我们的用户在找不到所需组件时引入自己的组件。用户可以[包装自己的 React 组件](/docs/wrapping-react/overview)，然后[发布](/docs/custom-components/overview)供其他人使用。随着时间的推移，我们将建立我们的[第三方组件生态系统](/docs/custom-components/overview)，以便用户可以轻松找到并使用其他人构建的组件。

### 样式

我们希望确保 Reflex 应用开箱即用就很好看，同时仍让开发者完全控制其应用的外观。

我们有一个核心的[主题系统](/docs/styling/theming)，允许你在应用中设置高层次的样式选项，如深色模式和强调色，使其具有统一的外观和感觉。

除此之外，Reflex 组件可以使用完整的 CSS 功能进行样式设置。我们利用 [Emotion](https://emotion.sh/docs/introduction) 库来实现"Python 中的 CSS"式样式设置，因此你可以将任何 CSS prop 作为关键字参数传递给组件。这还包括通过传递值列表来实现[响应式 props](/docs/styling/responsive)。

## 后端

现在让我们看看我们是如何为应用添加交互性的。

在 Reflex 中，只有前端会编译为 Javascript 并在用户浏览器中运行，而所有的状态和逻辑都保持在 Python 中并在服务器上运行。当你运行 `uv run reflex run` 时，我们会启动一个 FastAPI 服务器（默认端口 `8000`），前端通过 WebSocket 与之连接。

所有的状态和逻辑都在一个 `State` 类中定义。

```python
class GithubState(rx.State):
    url: str = "https://github.com/reflex-dev"
    profile_image: str = "https://avatars.githubusercontent.com/u/104714959"

    def set_profile(self, username: str):
        if username == "":
            return
        github_data = requests.get(f"https://api.github.com/users/{username}").json()
        self.url = github_data["url"]
        self.profile_image = github_data["avatar_url"]
```

状态由 **vars** 和 **事件处理器（event handlers）** 组成。

Vars 是应用中可能随时间变化的任何值。它们被定义为 `State` 类上的类属性，可以是任何可以序列化为 JSON 的 Python 类型。在我们的示例中，`url` 和 `profile_image` 就是 vars。

事件处理器是 `State` 类中的方法，当用户与 UI 交互时被调用。它们是我们在 Reflex 中修改 vars 的唯一方式，可以在响应诸如点击按钮或在文本框中输入等用户操作时被调用。在我们的示例中，`set_profile` 是一个更新 `url` 和 `profile_image` vars 的事件处理器。

由于事件处理器在后端运行，你可以在其中使用任何 Python 库。在我们的示例中，我们使用 `requests` 库调用 Github 的 API 来获取用户的个人资料图片。

## 事件处理

现在我们来谈谈有趣的部分——我们如何处理事件和状态更新。

通常，在编写 Web 应用时，你必须编写大量样板代码来连接前端和后端。使用 Reflex，你无需担心这一点——我们为你处理前端和后端之间的通信。开发者只需编写事件处理器的逻辑，当 vars 更新时，UI 会自动更新。

你可以参考上面的图表来直观地了解这个过程。让我们以 Github 个人资料图片示例来逐步说明。

### 事件触发器

用户可以通过许多方式与 UI 交互，例如点击按钮、在文本框中输入或悬停在元素上。在 Reflex 中，我们称这些为**事件触发器（event triggers）**。

```python
rx.input(
    placeholder="Your Github username",
    on_blur=GithubState.set_profile,
)
```

在我们的示例中，我们将 `on_blur` 事件触发器绑定到 `set_profile` 事件处理器。这意味着当用户在输入字段中输入文本然后点击其他地方时，`set_profile` 事件处理器就会被调用。

### 事件队列

在前端，我们维护一个所有待处理事件的事件队列。一个事件由三个主要数据部分组成：

- **client token**：每个客户端（浏览器标签页）都有一个唯一的 token 用于标识自己。这让后端知道要更新哪个状态。
- **event handler**：要在状态上运行的事件处理器。
- **arguments**：传递给事件处理器的参数。

假设我在输入框中输入我的用户名 "picklelo"。在这个例子中，我们的事件看起来会是这样的：

```json
{
  "client_token": "abc123",
  "event_handler": "GithubState.set_profile",
  "arguments": ["picklelo"]
}
```

在前端，我们维护一个所有待处理事件的事件队列。

当一个事件被触发时，它会被添加到队列中。我们有一个 `processing` 标志来确保一次只处理一个事件。这确保了状态始终保持一致，不会出现两个事件处理器同时修改状态导致的竞态条件。

```md alert info
# 但也有例外情况，例如[后台事件](/docs/events/background-events)允许你在后台运行事件而不会阻塞 UI。
```

一旦事件准备好被处理，它就会通过 WebSocket 连接发送到后端。

### 状态管理器

当事件被接收后，它会在后端被处理。

Reflex 使用一个**状态管理器（state manager）**，它维护客户端 token 与其状态之间的映射。默认情况下，状态管理器只是一个内存中的字典，但可以扩展为使用数据库或缓存。在生产环境中，我们使用 Redis 作为状态管理器。

### 事件处理

一旦我们获取到用户的状态，下一步就是使用参数运行事件处理器。

```python
def set_profile(self, username: str):
    if username == "":
        return
    github_data = requests.get(f"https://api.github.com/users/{username}").json()
    self.url = github_data["url"]
    self.profile_image = github_data["avatar_url"]
```

在我们的示例中，`set_profile` 事件处理器在用户的状态上运行。它调用 Github 的 API 获取用户的个人资料图片，然后更新状态的 `url` 和 `profile_image` vars。

### 状态更新

每次事件处理器返回（或 [yield](/docs/events/yield-events)）时，我们都会将状态保存在状态管理器中，并将**状态更新**发送到前端以更新 UI。

为了在状态增长时保持性能，Reflex 内部会跟踪在事件处理器期间被更新的 vars（**脏 vars**）。当事件处理器处理完成时，我们会找出所有脏 vars 并创建一个状态更新发送到前端。

在我们的例子中，状态更新看起来可能是这样的：

```json
{
  "url": "https://github.com/picklelo",
  "profile_image": "https://avatars.githubusercontent.com/u/104714959"
}
```

我们将新状态存储在状态管理器中，然后将状态更新发送到前端。前端随后更新 UI 以反映新的状态。在我们的示例中，新的 Github 个人资料图片就会被显示出来。
