```python exec
import reflex as rx
```

# 插件（Plugins）

Reflex 支持一个插件系统，允许你在编译过程中扩展框架的功能。插件可以添加前端依赖、修改构建配置、生成静态资产，并在编译前执行自定义任务。

## 配置插件

插件在你的 `rxconfig.py` 文件中使用 `plugins` 参数进行配置：

```python
import reflex as rx

config = rx.Config(
    app_name="my_app",
    plugins=[
        rx.plugins.SitemapPlugin(),
        rx.plugins.TailwindV4Plugin(),
    ],
)
```

## 内置插件

Reflex 附带了几个提供常见功能的内置插件。

### SitemapPlugin

`SitemapPlugin` 自动为你的应用生成 sitemap.xml 文件，这有助于搜索引擎发现和索引你的页面。

```python
import reflex as rx

config = rx.Config(
    app_name="my_app",
    plugins=[
        rx.plugins.SitemapPlugin(),
    ],
)
```

sitemap 插件自动包含你应用的所有路由。对于动态路由或自定义配置，你可以向各个页面添加 sitemap 元数据：

```python
@rx.page(
    route="/blog/[slug]", context={"sitemap": {"changefreq": "weekly", "priority": 0.8}}
)
def blog_post():
    return rx.text("Blog post content")


@rx.page(
    route="/about", context={"sitemap": {"changefreq": "monthly", "priority": 0.5}}
)
def about():
    return rx.text("About page")
```

sitemap 配置支持以下选项：

- `loc`：页面的自定义 URL（动态路由必需）
- `lastmod`：最后修改日期（datetime 对象）
- `changefreq`：页面更改的频率（`"always"`、`"hourly"`、`"daily"`、`"weekly"`、`"monthly"`、`"yearly"`、`"never"`）
- `priority`：此 URL 相对于其他 URL 的优先级（0.0 到 1.0）

### TailwindV4Plugin

`TailwindV4Plugin` 提供对 Tailwind CSS v4 的支持，这是新项目的推荐版本，包含性能改进和新功能。

```python
import reflex as rx

# 基本配置
config = rx.Config(
    app_name="my_app",
    plugins=[
        rx.plugins.TailwindV4Plugin(),
    ],
)
```

你可以通过传递配置字典来自定义 Tailwind 配置：

```python
import reflex as rx

tailwind_config = {
    "theme": {
        "extend": {
            "colors": {
                "brand": {
                    "50": "#eff6ff",
                    "500": "#3b82f6",
                    "900": "#1e3a8a",
                }
            }
        }
    },
    "plugins": ["@tailwindcss/typography"],
}

config = rx.Config(
    app_name="my_app",
    plugins=[
        rx.plugins.TailwindV4Plugin(tailwind_config),
    ],
)
```

### TailwindV3Plugin

`TailwindV3Plugin` 将 Tailwind CSS v3 集成到你的 Reflex 应用中。虽然仍然受支持，但新项目推荐使用 TailwindV4Plugin。

```python
import reflex as rx

# 基本配置
config = rx.Config(
    app_name="my_app",
    plugins=[
        rx.plugins.TailwindV3Plugin(),
    ],
)
```

你可以通过传递配置字典来自定义 Tailwind 配置：

```python
import reflex as rx

tailwind_config = {
    "theme": {
        "extend": {
            "colors": {
                "primary": "#3b82f6",
                "secondary": "#64748b",
            }
        }
    },
    "plugins": ["@tailwindcss/typography", "@tailwindcss/forms"],
}

config = rx.Config(
    app_name="my_app",
    plugins=[
        rx.plugins.TailwindV3Plugin(tailwind_config),
    ],
)
```

## 插件管理

### 默认插件

某些插件默认启用。目前，`SitemapPlugin` 会自动启用。如果你想禁用默认插件，使用 `disable_plugins` 参数：

```python
import reflex as rx

config = rx.Config(
    app_name="my_app",
    disable_plugins=["reflex.plugins.sitemap.SitemapPlugin"],
)
```

### 插件顺序

插件按它们在 `plugins` 列表中出现的顺序执行。如果插件相互依赖或修改相同的文件，这可能很重要。

```python
import reflex as rx

config = rx.Config(
    app_name="my_app",
    plugins=[
        rx.plugins.TailwindV4Plugin(),  # 先运行
        rx.plugins.SitemapPlugin(),  # 后运行
    ],
)
```

## 插件架构

所有插件都继承自基础 `Plugin` 类，并可以实现几个生命周期方法：

```python
class Plugin:
    def get_frontend_development_dependencies(self, **context) -> list[str]:
        """Get NPM packages required by the plugin for development."""
        return []

    def get_frontend_dependencies(self, **context) -> list[str]:
        """Get NPM packages required by the plugin."""
        return []

    def get_static_assets(self, **context) -> Sequence[tuple[Path, str | bytes]]:
        """Get static assets required by the plugin."""
        return []

    def get_stylesheet_paths(self, **context) -> Sequence[str]:
        """Get paths to stylesheets required by the plugin."""
        return []

    def pre_compile(self, **context) -> None:
        """Called before compilation to perform custom tasks."""
        pass
```

### 创建自定义插件

你可以通过继承基础 `Plugin` 类来创建自定义插件：

```python
from reflex.plugins.base import Plugin
from pathlib import Path


class CustomPlugin(Plugin):
    def get_frontend_dependencies(self, **context):
        return ["my-custom-package@1.0.0"]

    def pre_compile(self, **context):
        # 编译前的自定义逻辑
        print("Running custom plugin logic...")

        # 添加自定义任务
        context["add_save_task"](self.create_custom_file)

    def create_custom_file(self):
        return "public/custom.txt", "Custom content"
```

然后在你的配置中使用它：

```python
import reflex as rx
from my_plugins import CustomPlugin

config = rx.Config(
    app_name="my_app",
    plugins=[
        CustomPlugin(),
    ],
)
```
