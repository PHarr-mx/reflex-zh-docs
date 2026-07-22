---
title: Interactive Maps
---

# 交互式地图

```python exec
import reflex as rx
import reflex_enterprise as rxe
```

Reflex Enterprise 中的地图组件提供了基于 **Leaflet**（最流行的开源 JavaScript 地图库之一）构建的交互式地图功能。这些组件使你能够创建包含标记、图层、控件和事件处理的丰富交互式地图。

```md alert info
# 所有地图组件均基于 Leaflet 和 react-leaflet 构建，提供熟悉且强大的地图体验。
如需了解高级 Leaflet 功能，请参阅 [Leaflet 文档](https://leafletjs.com/reference.html)。
```

🌍 **[查看在线演示](https://map.reflex.run)** - 通过交互式示例查看地图组件的实际效果。

## 安装与配置

地图组件已包含在 `reflex-enterprise` 中，无需额外安装。

## 基本用法

以下是创建带标记地图的简单示例：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class MapState(rx.State):
    center: rxe.map.LatLng = rxe.map.latlng(lat=51.505, lng=-0.09)
    zoom: float = 13.0


def basic_map():
    return rxe.map(
        rxe.map.tile_layer(
            url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
            attribution='&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
        ),
        rxe.map.marker(
            rxe.map.popup("Hello from London!"),
            position=MapState.center,
        ),
        id="basic-map",
        center=MapState.center,
        zoom=MapState.zoom,
        height="400px",
        width="100%",
    )
```

## 核心组件

### 地图容器

`rxe.map()` 组件是容纳所有其他地图元素的主容器：

```python
rxe.map(
    # 子组件（标记、图层、控件）
    id="my-map",
    center=rxe.map.latlng(lat=51.505, lng=-0.09),
    zoom=13,
    height="400px",
    width="100%",
)
```

**关键属性：**
- `center`：初始地图中心坐标
- `zoom`：初始缩放级别（0-18+，取决于瓦片提供商）
- `bounds`：center/zoom 的替代方案，将地图适配到边界范围
- `height`/`width`：地图容器尺寸

### 瓦片图层（Tile Layers）

瓦片图层提供底图图像。最常用的是 OpenStreetMap：

```python
rxe.map.tile_layer(
    url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
    attribution="&copy; OpenStreetMap contributors",
)
```


### 标记（Markers）

在特定位置添加点标记：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def markers_example():
    return rxe.map(
        rxe.map.tile_layer(
            url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
            attribution="&copy; OpenStreetMap contributors",
        ),
        rxe.map.marker(
            rxe.map.popup(
                rx.vstack(
                    rx.text("Big Ben", weight="bold"),
                    rx.text("Famous clock tower in London"),
                    spacing="2",
                )
            ),
            position=rxe.map.latlng(lat=51.4994, lng=-0.1245),
        ),
        rxe.map.marker(
            rxe.map.popup("London Eye"),
            position=rxe.map.latlng(lat=51.5033, lng=-0.1196),
        ),
        id="markers-map",
        center=rxe.map.latlng(lat=51.501, lng=-0.122),
        zoom=14,
        height="400px",
        width="100%",
    )
```

### 矢量图层（Vector Layers）

在地图上绘制形状和区域：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def vectors_example():
    return rxe.map(
        rxe.map.tile_layer(
            url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
            attribution="&copy; OpenStreetMap contributors",
        ),
        # 圆形（半径单位为米）
        rxe.map.circle(
            center=rxe.map.latlng(lat=51.505, lng=-0.09),
            radius=500,
            path_options=rxe.map.path_options(
                color="#ff0000", fill_color="#ff3333", fill_opacity=0.3, weight=2
            ),
        ),
        # 多边形
        rxe.map.polygon(
            positions=[
                rxe.map.latlng(lat=51.515, lng=-0.08),
                rxe.map.latlng(lat=51.515, lng=-0.07),
                rxe.map.latlng(lat=51.520, lng=-0.07),
                rxe.map.latlng(lat=51.520, lng=-0.08),
            ],
            path_options=rxe.map.path_options(
                color="#0000ff", fill_color="#3333ff", fill_opacity=0.3
            ),
        ),
        # 折线
        rxe.map.polyline(
            positions=[
                rxe.map.latlng(lat=51.500, lng=-0.095),
                rxe.map.latlng(lat=51.510, lng=-0.085),
                rxe.map.latlng(lat=51.515, lng=-0.095),
            ],
            path_options=rxe.map.path_options(color="#00ff00", weight=4),
        ),
        id="vectors-map",
        center=rxe.map.latlng(lat=51.510, lng=-0.08),
        zoom=13,
        height="400px",
        width="100%",
    )
```

## 交互功能

### 事件处理

地图支持全面的用户交互事件处理：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class InteractiveMapState(rx.State):
    last_click: str = "No clicks yet"
    current_zoom: float = 13.0

    def handle_map_click(self, event):
        lat = event.get("latlng", {}).get("lat", 0)
        lng = event.get("latlng", {}).get("lng", 0)
        self.last_click = f"Clicked at: {lat:.4f}, {lng:.4f}"

    def handle_zoom_change(self, event):
        self.current_zoom = float(event.get("target", {}).get("_zoom", 13.0))


def interactive_example():
    return rx.vstack(
        rx.text(f"Last click: {InteractiveMapState.last_click}"),
        rx.text(f"Current zoom: {InteractiveMapState.current_zoom}"),
        rxe.map(
            rxe.map.tile_layer(
                url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
                attribution="&copy; OpenStreetMap contributors",
            ),
            id="interactive-map",
            center=rxe.map.latlng(lat=51.505, lng=-0.09),
            zoom=InteractiveMapState.current_zoom,
            height="350px",
            width="100%",
            on_click=InteractiveMapState.handle_map_click,
            on_zoom=InteractiveMapState.handle_zoom_change,
        ),
        spacing="3",
    )
```

### 地图控件

添加 UI 控件以增强用户交互：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


def controls_example():
    return rxe.map(
        rxe.map.tile_layer(
            url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
            attribution="&copy; OpenStreetMap contributors",
        ),
        rxe.map.zoom_control(position="topright"),
        rxe.map.scale_control(position="bottomleft"),
        rxe.map.attribution_control(position="bottomright"),
        id="controls-map",
        center=rxe.map.latlng(lat=51.505, lng=-0.09),
        zoom=13,
        height="400px",
        width="100%",
    )
```

## 辅助函数

### 坐标创建

```python
# 创建经纬度坐标
center = rxe.map.latlng(lat=51.505, lng=-0.09, nround=4)

# 创建边界
bounds = rxe.map.latlng_bounds(
    corner1_lat=51.49, corner1_lng=-0.11, corner2_lat=51.52, corner2_lng=-0.07
)
```

## 地图 API

地图 API 提供对地图的编程控制，允许你从 Reflex 状态方法中以编程方式操作地图。

### 获取 API 引用

要访问地图 API，你需要使用地图的 ID 获取对地图的引用：

```python
map_api = rxe.map.api("my-map-id")
```

### 交互式演示

以下是一些常用 API 方法的实际演示：

```python demo exec
import reflex as rx
import reflex_enterprise as rxe

map_api = rxe.map.api("api-demo-map")


class MapAPIState(rx.State):
    current_location: str = "London"

    def fly_to_london(self):
        yield map_api.fly_to([51.505, -0.09], 13)
        self.current_location = "London"

    def fly_to_paris(self):
        yield map_api.fly_to([48.8566, 2.3522], 13)
        self.current_location = "Paris"


def map_api_example():
    return rx.vstack(
        rx.text(f"Current location: {MapAPIState.current_location}"),
        rx.hstack(
            rx.button("Fly to London", on_click=MapAPIState.fly_to_london),
            rx.button("Fly to Paris", on_click=MapAPIState.fly_to_paris),
            rx.button("Zoom Out", on_click=map_api.set_zoom(8)),
            rx.button(
                "Log Center", on_click=map_api.get_center(callback=rx.console_log)
            ),
            spacing="2",
        ),
        rxe.map(
            rxe.map.tile_layer(
                url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
                attribution="&copy; OpenStreetMap contributors",
            ),
            id="api-demo-map",
            center=rxe.map.latlng(lat=51.505, lng=-0.09),
            zoom=13.0,
            height="350px",
            width="100%",
        ),
        spacing="3",
    )
```

### 常用 API 方法

**视图控制：**
- `fly_to(latlng, zoom, options)` - 平滑动画移动到指定位置
- `set_view(latlng, zoom, options)` - 即时移动到指定位置
- `set_zoom(zoom)` - 更改缩放级别
- `zoom_in()` / `zoom_out()` - 缩放一个级别
- `fit_bounds(bounds, options)` - 将地图适配到指定边界

**定位服务：**
- `locate(options)` - 获取用户当前位置
- `stop_locate()` - 停止位置追踪

**信息获取：**
- `get_center(callback)` - 获取当前地图中心
- `get_zoom(callback)` - 获取当前缩放级别
- `get_bounds(callback)` - 获取当前地图边界
- `get_size(callback)` - 获取地图容器尺寸

**图层管理：**
- `add_layer(layer)` - 向地图添加图层
- `remove_layer(layer)` - 从地图移除图层
- `has_layer(layer)` - 检查地图上是否存在某图层

### 完整 Leaflet API 访问

```md alert info
# 地图 API 提供对完整 Leaflet 地图 API 的访问。Leaflet 地图实例上可用的任何方法都可以通过 MapAPI 实例调用。
函数名会自动从 snake_case（Python）转换为 camelCase（JavaScript）。
```

这意味着你可以使用 [Leaflet Map 文档](https://leafletjs.com/reference.html#map)中的任何方法。例如：

**Python (snake_case) → JavaScript (camelCase)：**
- `map_api.pan_to(latlng)` → `map.panTo(latlng)`
- `map_api.set_max_bounds(bounds)` → `map.setMaxBounds(bounds)`
- `map_api.get_pixel_bounds()` → `map.getPixelBounds()`
- `map_api.container_point_to_lat_lng(point)` → `map.containerPointToLatLng(point)`

### 高级示例

```python demo exec
import reflex as rx
import reflex_enterprise as rxe


class AdvancedMapState(rx.State):
    constraints_applied: bool = False
    location_tracking: bool = False
    location_status: str = "Location tracking disabled"

    def set_location_status(self, status: str):
        self.location_status = status

    def setup_map_constraints(self):
        map_api = rxe.map.api("advanced-demo-map")

        # 设置最大边界（将平移限制在伦敦区域）
        max_bounds = rxe.map.latlng_bounds(
            corner1_lat=51.4, corner1_lng=-0.3, corner2_lat=51.6, corner2_lng=0.1
        )
        yield map_api.set_max_bounds(max_bounds)

        # 设置最小/最大缩放级别
        yield map_api.set_min_zoom(10)
        yield map_api.set_max_zoom(16)

        # 禁用滚轮缩放
        yield map_api.scroll_wheel_zoom(False)

        self.constraints_applied = True

    def remove_constraints(self):
        map_api = rxe.map.api("advanced-demo-map")

        # 移除边界限制
        yield map_api.set_max_bounds(None)

        # 重置缩放限制
        yield map_api.set_min_zoom(1)
        yield map_api.set_max_zoom(18)

        # 重新启用滚轮缩放
        yield map_api.scroll_wheel_zoom(True)

        self.constraints_applied = False

    def toggle_location_tracking(self):
        map_api = rxe.map.api("advanced-demo-map")

        if self.location_tracking == False:
            # 开始位置追踪
            locate_options = rxe.map.locate_options(
                set_view=True,
                max_zoom=16,
                timeout=10000,
                enable_high_accuracy=True,
                watch=False,  # 单次位置请求
            )
            yield map_api.locate(locate_options)
            self.location_tracking = True
            self.location_status = "Requesting location..."
        else:
            # 停止位置追踪
            yield map_api.stop_locate()
            self.location_tracking = False
            self.location_status = "Location tracking disabled"


def advanced_example():
    return rx.vstack(
        rx.hstack(
            rx.button(
                rx.cond(
                    AdvancedMapState.constraints_applied,
                    "Remove Constraints",
                    "Apply Constraints",
                ),
                on_click=rx.cond(
                    AdvancedMapState.constraints_applied,
                    AdvancedMapState.remove_constraints,
                    AdvancedMapState.setup_map_constraints,
                ),
                color_scheme="blue",
            ),
            rx.button(
                rx.cond(
                    AdvancedMapState.location_tracking,
                    "Disable Location",
                    "Enable Location",
                ),
                on_click=AdvancedMapState.toggle_location_tracking,
                color_scheme="green",
            ),
            spacing="3",
        ),
        rx.text(f"Status: {AdvancedMapState.location_status}"),
        rx.text(
            rx.cond(
                AdvancedMapState.constraints_applied,
                "Constraints: Applied (restricted to London area, zoom 10-16, no scroll wheel)",
                "Constraints: None",
            )
        ),
        rxe.map(
            rxe.map.tile_layer(
                url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
                attribution="&copy; OpenStreetMap contributors",
            ),
            rxe.map.marker(
                rxe.map.popup("Try panning and zooming when constraints are applied!"),
                position=rxe.map.latlng(lat=51.505, lng=-0.09),
            ),
            id="advanced-demo-map",
            center=rxe.map.latlng(lat=51.505, lng=-0.09),
            zoom=12.0,
            height="400px",
            width="100%",
            on_locationfound=lambda e: AdvancedMapState.set_location_status(
                "Location found!"
            ),
            on_locationerror=lambda e: AdvancedMapState.set_location_status(
                "Location error - permission denied or unavailable"
            ),
        ),
        spacing="3",
    )
```

### 回调处理

许多用于获取信息的 API 方法需要回调来处理结果：

```python
class CallbackMapState(rx.State):
    map_info: str = ""

    def handle_center_result(self, result):
        lat = result.get("lat", 0)
        lng = result.get("lng", 0)
        self.map_info = f"Center: {lat:.4f}, {lng:.4f}"

    def handle_bounds_result(self, result):
        # result 将包含边界信息
        self.map_info = f"Bounds: {result}"

    def get_map_info(self):
        map_api = rxe.map.api("info-map")
        yield map_api.get_center(self.handle_center_result)
        # 或者
        yield map_api.get_bounds(self.handle_bounds_result)
```

## 可用事件

地图组件支持全面的事件集：

**地图事件：**
- `on_click`、`on_dblclick` - 鼠标点击事件
- `on_zoom`、`on_zoom_start`、`on_zoom_end` - 缩放事件
- `on_move`、`on_move_start`、`on_move_end` - 平移事件
- `on_resize` - 地图容器大小调整
- `on_load`、`on_unload` - 地图生命周期

**定位事件：**
- `on_locationfound`、`on_locationerror` - 地理定位

**图层事件：**
- `on_layeradd`、`on_layerremove` - 图层管理

**弹窗事件：**
- `on_popupopen`、`on_popupclose` - 弹窗生命周期
- `on_tooltipopen`、`on_tooltipclose` - 提示框生命周期

## 常见模式

### 动态标记

```python
class DynamicMapState(rx.State):
    markers: list[dict] = [
        {"lat": 51.505, "lng": -0.09, "title": "London"},
        {"lat": 48.8566, "lng": 2.3522, "title": "Paris"},
        {"lat": 52.5200, "lng": 13.4050, "title": "Berlin"},
    ]


def dynamic_markers():
    return rxe.map(
        rxe.map.tile_layer(url="..."),
        rx.foreach(
            DynamicMapState.markers,
            lambda marker: rxe.map.marker(
                rxe.map.popup(marker["title"]),
                position=rxe.map.latlng(lat=marker["lat"], lng=marker["lng"]),
            ),
        ),
        # ... 地图配置
    )
```


## 最佳实践

1. **始终包含归属信息**——为瓦片提供商添加 attribution
2. **设置合理的缩放级别**（通常为 1-18）
3. **对多个标记使用 bounds**——而非任意的 center/zoom
4. **处理加载状态**——针对动态地图内容
5. **优化标记渲染**——对大数据集使用聚合
6. **在移动设备上测试**——确保触摸交互正常

---

[← 返回主文档](/docs/enterprise/overview/)
