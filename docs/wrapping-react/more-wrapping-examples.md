# 更多 React 库

## AG Charts

这里我们封装来自 NPM 包 [ag-charts-react](https://www.npmjs.com/package/ag-charts-react) 的 AG Charts 库。

在下面的 React 代码中，前 `2` 行是导入 React 和 ReactDOM，这在封装组件时可以忽略。

第 5 行我们从 `ag-charts-react` 库中导入 `AgCharts` 组件。在 Reflex 中，这通过 `library = "ag-charts-react"` 和 `tag = "AgCharts"` 来封装。

第 `7` 行定义了一个 React 函数组件，在第 `26` 行返回 `AgCharts`，类似于 Reflex 代码中使用 `chart` 组件。

第 `9` 行使用 `useState` hook 创建了一个状态变量 `chartOptions` 及其 setter 函数 `setChartOptions`。在 Reflex 中，等价的是 `rx.State` 子类上的基本变量（base var），由事件处理器来修改。初始状态变量是 dict 类型，包含两个键值对 `data` 和 `series`。

当我们在 React 代码中看到 `useState` 时，它对应于 State 中的状态变量。正如你在我们的 Reflex 代码中看到的，我们有一个状态变量 `chart_options`，它是一个字典，就像在 React 代码中一样。

转到第 `26` 行，我们看到 `AgCharts` 有一个 `options` 属性。为了在 Reflex 中使用它，我们必须封装这个属性。我们在 `AgCharts` 组件中使用 `options: rx.Var[dict]` 来实现。

第 `31` 和 `32` 行在根元素内渲染组件。在封装组件时，这可以忽略，因为在 Reflex 中这是通过创建 `index` 函数并将其添加到应用来完成的。

`````md tabs

## React 代码

```javascript
1 | import React, { useState } from 'react';
2 | import ReactDOM from 'react-dom/client';
3 |
4 | // React Chart Component
5 | import { AgCharts } from 'ag-charts-react';
6 |
7 | const ChartExample = () => {
8 |     // Chart Options: Control & configure the chart
9 |     const [chartOptions, setChartOptions] = useState({
10|         // Data: Data to be displayed in the chart
11|         data: [
12|             { month: 'Jan', avgTemp: 2.3, iceCreamSales: 162000 },
13|             { month: 'Mar', avgTemp: 6.3, iceCreamSales: 302000 },
14|             { month: 'May', avgTemp: 16.2, iceCreamSales: 800000 },
15|             { month: 'Jul', avgTemp: 22.8, iceCreamSales: 1254000 },
16|             { month: 'Sep', avgTemp: 14.5, iceCreamSales: 950000 },
17|             { month: 'Nov', avgTemp: 8.9, iceCreamSales: 200000 },
18|         ],
19|         // Series: Defines which chart type and data to use
20|         series: [{ type: 'bar', xKey: 'month', yKey: 'iceCreamSales' }],
21|     });
22|
23|     // React Chart Component
24|     return (
25|         // AgCharts component with options passed as prop
26|         <AgCharts options={chartOptions} />
27|     );
28| }
29|
30| // Render component inside root element
31| const root = ReactDOM.createRoot(document.getElementById('root'));
32| root.render(<ChartExample />);
```


## Reflex 代码

```python
import reflex as rx

class AgCharts(rx.Component):
    """ A simple line chart component using AG Charts """

    library = "ag-charts-react"

    tag = "AgCharts"

    options: rx.Var[dict]


chart = AgCharts.create


class State(rx.State):
    """The app state."""
    chart_options: dict = {
           "data": [
                {"month":"Jan", "avgTemp":2.3, "iceCreamSales":162000},
                {"month":"Mar", "avgTemp":6.3, "iceCreamSales":302000},
                {"month":"May", "avgTemp":16.2, "iceCreamSales":800000},
                {"month":"Jul", "avgTemp":22.8, "iceCreamSales":1254000},
                {"month":"Sep", "avgTemp":14.5, "iceCreamSales":950000},
                {"month":"Nov", "avgTemp":8.9, "iceCreamSales":200000}
            ],
            "series": [{"type":"bar", "xKey":"month", "yKey":"iceCreamSales"}]
        }

def index() -> rx.Component:
    return chart(
        options=State.chart_options,
    )

app = rx.App()
app.add_page(index)
```

`````
## React Leaflet

在此示例中，我们封装来自 NPM 包 [react-leaflet](https://www.npmjs.com/package/react-leaflet) 的 React Leaflet 库。

第 `1` 行我们从 Next.js 导入 `dynamic` 函数，第 `21` 行设置 `ssr: false`。第 `4` 和 `6` 行使用 `dynamic` 函数从 `react-leaflet` 库中导入 `MapContainer` 和 `TileLayer` 组件。这用于从 `react-leaflet` 库动态导入 `MapContainer` 和 `TileLayer` 组件。在 Reflex 中，这通过在定义组件时使用 `NoSSRComponent` 类来实现。关于何时需要这样做的更多信息，请参见此[页面](/docs/wrapping-react/library-and-tags)的"动态导入"部分。

文档中提到需要包含 Leaflet CSS 文件，在下面的 React 代码中，这在第 `2` 行添加。在 Reflex 中，这可以通过在 `MapContainer` 组件中使用 `add_imports` 方法来实现。我们可以添加来自 React 库内部的相对路径或 CSS 文件的完整 URL。

第 `4` 行定义了一个 React 函数组件，在第 `8` 行返回 `MapContainer`，这在 Reflex 代码中通过使用 `map_container` 组件实现。

`MapContainer` 组件具有 `center`、`zoom`、`scrollWheelZoom` 等 props，我们在 Reflex 代码的 `MapContainer` 组件中封装了这些属性。我们忽略了 `style` 属性，因为它是 Reflex 中的保留名称。我们可以使用 `rename_props` 方法来更改属性名称，正如我们将在 React PDF Renderer 示例中看到的那样，但在本例中，我们只是忽略它，并在 Reflex 中将 `width` 和 `height` 作为 CSS 添加。

`TileLayer` 组件有一个 `url` 属性，我们在 Reflex 代码的 `TileLayer` 组件中封装了它。

第 `24` 和 `25` 行定义并导出了一个名为 `Home` 的 React 函数组件，它返回 `MapComponent` 组件。在封装组件时，这可以在 Reflex 代码中忽略，因为我们是在 `index` 函数中返回 `map_container` 组件。

`````md tabs

## React 代码

```javascript
1 | import dynamic from "next/dynamic";
2 | import "leaflet/dist/leaflet.css";
3 |
4 | const MapComponent = dynamic(
5 |   () => {
6 |     return import("react-leaflet").then(({ MapContainer, TileLayer }) => {
7 |       return () => (
8 |         <MapContainer
9 |           center={[51.505, -0.09]}
10|           zoom={13}
11|           scrollWheelZoom={true}
12|           style={{ height: "50vh", width: "100%" }}
13|        >
14|          <TileLayer
15|            url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
16|          />
17|        </MapContainer>
18|      );
19|    });
20|  },
21|  { ssr: false }
22| );
23|
24| export default function Home() {
25|   return <MapComponent />;
26| }
```


## Reflex 代码

```python
import reflex as rx

class MapContainer(rx.NoSSRComponent):

    library = "react-leaflet"

    tag = "MapContainer"

    center: rx.Var[list]

    zoom: rx.Var[int]

    scroll_wheel_zoom: rx.Var[bool]

    # Can also pass a url like: https://unpkg.com/leaflet/dist/leaflet.css
    def add_imports(self):
        return {"": ["leaflet/dist/leaflet.css"]}



class TileLayer(rx.NoSSRComponent):

    library = "react-leaflet"

    tag = "TileLayer"

    url: rx.Var[str]


map_container = MapContainer.create
tile_layer = TileLayer.create

def index() -> rx.Component:
    return map_container(
                tile_layer(url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"),
                center=[51.505, -0.09],
                zoom=13,
                #scroll_wheel_zoom=True
                width="100%",
                height="50vh",
            )


app = rx.App()
app.add_page(index)

```

`````
## React PDF Renderer

在此示例中，我们封装来自 NPM 包 [@react-pdf/renderer](https://www.npmjs.com/package/@react-pdf/renderer) 的 React PDF 渲染器，用于在浏览器和服务器上创建 PDF 文件。

这个示例与之前的示例类似，同样需要动态导入。在 Reflex 中，这通过在定义组件时使用 `NoSSRComponent` 类来实现。关于为什么需要这样做的更多信息，请参见此[页面](/docs/wrapping-react/library-and-tags)的"动态导入"部分。

本例的主要区别在于，React 代码中第 `20`、`21` 和 `24` 行使用的 `style` 属性是 Reflex 中的保留名称，因此无法封装。在封装此属性时必须使用不同的名称，然后通过 `rename_props` 方法将其更改回原始名称。在本例中，我们在 Reflex 代码中将此属性命名为 `theme`，然后在 `Page` 和 `View` 组件中通过 `rename_props` 方法将其改回 `style`。

```md alert info
# Reflex 中的保留名称列表

_组件的样式。_

`style: Style = Style()`

_从事件触发器到事件链的映射。_

`event_triggers: Dict[str, Union[EventChain, Var]] = {}`

_tag 的别名。_

`alias: Optional[str] = None`

_导入是默认还是命名。_

`is_default: Optional[bool] = False`

_组件的唯一键。_

`key: Any = None`

_组件的 id。_

`id: Any = None`

_组件的类名。_

`class_name: Any = None`

_特殊组件 props。_

`special_props: List[Var] = []`

_组件是否应在页面加载后获得焦点。_

`autofocus: bool = False`

_不能作为子组件的组件。_

`_invalid_children: List[str] = []`

_仅允许作为子组件的组件。_

`_valid_children: List[str] = []`

_仅允许作为父组件的组件。_

`_valid_parents: List[str] = []`

_要更改名称的 props。_

`_rename_props: Dict[str, str] = {}`

_自定义属性。_

`custom_attrs: Dict[str, Union[Var, str]] = {}`

_何时记忆化此组件及其子组件。_

`_memoization_mode: MemoizationMode = MemoizationMode()`

_与此组件实例关联的状态类。_

`State: Optional[Type[reflex.state.State]] = None`
```

`````md tabs

## React 代码

```javascript
1 | import ReactDOM from 'react-dom';
2 | import { Document, Page, Text, View, StyleSheet, PDFViewer } from '@react-pdf/renderer';
3 |
4 | // Create styles
5 | const styles = StyleSheet.create({
6 |   page: {
7 |     flexDirection: 'row',
8 |     backgroundColor: '#E4E4E4',
9 |   },
10|   section: {
11|     margin: 10,
12|    padding: 10,
13|     flexGrow: 1,
14|   },
15| });
16|
17| // Create Document Component
18| const MyDocument = () => (
19|   <Document>
20|     <Page size="A4" style={styles.page}>
21|       <View style={styles.section}>
22|         <Text>Section #1</Text>
23|       </View>
24|       <View style={styles.section}>
25|         <Text>Section #2</Text>
26|       </View>
27|     </Page>
28|   </Document>
29| );
30|
31| const App = () => (
32|   <PDFViewer>
33|     <MyDocument />
34|   </PDFViewer>
35| );
36|
37| ReactDOM.render(<App />, document.getElementById('root'));
```


## Reflex 代码

```python
import reflex as rx


class Document(rx.Component):
    library = "@react-pdf/renderer"

    tag = "Document"


class Page(rx.Component):
    library = "@react-pdf/renderer"

    tag = "Page"

    size: rx.Var[str]
    # here we are wrapping style prop but as style is a reserved name in Reflex we must name it something else and then change this name with rename props method
    theme: rx.Var[dict]

    _rename_props: dict[str, str] = {
        "theme": "style",
    }


class Text(rx.Component):
    library = "@react-pdf/renderer"

    tag = "Text"


class View(rx.Component):
    library = "@react-pdf/renderer"

    tag = "View"

    # here we are wrapping style prop but as style is a reserved name in Reflex we must name it something else and then change this name with rename props method
    theme: rx.Var[dict]

    _rename_props: dict[str, str] = {
        "theme": "style",
    }


class StyleSheet(rx.Component):
    library = "@react-pdf/renderer"

    tag = "StyleSheet"

    page: rx.Var[dict]

    section: rx.Var[dict]


class PDFViewer(rx.NoSSRComponent):
    library = "@react-pdf/renderer"

    tag = "PDFViewer"


document = Document.create
page = Page.create
text = Text.create
view = View.create
style_sheet = StyleSheet.create
pdf_viewer = PDFViewer.create


styles = style_sheet({
    "page": {
        "flexDirection": "row",
        "backgroundColor": "#E4E4E4",
    },
    "section": {
        "margin": 10,
        "padding": 10,
        "flexGrow": 1,
    },
})


def index() -> rx.Component:
    return pdf_viewer(
        document(
            page(
                view(
                    text("Hello, World!"),
                    theme=styles.section,
                ),
                view(
                    text("Hello, 2!"),
                    theme=styles.section,
                ),
                size="A4",
                theme=styles.page,
            ),
        ),
        width="100%",
        height="80vh",
    )


app = rx.App()
app.add_page(index)
```

`````
