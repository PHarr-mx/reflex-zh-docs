# 项目结构


## 目录结构

让我们创建一个名为 `hello` 的新应用

```bash
mkdir hello
cd hello
uv init
uv add reflex
uv run reflex init
```

这将创建如下目录结构：

```bash
hello
├── .venv
├── .web
├── assets
├── hello
│   ├── __init__.py
│   └── hello.py
├── .gitignore
├── .python-version
├── pyproject.toml
├── rxconfig.py
└── uv.lock
```

`uv init` 还可能创建一些辅助文件，例如 `README.md`、`main.py` 以及 Git 元数据。上方的目录树聚焦于你在构建 Reflex 应用时会接触到的主要文件。

让我们逐一了解这些目录和文件。

## .venv

`uv add reflex` 默认会在 `.venv` 中创建一个本地虚拟环境。这可以让你的应用依赖与系统中其余的 Python 环境相互隔离。

## .web

这里是编译后的 Javascript 文件的存放位置。你永远不需要手动操作这个目录，但它对调试很有用。

每个 Reflex 页面都会编译为 `.web/pages` 目录中对应的 `.js` 文件。

如果 Reflex 使用 Bun 安装前端依赖，规范的 `bun.lock` 会存放在项目根目录下的 `reflex.lock/` 目录中，并应提交到版本控制。这个专用目录可以避免该文件与可能和 Reflex 项目并存的用户自管 bun 项目中的 `bun.lock` 发生冲突。当 Reflex 需要运行包管理器时，它会将 `reflex.lock/bun.lock` 镜像到 `.web` 中。

## Assets

`assets` 目录用于存放任何你希望公开访问的静态资源，包括图片、字体和其他文件。

例如，如果你将一张图片保存到 `assets/image.png`，你可以这样在应用中显示它：

```python
rx.image(src="https://web.reflex-assets.dev/other/image.png", alt="Example image asset")
```

## 主项目

初始化项目会创建一个与应用同名的目录。你将在这里编写应用的逻辑。

Reflex 会在 `hello/hello.py` 文件中生成一个默认应用。你可以修改这个文件来自定义你的应用。

## Python 项目文件

`pyproject.toml` 定义你的 Python 项目元数据和依赖项。`uv add reflex` 会在你初始化应用之前将 Reflex 依赖记录在其中。

`uv.lock` 存储完全解析后的依赖集合，以实现可复现的安装。请将它提交到版本控制，以便所有参与该应用开发的人都能获得相同的 Python 包版本。

## 配置

`rxconfig.py` 文件可用于配置你的应用。默认情况下，它的内容大致如下：

```python
import reflex as rx


config = rx.Config(
    app_name="hello",
)
```

我们将在[高级项目结构](/docs/advanced-onboarding/code-structure)文档中更详细地讨论项目结构和配置。
