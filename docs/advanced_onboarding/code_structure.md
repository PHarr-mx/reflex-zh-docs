
# 项目结构（进阶）

## 应用模块

Reflex 基于配置中的 `app_name` 导入主应用模块，该模块**必须定义一个模块级全局变量 `app`，作为 `rx.App` 的实例**。

主应用模块负责导入构成应用的所有其他模块，并定义 `app = rx.App()`。

**所有其他包含页面、状态和模型的模块，都必须被主应用模块或包导入**，否则 Reflex 不会将它们包含在编译输出中。

## 将应用拆分为更小的部分

随着应用的扩展，有效的组织至关重要。这可以通过将应用拆分为更小、更易于管理的模块，并将它们组织到避免循环依赖的逻辑包中来实现。

在接下来的文档中，将有一个 `app_name` 为 `example_big_app` 的应用。主模块将是 `example_big_app/example_big_app.py`。

在[整合在一起](#putting-it-all-together)部分，有一个项目文件夹结构的图示，可以帮助你理解下面的示例。

### 页面包：`example_big_app/pages`

所有复杂的应用都会有多个页面，因此建议将 `example_big_app/pages` 创建为一个包。

1. 这个包应该为应用中的每个页面包含一个模块。
2. 如果某个特定页面依赖于状态，那么子状态（substate）应该定义在与页面相同的模块中。
3. 返回页面的函数应使用 `rx.page()` 装饰器，以便将其添加为应用中的路由。

```python
import reflex as rx

from ..state import AuthState


class LoginState(AuthState):
    @rx.event
    def handle_submit(self, form_data):
        self.logged_in = authenticate(form_data["username"], form_data["password"])


def login_field(name: str, **input_props):
    return rx.hstack(
        rx.text(name.capitalize()),
        rx.input(name=name, **input_props),
        width="100%",
        justify="between",
    )


@rx.page(route="/login")
def login():
    return rx.card(
        rx.form(
            rx.vstack(
                login_field("username"),
                login_field("password", type="password"),
                rx.button("Login"),
                width="100%",
                justify="center",
            ),
            on_submit=LoginState.handle_submit,
        ),
    )
```

### 模板化：`example_big_app/template.py`

大多数应用在页面之间保持一致的布局和结构。在单独的模块中定义这种通用结构，有助于在构建各个页面时方便地共享和复用。

**最佳实践**

1. 将公共的前端 UI 元素提取为一个返回组件的函数。
2. 如果一个函数接受一个返回组件的函数作为参数，它可以像下面所示那样用作装饰器。

```python
from typing import Callable

import reflex as rx

from .components.menu import menu
from .components.navbar import navbar


def template(page: Callable[[], rx.Component]) -> rx.Component:
    return rx.vstack(
        navbar(),
        rx.hstack(
            menu(),
            rx.container(page()),
        ),
        width="100%",
    )
```

`@template` 装饰器应放在 `@rx.page` 装饰器下方、返回页面的函数上方。参见[帖子页面](#a-post-page-example_big_apppagespostspy)的代码示例。

## 状态管理

大多数页面都会以某种方式使用 State。你应该避免将那些只会在单个页面中使用的 vars 添加到共享状态中。相反，应定义一个新的 `rx.State` 子类，并将其保持在页面所在的同一模块中。

### 访问其他 State

从 Reflex 0.4.3 开始，任何事件处理器都可以通过 `get_state` API 获取到任何其他子状态（substate）的实例。从实践角度来看，这意味着可以将状态拆分为更小的部分，而不需要复杂的继承层次结构来共享对其他状态的访问。

在之前的版本中，如果一个应用想要在 `SettingsState` 中存储设置，并有一个页面或组件来修改它们，那么任何需要访问这些设置的事件处理器的其他状态都必须继承 `SettingsState`，即使该状态大部分是不相关的。这个其他状态现在也必须始终加载设置，即使对于不需要访问它们的事件处理器也是如此。

更好的策略是仅从需要访问该子状态的事件处理器中按需加载所需的状态。

### 设置组件：`example_big_app/components/settings.py`

```python
import reflex as rx


class SettingsState(rx.State):
    refresh_interval: int = 15
    auto_update: bool = True
    prefer_plain_text: bool = True
    posts_per_page: int = 20


def settings_dialog():
    return rx.dialog(...)
```

### 帖子页面：`example_big_app/pages/posts.py`

该页面加载 `SettingsState` 以确定每页显示多少帖子以及刷新频率。

```python
import reflex as rx

from ..models import Post
from ..template import template
from ..components.settings import SettingsState


class PostsState(rx.State):
    refresh_tick: int
    page: int
    posts: list[Post]

    @rx.event
    async def on_load(self):
        settings = await self.get_state(SettingsState)
        if settings.auto_update:
            self.refresh_tick = settings.refresh_interval * 1000
        else:
            self.refresh_tick = 0

    @rx.event
    async def tick(self, _):
        settings = await self.get_state(SettingsState)
        with rx.session() as session:
            q = (
                Post
                .select()
                .offset(self.page * settings.posts_per_page)
                .limit(settings.posts_per_page)
            )
            self.posts = q.all()

    @rx.event
    def go_to_previous(self):
        if self.page > 0:
            self.page = self.page - 1

    @rx.event
    def go_to_next(self):
        if self.posts:
            self.page = self.page + 1


@rx.page(route="/posts", on_load=PostsState.on_load)
@template
def posts():
    return rx.vstack(
        rx.foreach(PostsState.posts, post_view),
        rx.hstack(
            rx.button("< Prev", on_click=PostsState.go_to_previous),
            rx.button("Next >", on_click=PostsState.go_to_next),
            justify="between",
        ),
        rx.moment(
            interval=PostsState.refresh_tick, on_change=PostsState.tick, display="none"
        ),
        width="100%",
    )
```

### 公共状态：`example_big_app/state.py`

被多个页面或组件共享的_公共_状态和子状态，应在单独的模块中实现，以避免循环导入。该模块不应导入应用中的其他模块。

## 组件复用性

在 Reflex 中复用组件的主要机制是定义一个返回组件的函数，然后在需要该功能的地方直接调用它。

组件函数通常不应接受任何 State 类作为参数，而是倾向于导入所需的状态并直接通过类访问 vars。

### 使用 Memoize 函数提升性能

在大型应用中，如果一个组件有很多子组件，或者在很多地方被使用，使用 `@lru_cache` 装饰器对函数进行记忆化（memoize）可以提升编译和运行时性能。

要对 `foo` 组件进行记忆化以避免多次重新创建，只需在函数定义上添加 `@lru_cache`，该组件就只会为每组唯一的参数创建一次。

```python
from functools import lru_cache

import reflex as rx


class State(rx.State):
    v: str = "foo"

    @rx.event
    def set_v(self, value: str):
        self.v = value


@lru_cache
def foo():
    return rx.text(State.v)


def index():
    return rx.flex(
        rx.button(
            "Change", on_click=State.set_v(rx.cond(State.v != "bar", "bar", "foo"))
        ),
        *[foo() for _ in range(100)],
        direction="row",
        wrap="wrap",
    )
```

### example_big_app/components

这个包包含应用中可复用的部分，例如页头、页脚和菜单。如果某个特定组件需要状态，子状态可以为了局部性而定义在同一个模块中。在组件模块中定义的任何子状态，应只包含与该组件相关的字段和事件处理器。

### 外部组件

Reflex 0.4.3 引入了对 [`reflex component` CLI 命令](/docs/custom-components/overview)的支持，这让你可以轻松地将通用功能打包为一个独立的 Python 包发布到 PyPI，以便在任何 Reflex 应用中安装和使用。

在包装 npm 组件或其他自成体系的功能块时，将这种复杂性移出应用本身，有助于更方便地维护和在其他应用中复用。

## 数据库模型：`example_big_app/models.py`

建议将所有数据库模型实现在一个文件中，以便更容易地定义关系并理解整个模式（schema）。

然而，如果模式非常大，将模型放在一个 `models` 包中，每个模型定义在自己的模块中，可能更有意义。

无论如何，单独定义模型可以让任何页面或组件轻松导入和使用它们，而不会产生循环导入。

## 顶层包：`example_big_app/__init__.py`

这是导入应属于应用的所有状态、模型和页面的好地方。通常，组件和辅助函数不需要导入到此处，因为它们会被使用它们的页面导入（否则它们将不会被用到）。

```python
from . import state, models
from .pages import index, login, post, product, profile, schedule

__all__ = [
    "state",
    "models",
    "index",
    "login",
    "post",
    "product",
    "profile",
    "schedule",
]
```

如果任何页面在此处没有被导入，它们将不会作为应用的一部分被编译。

## example_big_app/example_big_app.py

这是主应用模块。由于其他所有内容都定义在其他模块中，这个文件变得非常简单。

```python
import reflex as rx

app = rx.App()
```

## 文件管理

在 Reflex 应用中，通常有两类非代码资源（媒体、字体、样式表、文档）。

### assets

`assets` 目录用于存放相对于前端根目录（默认端口 3000）可访问的**静态**文件。当应用以生产模式部署时，对 assets 目录的更改在运行时将**不会**生效！

在引用资源时，始终使用前导斜杠，这样无论页面路由如何，资源都能被正确解析。

### uploaded_files

如果应用需要在运行时动态地提供文件，建议通过 `REFLEX_UPLOADED_FILES_DIR` 环境变量设置目标目录（默认为 `./uploaded_files`），将文件写入 `rx.get_upload_dir()` 返回的路径，并通过 `rx.get_upload_url(relative_path)` 创建可用的链接。

上传的文件通过 `/_upload/<relative_path>` 从后端（默认端口 8000）提供访问。

## 整合在一起

基于前面的讨论，推荐的项目布局如下所示。

```text
example-big-app/
├─ assets/
├─ example_big_app/
│  ├─ components/
│  │  ├─ __init__.py
│  │  ├─ auth.py
│  │  ├─ footer.py
│  │  ├─ menu.py
│  │  ├─ navbar.py
│  ├─ pages/
│  │  ├─ __init__.py
│  │  ├─ index.py
│  │  ├─ login.py
│  │  ├─ posts.py
│  │  ├─ product.py
│  │  ├─ profile.py
│  │  ├─ schedule.py
│  ├─ __init__.py
│  ├─ example_big_app.py
│  ├─ models.py
│  ├─ state.py
│  ├─ template.py
├─ uploaded_files/
├─ pyproject.toml
├─ rxconfig.py
```

## 关键要点

- 与任何其他 Python 项目一样，**将应用拆分为模块和包**，以保持代码库的组织性和可管理性。
- 使用更小的模块和包可以更轻松地**在应用中复用组件和状态**，而不会引入循环依赖。
- 创建**独立的函数**来封装功能单元，并在需要的地方**复用它们**。
