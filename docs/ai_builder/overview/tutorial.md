# 你的第一个 Reflex Build 应用

在本教程中，你将构建一个数据仪表板应用，以表格和图表两种格式显示员工信息，并具有过滤和添加新数据的交互功能。我们还将添加一个简单的聊天机器人页面来演示多页面导航。

## 你将构建什么

在本教程结束时，你将创建：
- 一个以表格和柱状图显示员工数据的仪表板
- 用于搜索数据的交互式过滤功能
- 用于添加新员工的模态表单
- 一个带有简单聊天机器人界面的独立页面

本教程假设你从 AI Builder 中的新项目开始。

---

## 创建你的仪表板

让我们从构建应用的核心开始——一个显示员工数据的仪表板。

**提示词：**
```
Create a dashboard page with a table showing sample employee data with columns: Name, Department, and Salary. Below the table, add a bar chart that visualizes the salary data. Include at least 5 sample employees with different departments and salary ranges.
```

这将创建你的主仪表板页面，包含数据的表格和可视化表示。AI 将生成员工记录样本，并创建一个柱状图，便于比较团队中的薪资。

```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/overview/tutorial_1_light.avif",
        alt="Creating a dashboard in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

## 添加交互式过滤

现在你有了基本的仪表板，让我们通过添加过滤和搜索员工数据的功能使其更具交互性。

**提示词：**
```
Add filtering functionality to the employee table. Include a search input above the table that filters rows based on name, and dropdown filters for department. Make sure the filters work together and update the table in real-time.
```

你的仪表板现在通过实时过滤变得更加实用。用户可以按姓名快速查找特定员工，或按部门缩小结果范围。过滤器协同工作，因此你可以将部门过滤与姓名搜索结合使用。

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/overview/tutorial_2_light.avif",
        alt="Adding interactive filtering in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```


## 启用数据录入

静态仪表板很有用，但能够添加新数据会使你的应用更加实用。让我们添加创建新员工记录的功能。

**提示词：**
```
Add an "Add Employee" button above the table. When clicked, open a modal with input fields for Name, Department, and Salary. When the form is submitted, add the new employee to the table and update the bar chart. Include form validation for required fields.
```

你的应用现在具有员工记录的完整 CRUD 功能。模态表单提供了干净的数据录入界面，当添加新员工时，表格和图表都会立即更新。


```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/overview/tutorial_3_light.avif",
        alt="Enabling data entry in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```


## 构建多页面应用

大多数实际应用都有多个页面。让我们添加一个聊天机器人页面来演示导航，并创建更完整的用户体验。

**提示词：**
```
Create a new page called "Chat" and add it to the navigation. Build a simple chatbot interface with a message input field, send button, and chat history display. For now, make the bot echo back the user's messages with "Bot says: [user message]".
```

你的应用现在在仪表板和聊天功能之间有了适当的导航。聊天机器人页面演示了向 AI Builder 应用添加新功能和页面是多么容易。


```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/overview/tutorial_4_light.avif",
        alt="Building a multi-page app in Reflex AI Builder",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
    class_name="w-full flex flex-col rounded-md",
)
```

## 接下来做什么？

你已经成功构建了一个完整的 Web 应用，包含数据可视化、交互式过滤、数据录入和多页面导航。你的应用演示了现代 Web 应用中许多常见的模式：

- **数据展示** 使用表格和图表
- **用户交互** 通过过滤和表单
- **实时更新** 当数据变化时
- **多页面架构** 带有导航

## 进一步探索

现在你有了一个可工作的基础，尝试用这些想法进行实验：

- **自定义数据模型** - 更改员工字段或添加新列
- **增强可视化** - 尝试不同的图表类型或添加更多图表
- **改进聊天机器人** - 给它更复杂的响应或将其与员工数据集成
- **添加更多页面** - 创建额外功能，如员工资料或报告仪表板

AI Builder 的强大之处在于你可以使用自然语言提示词快速迭代。每个新功能都只需一次对话！
