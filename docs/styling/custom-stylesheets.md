```python exec
import reflex as rx
```

# 自定义样式表（Custom Stylesheets）

Reflex 允许你添加自定义样式表。只需将样式表的 URL 传递给 `rx.App`：

```python
app = rx.App(
    stylesheets=[
        "https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css",
    ],
)
```

## 本地样式表

你也可以添加本地样式表。只需将样式表放在 [`assets/`](/docs/assets/upload-and-download-files) 目录下，并将路径传递给 `rx.App`：

```python
app = rx.App(
    stylesheets=[
        "/styles.css",  # 此路径相对于 assets/
    ],
)
```

```md alert warning
# 引用 assets 目录中的文件时，始终使用前导斜杠（/）。

如果没有前导斜杠，路径将被视为相对于当前页面路由，对于包含多个路径组件的路由（如 `/blog/my-cool-post`）可能无法正常工作。
```

## 使用 CSS 样式

你可以通过将 CSS 变量与适当的属性一起传递，直接在 Reflex 应用中引用它们。在 `assets` 文件夹内创建一个 `style.css` 文件，包含以下内容：

```css
:root {
  --primary-color: blue;
  --accent-color: green;
}
```

然后，在 `rx.App` 的 `stylesheets` 属性中引用 CSS 文件后，你可以像这样直接访问 CSS 属性：

```python
app = rx.App(
    theme=rx.theme(appearance="light"),
    stylesheets=["/style.css"],
)
app.add_page(
    rx.center(
        rx.text("CSS Variables!"),
        width="100%",
        height="100vh",
        bg="var(--primary-color)",
    ),
    "/",
)
```

## SASS/SCSS 支持

Reflex 支持 SASS/SCSS 样式表以及常规 CSS。这使你可以使用更高级的样式特性，如变量、嵌套、混入（mixins）等。

### 使用 SASS/SCSS 文件

要在 Reflex 应用中使用 SASS/SCSS 文件：

1. 在 `assets` 目录中创建 `.sass` 或 `.scss` 文件
2. 在 `rx.App` 配置中引用该文件，就像使用 CSS 文件一样

```python
app = rx.App(
    stylesheets=[
        "/styles.scss",  # 此路径相对于 assets/
        "/sass/main.sass",  # 你也可以在子目录中组织文件
    ],
)
```

Reflex 会自动检测文件扩展名，并使用 `libsass` 包将这些文件编译为 CSS。

### SASS/SCSS 文件示例

以下是一个 SASS 文件（`assets/styles.scss`）的示例，展示了一些特性：

```scss
// 变量
$primary-color: #3498db;
$secondary-color: #2ecc71;
$padding: 16px;

// 嵌套
.container {
  background-color: $primary-color;
  padding: $padding;

  .button {
    background-color: $secondary-color;
    padding: $padding / 2;

    &:hover {
      opacity: 0.8;
    }
  }
}

// 混入
@mixin flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

.centered-box {
  @include flex-center;
  height: 100px;
}
```

### 依赖要求

SASS/SCSS 编译需要 `libsass` 包。如果未安装，Reflex 将显示错误消息。你可以使用以下命令安装：

```bash
pip install "libsass>=0.23.0"
```

该包已包含在默认的 Reflex 安装中，因此你通常无需单独安装。

## 字体（Fonts）

你可以利用 Reflex 对自定义样式表的支持，为应用添加自定义字体。

在此示例中，我们将使用 Google Fonts 的 [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) 字体。首先，向应用添加包含该字体的样式表。你可以从 Google 字体页面的"获取嵌入代码"部分获取此链接。

```python
app = rx.App(
    stylesheets=[
        "https://fonts.googleapis.com/css2?family=JetBrains+Mono:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;1,100;1,200;1,300;1,400;1,500;1,600;1,700&display=swap",
    ],
)
```

然后，你可以通过设置 `font_family` 属性在组件中使用该字体。

```python demo
rx.text(
    "Check out my font",
    font_family="JetBrains Mono",
    font_size="1.5em",
)
```

## 本地字体

结合以上两点，我们还可以创建一个样式表，让你使用托管在自己服务器上的字体。

如果你的字体名为 `MyFont.otf`，将其复制到 `assets/fonts` 目录。

现在字体已就绪，让我们创建样式表 `myfont.css`。

```css
@font-face {
  font-family: MyFont;
  src: url("/fonts/MyFont.otf") format("opentype");
}

@font-face {
  font-family: MyFont;
  font-weight: bold;
  src: url("/fonts/MyFont.otf") format("opentype");
}
```

在应用中添加对新样式表的引用。

```python
app = rx.App(
    stylesheets=[
        "/fonts/myfont.css",  # 此路径相对于 assets/
    ],
)
```

这样就完成了！你现在可以像使用其他任何字体一样使用 `MyFont` 来为组件设置样式。
