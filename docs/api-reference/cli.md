# 命令行界面（CLI）

`reflex` 命令行界面（CLI）是用于创建和管理 Reflex 应用的工具。

要查看所有可用命令的列表，运行 `reflex --help`。

```bash
$ reflex --help

Usage: reflex [OPTIONS] COMMAND [ARGS]...

  Reflex CLI to create, run, and deploy apps.

Options:
  --version  Show the version and exit.
  --help     Show this message and exit.

Commands:
  cloud      The Hosting CLI.
  component  CLI for creating custom components.
  db         Subcommands for managing the database schema.
  deploy     Deploy the app to the Reflex hosting service.
  export     Export the app to a zip file.
  init       Initialize a new Reflex app in the current directory.
  login      Authenticate with experimental Reflex hosting service.
  logout     Log out of access to Reflex hosting service.
  rename     Rename the app in the current directory.
  run        Run the app in the current directory.
  script     Subcommands for running helper scripts.
```

## 初始化（Init）

`reflex init` 命令在当前目录中创建一个新的 Reflex 应用。
如果 `rxconfig.py` 文件已经存在，它将使用最新模板重新初始化应用。

```bash
$ reflex init --help
Usage: reflex init [OPTIONS]

  Initialize a new Reflex app in the current directory.

Options:
  --name APP_NAME                 The name of the app to initialize.
  --template [demo|sidebar|blank]
                                  The template to initialize the app with.
  --loglevel [debug|info|warning|error|critical]
                                  The log level to use.  [default:
                                  LogLevel.INFO]
  --help                          Show this message and exit.
```

## 运行（Run）

`reflex run` 命令在当前目录中运行应用。

默认情况下，它以开发模式运行你的应用。
这意味着当你更改代码时，应用将自动重新加载。
你也可以以生产模式运行，这将创建应用的优化构建。

你可以通过标志配置模式以及其他选项。

```bash
$ reflex run --help
Usage: reflex run [OPTIONS]

  Run the app in the current directory.

Options:
  --env [dev|prod]                The environment to run the app in.
                                  [default: Env.DEV]
  --frontend-only                 Execute only frontend.
  --backend-only                  Execute only backend.
  --frontend-port TEXT            Specify a different frontend port.
                                  [default: 3000]
  --backend-port TEXT             Specify a different backend port.  [default:
                                  8000]
  --backend-host TEXT             Specify the backend host.  [default:
                                  0.0.0.0]
  --loglevel [debug|info|warning|error|critical]
                                  The log level to use.  [default:
                                  LogLevel.INFO]
  --help                          Show this message and exit.
```

## 导出（Export）

你可以使用 `reflex export` 命令将应用的前端和后端导出为 zip 文件。

前端是一个编译好的 NextJS 应用，可以部署到像 Github Pages 或 Vercel 这样的静态托管服务。
然而这只是一个静态构建，因此你需要单独部署后端。
有关更多信息，请参阅自托管指南。

## 重命名（Rename）

`reflex rename` 命令允许你重命名你的 Reflex 应用。这将更新配置文件中的应用名称。

```bash
$ reflex rename --help
Usage: reflex rename [OPTIONS] NEW_NAME

  Rename the app in the current directory.

Options:
  --loglevel [debug|default|info|warning|error|critical]
                                  The log level to use.
  --help                          Show this message and exit.
```

## 云（Cloud）

`reflex cloud` 命令提供对 Reflex Cloud 托管服务的访问。它包括用于管理应用、项目、密钥等的子命令。

有关 Reflex Cloud 和部署的详细文档，请参阅 [Cloud 快速入门指南](https://reflex.dev/docs/hosting/deploy-quick-start/)。

## 脚本（Script）

`reflex script` 命令提供对 Reflex 开发辅助脚本的访问。

```bash
$ reflex script --help
Usage: reflex script [OPTIONS] COMMAND [ARGS]...

  Subcommands for running helper scripts.

Options:
  --help  Show this message and exit.
```
