```python exec
import reflex as rx
```

# 资源文件

图片和样式表等静态文件可以放在项目的 `assets/` 文件夹中。这些文件可以在你的应用中被引用。

```md alert
# 资源文件在构建过程中被复制。

在运行时放入 `assets/` 文件夹的任何文件在生成模式下运行时将不可用于应用。`assets/` 文件夹应仅用于静态文件。
```

## 引用资源文件

在 Reflex 应用中有两种方式引用资源文件：

### 1. 直接路径引用

要引用 `assets/` 文件夹中的图片，请将相对路径作为 prop 传递。

例如，你可以将 logo 放在 assets 文件夹中：

```bash
assets
└── Reflex.svg
```

然后你可以使用 `rx.image` 组件来显示它：

```python demo
rx.image(
    src="https://web.reflex-assets.dev/other/Reflex.svg", alt="Reflex logo", width="5em"
)
```

```md alert
# 始终在资源路径前加上前导斜杠 `/`，以便从项目根目录引用资源，否则它在非根页面上可能无法正确显示。
```

### 2. 使用 rx.asset 函数

`rx.asset` 函数提供了一种更灵活的方式来引用应用中的资源文件。它同时支持本地资源（在应用的 `assets/` 目录中）和共享资源（放在你的 Python 文件旁）。

#### 本地资源

本地资源存储在应用的 `assets/` 目录中，使用 `rx.asset` 引用：

```python demo
rx.image(src=rx.asset("Reflex.svg"), alt="Reflex logo", width="5em")
```

#### 共享资源

共享资源放在你的 Python 文件旁，并链接到应用的外部资源目录。这对于创建带自有资源的可复用组件非常有用：

```python box
# my_component.py
import reflex as rx


# my_script.js is located in the same directory as this Python file
def my_component():
    return rx.box(
        rx.script(src=rx.asset("my_script.js", shared=True)),
        "Component with custom script",
    )
```

你也可以为共享资源指定子文件夹：

```python box
# my_component.py
import reflex as rx


# image.png is located in a subfolder next to this Python file
def my_component_with_image():
    return rx.image(
        src=rx.asset("image.png", shared=True, subfolder="images"),
        alt="Example image asset",
    )
```

```md alert
# 共享资源通过符号链接连接到你的应用。

当使用 `shared=True` 时，资源会从其原始位置通过符号链接到应用的外部资源目录。这让你可以将资源与其相关代码放在一起。
```

## 网站图标

网站图标是出现在浏览器标签页中的小图标。

你可以将一个 `favicon.ico` 文件添加到 `assets/` 文件夹来更改网站图标。
