
# 命令参考

自定义组件命令位于 `reflex component` 子命令下。要查看可用命令列表，运行 `reflex component --help`。要查看特定命令的手册，运行 `reflex component <command> --help`，例如 `reflex component init --help`。

```bash
reflex component --help
```

```text
Usage: reflex component [OPTIONS] COMMAND [ARGS]...

  Subcommands for creating and publishing Custom Components.

Options:
  --help  Show this message and exit.

Commands:
  init     Initialize a custom component.
  build    Build a custom component.
  share    Collect more details on the published package for gallery.
```

## reflex component init

以下是运行 `init` 命令的示例。

```bash
reflex component init
```

```text
reflex component init
─────────────────────────────────────── Initializing reflex-google-auth project ───────────────────────────────────────
Info: Populating pyproject.toml with package name: reflex-google-auth
Info: Initializing the component directory: custom_components/reflex_google_auth
Info: Creating app for testing: google_auth_demo
──────────────────────────────────────────── Initializing google_auth_demo ────────────────────────────────────────────
[07:58:16] Initializing the app directory.                                                                console.py:85
           Initializing the web directory.                                                                console.py:85
Success: Initialized google_auth_demo
─────────────────────────────────── Installing reflex-google-auth in editable mode. ───────────────────────────────────
Info: Package reflex-google-auth installed!
Custom component initialized successfully!
─────────────────────────────────────────────────── Project Summary ───────────────────────────────────────────────────
[ README.md ]: Package description. Please add usage examples.
[ pyproject.toml ]: Project configuration file. Please fill in details such as your name, email, homepage URL.
[ custom_components/ ]: Custom component code template. Start by editing it with your component implementation.
[ google_auth_demo/ ]: Demo App. Add more code to this app and test.
```

`init` 命令使用当前包含文件夹的名称来构造 Python 包名，通常采用 kebab-case 格式。例如，如果在文件夹 `google_auth` 中运行 init，包名将为 `reflex-google-auth`。添加的前缀减少了 PyPI（Python 包索引）上名称冲突的机会，并表明该包是 Reflex 自定义组件。用户可以通过提供 `--package-name` 选项来覆盖包名。

`init` 命令创建一组预填充了包名和其他详细信息的文件和文件夹。在初始化期间，`custom_component` 文件夹以可编辑模式本地安装，因此开发者可以轻松地增量开发和测试。组件实现中的更改会自动反映在使用它的地方。以下是 `init` 命令后的文件夹结构。

```text
google_auth/
├── pyproject.toml
├── README.md
├── custom_components/
│   └── reflex_google_auth/
│       ├── google_auth.py
│       └── __init__.py
└── google_auth_demo/
    └── assets/
        google_auth_demo/
        requirements.txt
        rxconfig.py
```

### pyproject.toml

`pyproject.toml` 是包构建和发布所必需的。它预填充了包名、版本（`0.0.1`）、作者姓名和电子邮件、主页 URL 等信息。默认使用 **Apache-2.0** 许可证，与 Reflex 相同。如果任何这些信息需要更新，用户可以手动编辑文件。

### README

`README.md` 文件创建时包含安装说明，例如 `pip install reflex-google-auth`，以及包的简要描述。通常 `README.md` 包含使用示例。在 PyPI 上，`README.md` 作为包页面的一部分呈现。

### 自定义组件文件夹

`custom_components` 文件夹是实际实现所在的位置。不要担心这个文件夹名称：无需更改它。它是 `pyproject.toml` 指定 Python 包源的位置。发布的包包含其中的内容，不包括此文件夹。

`reflex_google_auth` 是可导入代码的顶级文件夹。`reflex_google_auth/__init__.py` 从 `reflex_google_auth/google_auth.py` 导入所有内容。对于包的用户，导入看起来像 `from reflex_google_auth import ABC, XYZ`。

`reflex_google_auth/google_auth.py` 预填充了来自[封装 React 指南](/docs/wrapping-react/overview)的代码示例和说明。

### 演示应用文件夹

在 `google_auth_demo` 文件夹内生成一个演示应用，包含导入语句和组件的示例用法。这是一个常规的 Reflex 应用。进入此目录并开始使用任何 reflex 命令进行测试。

### 帮助手册

向命令添加 `--help` 选项时显示帮助手册。

```bash
reflex component init --help
```

```text
Usage: reflex component init [OPTIONS]

  Initialize a custom component.

  Args:     library_name: The name of the library.     install: Whether to
  install package from this local custom component in editable mode.
  loglevel: The log level to use.

  Raises:     Exit: If the pyproject.toml already exists.

Options:
  --library-name TEXT             The name of your library. On PyPI, package
                                  will be published as `reflex-{library-
                                  name}`.
  --install / --no-install        Whether to install package from this local
                                  custom component in editable mode.
                                  [default: install]
  --loglevel [debug|info|warning|error|critical]
                                  The log level to use.  [default:
                                  LogLevel.INFO]
  --help                          Show this message and exit.
```

## reflex component publish

要发布到包索引，用户需要已经拥有其账户。截至 **0.7.5**，Reflex 不为你处理发布过程。你可以先运行 `reflex component build`，然后运行 `twine upload` 或 `uv publish` 或你选择的发布工具来手动完成。

然后你可以使用 `reflex component share` 在我们的网站上共享你的构建。

## reflex component build

在发布之前不需要单独运行 `build` 命令。如果包尚未构建，`publish` 命令将构建包。`build` 命令是为了用户的方便而提供的。

`build` 命令生成 `.tar.gz` 和 `.whl` 分发文件，以上传到所需的包索引，例如 PyPI。此命令必须在 `pyproject.toml` 文件所在的项目顶层运行。成功构建的结果是一个包含分发文件的新 `dist` 文件夹。

```bash
reflex component build --help
```

```text
Usage: reflex component build [OPTIONS]

  Build a custom component. Must be run from the project root directory where
  the pyproject.toml is.

  Args:     loglevel: The log level to use.

  Raises:     Exit: If the build fails.

Options:
  --loglevel [debug|info|warning|error|critical]
                                  The log level to use.  [default:
                                  LogLevel.INFO]
  --help                          Show this message and exit.
```
