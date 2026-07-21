# reflex-zh-docs

Reflex 中文文档。

[Reflex](https://reflex.dev/) 是一个用于构建和部署全栈 Web 应用的纯 Python 框架——前端、后端和数据库一站式搞定，无需 JavaScript、无需单独的 API。本仓库收录了 Reflex 官方文档的简体中文翻译，方便中文开发者快速上手。

## 文档列表

### getting_started

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [安装](docs/getting_started/installation.md) | 介绍如何安装 Reflex，包括虚拟环境配置、macOS/Linux 与 Windows 的安装步骤，以及运行第一个应用。 |  |
| [简介](docs/getting_started/introduction.md) | 介绍 Reflex 的设计目标，并通过构建一个计数器应用，讲解 state、event handlers 和 components 三大核心概念。 |  |
| [项目结构](docs/getting_started/project-structure.md) | 介绍 Reflex 项目的目录结构，以及各文件和目录（如 `.venv`、`.web`、`assets`、`rxconfig.py`）的作用。 |  |
| [基础](docs/getting_started/basics.md) | 介绍最常用的核心概念：组件与 props、state、事件、编译时与运行时、条件渲染、列表渲染以及 var 操作。 |  |
| [AI 聊天应用教程](docs/getting_started/chatapp_tutorial.md) | 手把手用纯 Python 构建一个流式 AI 聊天机器人，涵盖 UI、状态管理以及 OpenAI 集成与部署。 |  |
| [数据仪表盘教程](docs/getting_started/dashboard_tutorial.md) | 构建一个交互式数据仪表盘，包含用户表格、用于添加数据的表单（对话框）以及随数据更新的柱状图。 |  |

### advanced_onboarding

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [项目结构（进阶）](docs/advanced_onboarding/code_structure.md) | 深入讲解 Reflex 应用的模块组织方式，包括主应用模块、状态/页面/模型的导入规则及多模块项目结构。 |  |
| [配置](docs/advanced_onboarding/configuration.md) | 介绍如何通过配置文件（rxconfig.py）、环境变量和命令行参数来配置 Reflex 应用。 |  |
| [Reflex 工作原理](docs/advanced_onboarding/how-reflex-works.md) | 以一个 Github 个人资料应用为例，解释 Reflex 的前后端架构、编译流程和运行时通信机制。 |  |

### vars

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [基本变量（Base Vars）](docs/vars/base_vars.md) | 介绍 State 中基本变量的定义、类型注解、默认值及可变性规则。 |  |
| [计算变量（Computed Vars）](docs/vars/computed_vars.md) | 介绍如何通过 @rx.var 装饰器定义派生状态，包括缓存变量和后台计算变量。 |  |
| [自定义变量（Custom Vars）](docs/vars/custom_vars.md) | 介绍如何创建自定义 Var 类型以支持复杂数据结构和序列化逻辑。 |  |
| [混合属性（Hybrid Properties）](docs/vars/hybrid_properties.md) | 介绍实验性的 hybrid_property，允许同一属性在前端和后端使用不同实现。 |  |
| [Var 操作](docs/vars/var-operations.md) | 介绍在 JSX 中对 Var 进行条件表达式、算术运算、字符串操作、列表操作等前端计算。 |  |

### events

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [事件概述](docs/events/events_overview.md) | 介绍事件系统的基本概念：事件处理器、事件触发、事件队列及前后端通信流程。 |  |
| [事件参数](docs/events/event_arguments.md) | 介绍如何向事件处理器传递参数，包括组件自带参数和自定义参数。 |  |
| [事件链](docs/events/chaining_events.md) | 介绍如何按顺序触发多个事件处理器，实现事件的串联调用。 |  |
| [事件动作](docs/events/event_actions.md) | 介绍事件动作修饰符，如 preventDefault、stopPropagation、throttle、debounce 等。 |  |
| [后台任务](docs/events/background_events.md) | 介绍如何使用 @rx.background 装饰器在后台线程中运行耗时任务。 |  |
| [去中心化事件处理器](docs/events/decentralized_event_handlers.md) | 介绍如何在组件级别定义事件处理逻辑，实现事件处理的去中心化组织。 |  |
| [页面加载事件](docs/events/page_load_events.md) | 介绍如何在页面加载时自动触发事件处理器。 |  |
| [特殊事件](docs/events/special_events.md) | 介绍内置的特殊事件，如 set_clipboard、download、redirect、scroll_to 等。 |  |
| [通过 Yield 更新](docs/events/yield_events.md) | 介绍如何在事件处理器中使用 yield 实现流式/渐进式状态更新。 |  |

### pages

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [页面](docs/pages/overview.md) | 介绍如何定义页面、路由、页面元数据（标题、描述、图片）及多页面应用结构。 |  |
| [动态路由](docs/pages/dynamic_routing.md) | 介绍如何创建带参数的动态路由，以及如何在页面中访问路由参数。 |  |

### styling

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [样式（Styling）](docs/styling/overview.md) | 介绍 Reflex 的样式系统，包括内联样式、全局样式、样式字典及条件样式。 |  |
| [样式和布局属性](docs/styling/common-props.md) | 介绍常用的样式和布局属性，如颜色、间距、字体、Flex/Grid 布局等。 |  |
| [自定义样式表（Custom Stylesheets）](docs/styling/custom-stylesheets.md) | 介绍如何引入自定义 CSS 样式表并在组件中使用自定义类名。 |  |
| [布局组件（Layout Components）](docs/styling/layout.md) | 介绍 Flex、Grid、Stack、Container 等布局组件的使用方法。 |  |
| [响应式设计（Responsive）](docs/styling/responsive.md) | 介绍如何实现响应式布局，使用断点和媒体查询适配不同屏幕尺寸。 |  |
| [Tailwind](docs/styling/tailwind.md) | 介绍如何在 Reflex 中使用 Tailwind CSS 工具类进行样式设计。 |  |
| [主题化（Theming）](docs/styling/theming.md) | 介绍如何自定义应用主题，包括颜色方案、外观模式和全局主题配置。 |  |

### ui

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [UI 概述](docs/ui/overview.md) | 介绍 Reflex 的组件系统，包括组件分类、使用方式及自定义组件的基础知识。 |  |

### assets

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [资源文件](docs/assets/overview.md) | 介绍如何在应用中使用静态资源文件（图片、字体、视频等）。 |  |
| [文件](docs/assets/upload_and_download_files.md) | 介绍文件上传和下载功能的实现方式。 |  |

### authentication

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [身份验证概述](docs/authentication/authentication_overview.md) | 介绍 Reflex 中实现用户身份验证的几种方式和最佳实践。 |  |

### api-routes

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [API 转换器](docs/api-routes/overview.md) | 介绍如何定义 API 路由，将后端函数暴露为 REST API 端点。 |  |

### utility_methods

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [异常处理程序](docs/utility_methods/exception_handlers.md) | 介绍如何定义全局异常处理函数来捕获和处理应用运行时异常。 |  |
| [生命周期任务（Lifespan Tasks）](docs/utility_methods/lifespan_tasks.md) | 介绍如何在后端服务器启动/关闭时执行协程任务。 |  |
| [其他方法](docs/utility_methods/other_methods.md) | 介绍 State 的其他实用方法，如 reset、get_value、dict 等。 |  |
| [状态实用方法](docs/utility_methods/router_attributes.md) | 介绍 State 中可访问的路由属性，如当前页面路径、查询参数等。 |  |

### wrapping-react

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [封装 React](docs/wrapping-react/overview.md) | 介绍如何在 Reflex 中封装和使用 React 组件库。 |  |
| [逐步封装 React](docs/wrapping-react/step-by-step.md) | 通过逐步示例讲解封装 React 组件的完整流程。 |  |
| [样式和导入](docs/wrapping-react/imports-and-styles.md) | 介绍封装 React 组件时如何处理样式表和模块导入。 |  |
| [Library and Tags](docs/wrapping-react/library-and-tags.md) | 介绍如何指定 React 组件的 npm 库来源和组件标签。 |  |
| [Props](docs/wrapping-react/props.md) | 介绍如何定义和传递 React 组件的 Props。 |  |
| [序列化器](docs/wrapping-react/serializers.md) | 介绍如何为自定义类型定义序列化器以在前后端间传递数据。 |  |
| [自定义代码和 Hooks](docs/wrapping-react/custom-code-and-hooks.md) | 介绍如何在封装组件中嵌入自定义 JavaScript 代码和 React Hooks。 |  |
| [Wrapping Local Packages](docs/wrapping-react/local-packages.md) | 介绍如何封装本地 React 包而非 npm 远程包。 |  |
| [复杂示例](docs/wrapping-react/example.md) | 通过一个完整的复杂示例演示封装 React 组件的综合用法。 |  |
| [更多 React 库](docs/wrapping-react/more-wrapping-examples.md) | 提供更多封装常用 React 库的示例和参考。 |  |

### ai_builder

| 文档 | 摘要 | 博客园链接 |
| --- | --- | --- |
| [什么是 Reflex Build](docs/ai_builder/overview/what_is_reflex_build.md) | 介绍 Reflex Build 的定位、核心功能和适用场景。 |  |
| [你的第一个 Reflex Build 应用](docs/ai_builder/overview/tutorial.md) | 通过教程引导用户创建第一个 Reflex Build 应用。 |  |
| [模板（Templates）](docs/ai_builder/overview/templates.md) | 介绍如何使用模板快速启动 Reflex Build 项目。 |  |
| [最佳实践](docs/ai_builder/overview/best_practices.md) | 介绍使用 Reflex Build 的最佳实践和技巧。 |  |
| [编辑器模式（Editor Modes）](docs/ai_builder/features/editor_modes.md) | 介绍 Reflex Build 编辑器的不同工作模式。 |  |
| [文件树（File Tree）](docs/ai_builder/features/file_tree.md) | 介绍 Reflex Build 中的文件树导航和管理功能。 |  |
| [Reflex Build IDE](docs/ai_builder/features/ide.md) | 介绍 Reflex Build 内置 IDE 的功能和使用方法。 |  |
| [应用样式自定义](docs/ai_builder/features/customization.md) | 介绍如何在 Reflex Build 中自定义应用样式。 |  |
| [使用图片作为提示词](docs/ai_builder/features/image_as_prompt.md) | 介绍如何上传图片作为 AI 生成应用的提示。 |  |
| [安装外部包（Installing External Packages）](docs/ai_builder/features/installing_external_packages.md) | 介绍如何在 Reflex Build 中安装和使用外部 Python 包。 |  |
| [集成快捷方式（Integrations Shortcut）](docs/ai_builder/features/integration_shortcut.md) | 介绍 Reflex Build 中快速添加集成的快捷方式。 |  |
| [知识（Knowledge）](docs/ai_builder/features/knowledge.md) | 介绍如何为 AI 提供项目知识上下文以提升生成质量。 |  |
| [恢复检查点（Restore Checkpoint）](docs/ai_builder/features/restore_checkpoint.md) | 介绍如何将应用恢复到之前的检查点状态。 |  |
| [密钥（Secrets）](docs/ai_builder/features/secrets.md) | 介绍如何在 Reflex Build 中管理 API 密钥等敏感信息。 |  |
| [AI 测试功能](docs/ai_builder/features/automated_testing.md) | 介绍 Reflex Build 的 AI 自动化测试功能。 |  |
| [连接到 GitHub](docs/ai_builder/features/connect_to_github.md) | 介绍如何将 Reflex Build 连接到 GitHub 以版本化应用和同步代码。 |  |
| [连接到 Git 提供商](docs/ai_builder/features/connect_to_git_providers.md) | 介绍如何连接到 GitLab、Bitbucket 或其他 Git 远程仓库。 |  |
| [常规应用设置（General App Settings）](docs/ai_builder/app_lifecycle/general.md) | 介绍 Reflex Build 应用的常规设置选项。 |  |
| [部署应用（Deploy App）](docs/ai_builder/app_lifecycle/deploy_app.md) | 介绍如何部署 Reflex Build 应用。 |  |
| [共享应用（Share App）](docs/ai_builder/app_lifecycle/share_app.md) | 介绍如何共享 Reflex Build 应用给他人。 |  |
| [复制应用（Copy App）](docs/ai_builder/app_lifecycle/copy_app.md) | 介绍如何复制一个现有的 Reflex Build 应用。 |  |
| [分叉应用（Fork App）](docs/ai_builder/app_lifecycle/fork_app.md) | 介绍如何分叉他人的 Reflex Build 应用。 |  |
| [下载应用（Download App）](docs/ai_builder/app_lifecycle/download_app.md) | 介绍如何将 Reflex Build 应用下载到本地。 |  |
| [集成概述](docs/ai_builder/integrations/overview.md) | 介绍 Reflex Build 支持的各类集成。 |  |
| [AGENTS.md 和 CLAUDE.md](docs/ai_builder/integrations/agents_md.md) | 介绍如何通过 AGENTS.md/CLAUDE.md 文件为 AI 提供项目指令。 |  |
| [AI 入门（AI Onboarding）](docs/ai_builder/integrations/ai_onboarding.md) | 介绍 AI 辅助入门功能的使用方法。 |  |
| [MCP 概述（Overview）](docs/ai_builder/integrations/mcp_overview.md) | 介绍 Reflex Build 中 MCP（Model Context Protocol）集成的概述。 |  |
| [MCP 安装（Installation）](docs/ai_builder/integrations/mcp_installation.md) | 介绍如何在 Reflex Build 中安装和配置 MCP 服务器。 |  |
| [技能（Skills）](docs/ai_builder/integrations/skills.md) | 介绍 Reflex Build 中的技能系统。 |  |
| [API 集成（API Integration）](docs/ai_builder/apis.md) | 介绍如何在 Reflex Build 中集成外部 API。 |  |
| [Python 库（Python Libraries）](docs/ai_builder/python_libraries.md) | 介绍 Reflex Build 中可用的 Python 库及使用方式。 |  |
| [Figma 集成（Figma Integration）](docs/ai_builder/figma.md) | 介绍如何将 Figma 设计稿导入 Reflex Build。 |  |
| [文件（Files）](docs/ai_builder/files.md) | 介绍 Reflex Build 中的文件管理功能。 |  |
| [图片（Images）](docs/ai_builder/images.md) | 介绍 Reflex Build 中的图片处理功能。 |  |
| [URL](docs/ai_builder/urls.md) | 介绍 Reflex Build 应用的 URL 和自定义域名配置。 |  |
| [Webhooks](docs/ai_builder/webhooks.md) | 介绍如何在 Reflex Build 中配置和使用 Webhooks。 |  |
