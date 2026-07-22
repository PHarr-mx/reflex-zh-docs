# 主题（Theming）

你可以使用 CSS 自定义流程图（Flow）组件的外观。流程图组件自带一个默认主题，你可以用自己的样式覆盖它。

## CSS 变量

流程图组件使用 CSS 变量进行主题设置。你可以覆盖这些变量来改变流程图的外观。以下是一些最常见的变量：

```css
.react-flow {
  --xy-background-color: #f7f9fb;
  --xy-node-border-default: 1px solid #ededed;
  --xy-node-boxshadow-default: 0px 3.54px 4.55px 0px #00000005,
    0px 3.54px 4.55px 0px #0000000d, 0px 0.51px 1.01px 0px #0000001a;
  --xy-node-border-radius-default: 8px;
  --xy-handle-background-color-default: #ffffff;
  --xy-handle-border-color-default: #aaaaaa;
  --xy-edge-label-color-default: #505050;
}
```

## 自定义样式表

你可以向应用添加自定义样式表来覆盖默认样式。为此，请向你的 `rxe.App` 或 `rx.App` 实例添加 `stylesheets` 属性：

```python
app = rxe.App(
    stylesheets=[
        "/css/my-custom-styles.css",
    ],
)
```

然后，在你的项目中创建一个文件 `assets/css/my-custom-styles.css`，并在其中添加你的自定义样式。

## 自定义节点和边的样式

你还可以使用 `style` 和 `className` 属性为单个节点和边应用自定义样式。

### 使用 style 属性

你可以向节点或边的 `style` 属性传递一个样式字典：

```python
node = {
    "id": "1",
    "position": {"x": 100, "y": 100},
    "data": {"label": "Node 1"},
    "style": {"backgroundColor": "#ffcc00"},
}
```

### 使用 className 属性

你还可以向 `className` 属性传递一个类名，并在你的 CSS 文件中定义样式：

```python
# 在你的 Python 代码中
node = {
    "id": "1",
    "position": {"x": 100, "y": 100},
    "data": {"label": "Node 1"},
    "className": "my-custom-node",
}
```

```css
/* 在你的 CSS 文件中 */
.my-custom-node {
    background-color: #ffcc00;
    border: 2px solid #ff9900;
    border-radius: 10px;
}
```
