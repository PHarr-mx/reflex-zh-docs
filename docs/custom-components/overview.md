# 自定义组件概述

```python exec
import reflex as rx
```

Reflex 用户创建了许多自己的组件：即用的高级组件，或封装良好的 React 组件。通过**自定义组件（Custom Components）**，社区现在可以轻松共享这些组件。

**0.4.3** 版本引入了一系列 `reflex component` 命令，帮助开发者封装 React 组件、测试并将其发布为 Python 包。如下图所示，PyPI 上已经有一些自定义组件发布，例如 `reflex-spline`、`reflex-webcam`。

在[此处](/docs/custom-components/overview)查看自定义组件库。

```python eval
rx.center(
    rx.image(
        src="https://web.reflex-assets.dev/custom_components/pypi_reflex_custom_components.webp",
        alt="PyPI page listing Reflex custom components",
        width="400px",
        border_radius="15px",
        border="1px solid",
    ),
)
```

## 发布前提条件

要发布 Python 包，需要在 Python 包索引（例如 PyPI）上拥有一个账户。创建账户和生成 API 令牌的文档可以在其网站上找到。有关快速参考，请查看我们的[发布前提条件](/docs/custom-components/prerequisites-for-publishing)页面。

## 发布步骤

按照以下步骤将自定义组件发布为 Python 包：

1. `reflex component init`：从模板创建新的自定义组件项目。
2. 开发和测试：开发者实现并测试自定义组件。
3. `reflex component build`：构建包。
4. `twine upload` 或 `uv publish`：将包上传到 Python 包索引。

### 初始化

```bash
reflex component init
```

首先为你的自定义组件项目创建一个新文件夹，例如 `color_picker`。包名将为 `reflex-color-picker`。前缀 `reflex-` 是有意为所有自定义组件添加的，以便在 PyPI 上轻松搜索。如果你偏好特定的包名，可以手动在 `pyproject.toml` 文件中更改，或在 `reflex component init` 命令中最初添加 `--library-name` 选项。

运行 `reflex component init`，将在 `color_picker` 文件夹中创建一组文件和文件夹。`pyproject.toml` 文件是项目的配置文件。`custom_components` 文件夹是自定义组件实现所在的位置。`color_picker_demo` 文件夹是使用自定义组件的演示 Reflex 应用。如果这是第一次创建 Python 包，建议浏览所有文件（数量不多）以了解项目的结构。

```bash
color_picker/
├── pyproject.toml            <- 配置文件
├── README.md
├── .gitignore                <- 排除 dist/ 和元数据文件夹
├── custom_components/
│   └── reflex_color_picker/  <- 自定义组件源目录
│       ├── color_picker.py
│       └── __init__.py
└── color_picker_demo/        <- 演示 Reflex 应用目录
    └── assets/
        color_picker_demo/
        requirements.txt
        rxconfig.py
```

### 开发和测试

完成自定义组件实现后，鼓励用户在发布前充分测试它。生成的 Reflex 演示应用 `color_picker_demo` 是一个很好的起点。它是一个常规的 Reflex 应用，预先填充了此组件的导入和使用。在初始化期间，`custom_component` 文件夹以可编辑模式本地安装，因此开发者可以轻松地增量开发和测试。组件实现中的更改会自动反映在演示应用中。

### 发布

```bash
reflex component build
```

当你准备好发布包时，运行 `reflex component build` 来构建包。如果分发包尚未构建，该命令将构建它们。最终结果是一个包含分发文件的 `dist` 文件夹。用户无需手动处理这些分发文件。

要将这些文件发布为 Python 包，你需要使用发布工具。任何工具都可以，但我们推荐 [Twine](https://twine.readthedocs.io/en/stable/) 或 [uv](https://docs.astral.sh/uv/guides/package/#publishing-your-package)。确保保持 pyproject.toml 中的包版本为最新。

你还可以使用命令 `reflex component share` 在我们的网站上与社区其他成员共享你的组件。在那里见！
