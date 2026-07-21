# Reflex Build：最佳实践

> 一份与 AI Builder 高效协作的综合指南。本指南概述了如何在 Reflex Build 中使用 AI Builder 时获得最可靠、最高效的结果。成功的关键在于清晰、结构化和迭代。

---

## 核心工作流

### 基础：构建前的规划

在开始使用 AI Builder 之前，花时间规划你的方案。充分的准备会带来更好的结果和更少的迭代。

- **明确核心目的和用户** — 写一个 2-3 句的应用描述，确定目标用户及其需求。
- **确定 3-5 个关键功能的优先级** — 先专注于最重要的功能，然后再扩展。
- **收集视觉参考** — 收集你想要模仿的布局的截图、线框图或草图。
- **结构化你的数据** — 列出每个页面需要显示的信息以及用户将如何与之交互。
- **从最重要的页面开始** — 通常是你的主仪表板、主页或主要工作流。先把这个做好。

### 从应用规格自动生成提示词

为了节省时间并获得更高质量的提示词，你可以将完整的应用规格输入 AI Builder，让它将规格分解为结构化的、可直接构建的提示词。

AI Builder 可以将你的愿景转化为：

- 布局指令
- UI 组件定义
- 数据模型需求
- 样式偏好
- 后续测试计划

**示例工作流：**

1. 将完整的应用规格粘贴到 AI Builder 中
2. 要求 **"将此分解为一系列可构建的提示词。"**
3. 按顺序执行每个生成的提示词
4. 使用结构化的提示词迭代构建

### 处理文本规格

如果你有结构化的应用规格，不要一次性将整个文档粘贴到构建器中。将其分解为逻辑部分，按顺序输入。

**专业提示：** 让 AI Builder 帮助准备提示词：

粘贴你的完整应用规格并询问：

  - **"将此分解为可构建的提示词。"**
  - **"为构建此应用的每个功能/页面写一个提示词。"**

这种方法——先规划，然后迭代构建——让你更快地前进，更聪明地构建。

### 编写清晰、面向任务的提示词

AI 在收到**具体的、结果驱动的指令**时表现最佳。避免模糊、宽泛的提示词。


```diff
- Build me an admin dashboard. # <- 不好
+ Create a 2-column layout with a sidebar for navigation and a top navbar. # <- 好
```

尽可能将大任务拆分为更小的步骤：

- 先定义布局（列、行、侧边栏）
- 添加 UI 组件（按钮、输入框、模态框）
- 稍后处理数据模型和状态
- 使用后续提示进行样式调整和润色

使用精确的样式语言，例如：

```diff
- Grid items have small spacing and sharp corners.
+ Add medium spacing between grid items and use large rounded corners on cards.
```

避免使用主观词汇，如"**nice**"、"**modern**"或"**clean**"。将你的提示词视为构建器的接口文档。

### 使用图片和视觉参考

你可以拖入网站、仪表板、应用的截图，甚至手绘线框图。构建器将从这些图片中提取布局、设计和功能思路。

**图片提示：**

- 清晰的截图效果最好
- 包含你想要的任何元素：表单、表格、导航、图表
- 你可以用箭头、注释或标签进行标注

**获取 UI/UX 反馈：**

上传截图并询问：**"我可以做哪 5 件事来改善这个的 UI/UX？"**

然后跟进：**实现第 1、2 和 4 项。**

或请求具体改进：**让这个更简约、更移动优先。**

---

## 优化你的工作流

### 迭代构建

试图在单个提示词中生成完整应用几乎从来不会顺利。相反，分阶段清晰地构建：

1. **布局** — Grid、Flex、响应式列/行
2. **组件** — 表格、按钮、模态框、图表等
3. **状态** — 绑定、存储、模拟数据
4. **优化** — 调整、视觉润色、边缘情况处理

在每个阶段，给出反馈并迭代。如果 AI 构建器做出了接近的结果，你可以说：

```diff
- Modal is included and sidebar is static.
+ Remove modal and make sidebar collapsible.

- Buttons vary across pages.
+ Use same button style from home page.

- Card layout differs across sections.
+ Repeat card layout from dashboard section.
```


### 改善 UI/UX

要改善你的设计，可以要求构建器提供更精致的布局、更好的结构或更现代的风格。

```diff
- Layout feels cluttered with small headings and dense sections.
+ Increase heading sizes and spacing between sections for better hierarchy.

- Navigation buttons are inconsistent.
+ Standardize button sizes and colors for consistent UX.
```

**建议的工作流：**

1. 上传一张图片或描述布局。
2. 询问：**"告诉我 5 件可以改善此页面 UI/UX 的事情。"**
3. 审查建议并决定要应用哪些。
4. 实现：**"通过增加标题大小和增加各部分之间的间距来改善视觉层次。"**

### 使用知识（Knowledge）引导构建

**知识（Knowledge）**面板让你提供长篇参考，影响代理如何构建你的应用。添加设计系统、样式指南、品牌指南或架构规则。

添加后，构建器将在整个会话中尝试遵循这些规则，确保一致性而无需重复指令。

尝试将知识与提示词结合使用：

  - **使用知识中的样式指南来改善此页面。**

  - **当前布局是否符合我们知识中的设计系统？**


### 本地开发集成

我们为企业客户提供了一个 MCP 服务器，用于连接本地 AI 开发工具，如 Claude Desktop、Windsurf 或 Codex。

这实现了一种混合工作流：在应用构建器中生成应用并进行重大更改，然后转到本地开发进行详细优化和自定义功能。

> **企业功能：** Reflex MCP 集成目前仅对企业客户可用。[预约演示](https://reflex.dev/pricing/)以讨论访问权限。

#### 快速设置

**前提条件：**

- 兼容 MCP 的 AI 工具（Claude Desktop、Windsurf、Codex）
- 用于 OAuth 认证的有效 Reflex 账户
- 连接到托管 MCP 服务器的网络连接

**优势：**

- **无缝交接** — 在 Web 构建器和本地开发之间切换
- **AI 驱动的本地开发** — 使用你偏好的 AI 工具处理 Reflex 项目
- **无需本地安装** — 托管 MCP 服务器无需额外的 Python 包
- **安全认证** — 与你的 Reflex 账户集成 OAuth 2.1

有关 Claude Desktop、Windsurf、Codex 和其他 MCP 客户端的完整设置说明，请访问我们的 [MCP 集成](https://reflex.dev/docs/ai/integrations/mcp-installation/)文档。

---

## 关键要点

- **构建前先规划**。几分钟的准备可以节省数小时的迭代。
- **模块化思考**。在构建完整系统之前先关注原子部分。
- **像设计师-开发者一样编写**。清晰、结构化和功能性的语言最有效。
- **持续迭代**。让每个提示词帮你完成 80%，然后进行优化。

通过这些技巧，AI Builder 将成为你创意和技术意图的可靠延伸。
