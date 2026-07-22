---
title: Reflex Enterprise
---

# Reflex Enterprise

```python exec
import reflex as rx

try:
    import reflex_enterprise as rxe
    from reflex_enterprise.components.ag_grid.resource import RendererParams
except ImportError:
    rxe = None
    RendererParams = None
```

Reflex Enterprise 是一个包含基于 Reflex 构建的付费功能的软件包。

Reflex Enterprise 的完整[最终用户许可协议（EULA）](https://raw.githubusercontent.com/reflex-dev/reflex/main/docs/enterprise/LICENSE)已发布在 Reflex 仓库中。

```md alert info
# 企业版组件对免费用户开放。
免费应用可以使用这些组件，但会在应用右下角显示 "Built with Reflex" 徽章。
有关徽章的更多信息，请访问 [Built with Reflex](/docs/enterprise/built-with-reflex/)。
```

## 安装

`reflex-enterprise` 必须与 `reflex` 一起安装才能使用企业版功能。

从 PyPI 安装：

```bash
pip install reflex-enterprise
```

## 功能

```python exec
# 创建按类别组织的主数据
categories_data = [
    {
        "category": "Configuration",
        "description": "Core enterprise features for deployment and branding",
        "count": 2,
        "components": [
            {
                "feature": "show_built_with_reflex",
                "description": "Toggle the 'Built with Reflex' badge in your app",
                "cloud_tier": "Enterprise",
                "self_hosted_tier": "Enterprise",
                "link": "/enterprise/built-with-reflex",
            },
            {
                "feature": "use_single_port",
                "description": "Enable single-port deployment by proxying backend to frontend",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/single-port-proxy",
            },
        ],
    },
    {
        "category": "Authentication",
        "description": "OIDC authentication with a secure-by-default model",
        "count": 6,
        "components": [
            {
                "feature": "AuthPlugin",
                "description": "OIDC (OpenID Connect) authentication with secure-by-default protection",
                "cloud_tier": "Enterprise",
                "self_hosted_tier": "Enterprise",
                "link": "/enterprise/auth/overview",
            },
            {
                "feature": "Secure by Default",
                "description": "Pages, event handlers, fields, and computed vars require login unless opted out",
                "cloud_tier": "Enterprise",
                "self_hosted_tier": "Enterprise",
                "link": "/enterprise/auth/secure-by-default",
            },
            {
                "feature": "OIDC Providers",
                "description": "Built-in generic OIDC provider plus named and multi-provider setups",
                "cloud_tier": "Enterprise",
                "self_hosted_tier": "Enterprise",
                "link": "/enterprise/auth/providers",
            },
            {
                "feature": "Custom Auth Pages",
                "description": "Replace the rendered login, callback, and logout pages",
                "cloud_tier": "Enterprise",
                "self_hosted_tier": "Enterprise",
                "link": "/enterprise/auth/custom-pages",
            },
            {
                "feature": "Testing",
                "description": "Unit-test authorization checks and exercise the full OIDC flow against a mock IdP",
                "cloud_tier": "Enterprise",
                "self_hosted_tier": "Enterprise",
                "link": "/enterprise/auth/testing",
            },
            {
                "feature": "Deploying to Production",
                "description": "HTTPS and cookie requirements, the exact redirect URI, reverse proxies, and a troubleshooting reference",
                "cloud_tier": "Enterprise",
                "self_hosted_tier": "Enterprise",
                "link": "/enterprise/auth/deployment",
            },
        ],
    },
    {
        "category": "Testing",
        "description": "pytest plugin for end-to-end testing of Reflex apps",
        "count": 1,
        "components": [
            {
                "feature": "reflex_app fixture",
                "description": "Boot the app under test with reflex run and drive it with browser tooling",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/testing",
            },
        ],
    },
    {
        "category": "AGGrid and AGChart",
        "description": "Advanced data visualization and grid components",
        "count": 2,
        "components": [
            {
                "feature": "AgGrid",
                "description": "Advanced data grid with enterprise features (sorting, filtering, grouping)",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/ag-grid",
            },
            {
                "feature": "AGCharts",
                "description": "Interactive charts and data visualization components",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/ag-chart",
            },
        ],
    },
    {
        "category": "Interactive Components",
        "description": "Interactive UI features including drag-and-drop and mapping",
        "count": 2,
        "components": [
            {
                "feature": "Drag and Drop",
                "description": "Drag and drop functionality for interactive UI elements",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/drag-and-drop",
            },
            {
                "feature": "Mapping",
                "description": "Interactive maps with markers, layers, and controls",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/map",
            },
        ],
    },
    {
        "category": "Mantine",
        "description": "Rich UI components from Mantine library",
        "count": 15,
        "components": [
            {
                "feature": "Autocomplete",
                "description": "Auto-completing text input with dropdown suggestions",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/autocomplete",
            },
            {
                "feature": "Combobox",
                "description": "Searchable dropdown with custom options and filtering",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/combobox",
            },
            {
                "feature": "Multi Select",
                "description": "Multi-selection dropdown with tags and search",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/multi-select",
            },
            {
                "feature": "Tags Input",
                "description": "Input field for creating and managing tags",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/tags-input",
            },
            {
                "feature": "Json Input",
                "description": "JSON editor with syntax highlighting and validation",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/json-input",
            },
            {
                "feature": "Pill",
                "description": "Small rounded elements for tags, badges, and labels",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/pill",
            },
            {
                "feature": "Tree",
                "description": "Hierarchical tree view with expandable nodes",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/tree",
            },
            {
                "feature": "Timeline",
                "description": "Timeline component for displaying chronological events",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/timeline",
            },
            {
                "feature": "Number Formatter",
                "description": "Format and display numbers with customizable formatting",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/number-formatter",
            },
            {
                "feature": "Ring Progress",
                "description": "Circular progress indicator with customizable styling",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/ring-progress",
            },
            {
                "feature": "Semi Circle Progress",
                "description": "Semi-circular progress indicator for dashboards",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/semi-circle-progress",
            },
            {
                "feature": "Loading Overlay",
                "description": "Loading overlay with spinner for async operations",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/loading-overlay",
            },
            {
                "feature": "Spoiler",
                "description": "Collapsible content container with show/hide toggle",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/spoiler",
            },
            {
                "feature": "Collapse",
                "description": "Animated collapsible content with smooth transitions",
                "cloud_tier": "Free",
                "self_hosted_tier": "Free",
                "link": "/enterprise/mantine/collapse",
            },
        ],
    },
]

if rxe is not None:

    @rxe.arrow_func
    def custom_link_renderer(params: RendererParams):
        """Custom cell renderer for links in AG Grid."""
        return rx.link(
            params.value,
            href=params.data.link,
        )

    grid = rxe.ag_grid(
        column_defs=[
            {
                "field": "category",
                "header_name": "Category",
                "cell_renderer": "agGroupCellRenderer",
                "suppress_menu": True,
                "width": 220,
            },
            {
                "field": "description",
                "width": 500,
            },
            {
                "field": "count",
                "header_name": "Components",
                "width": 150,
            },
        ],
        row_data=categories_data,
        master_detail=True,
        detail_cell_renderer_params={
            "detail_grid_options": {
                "column_defs": [
                    {
                        "field": "feature",
                        "header_name": "Component/Feature",
                        "cell_renderer": custom_link_renderer,
                        "width": 250,
                    },
                    {
                        "field": "description",
                        "header_name": "Description",
                        "width": 350,
                    },
                    {"field": "cloud_tier", "header_name": "Cloud Tier", "width": 120},
                    {
                        "field": "self_hosted_tier",
                        "header_name": "Self-hosted Tier",
                        "width": 140,
                    },
                ],
                "suppress_context_menu": True,
                "row_height": 35,
            },
            "get_detail_row_data": lambda params: rx.vars.function.FunctionStringVar(
                "params.successCallback"
            ).call(params.data.components),
        },
        id="features-grid",
        width="100%",
        detail_row_height=200,
        detail_row_auto_height=True,
        height="400px",
        loading=False,
    )
    grid.api.set_grid_option("suppressContextMenu", True)
else:
    grid = rx.text(
        "Reflex Enterprise not available. Install with: pip install reflex-enterprise"
    )
```

```python eval
grid
```

## 使用 reflex_enterprise

使用 `rxe.App` 作为你的 `app` 以启用企业版组件和 `rxe.Config` 选项。

### 在主文件中

使用 `rxe.App()` 创建应用：
```python
import reflex_enterprise as rxe

app = rxe.App()
```

### 在 rxconfig.py 中
```python
import reflex_enterprise as rxe
config = rxe.Config(
    app_name="MyApp",
    ... # 接受 rx.Config 参数以及 rxe.Config 选项
)
```
