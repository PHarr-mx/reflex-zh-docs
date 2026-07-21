# Reflex Build IDE

Reflex Build 包含一个**强大的浏览器内 IDE**，基于 **Monaco Editor** 构建，旨在使编码快速、高效且愉快——全部无需离开浏览器。

```python exec
import reflex as rx
```

```python eval
rx.el.div(
    rx.video(
        src="https://www.youtube.com/embed/UAj9vUweQ5g",
        width="100%",
        height="400px",
    ),
    class_name="p-1 my-4 rounded-lg bg-secondary-5",
)
```


## 功能

### 实时编辑
编辑你的代码并立即在项目中看到更改的反映。无需手动保存或重新构建——保持专注并更快地迭代。

### 语法高亮和错误检测
使用**语法高亮**、**内联错误警报**和**代码检查（linting）**自信地编写代码。在输入时发现问题，减少 bug 并加速开发。

### 代码片段和自动完成
使用函数、变量和导入的**自动完成**以及常见模式的可重用**代码片段**来加速开发。

### 集成预览
直接在编辑器中快速预览你的更改，无需切换上下文，确保你的应用在编码时按预期运行。

### 内置终端用于调试
使用集成终端运行命令、调试问题和检查日志——全部在 IDE 内完成。无需切换工具或选项卡。
