---
components:
  - rx.script
---

```python exec
import reflex as rx
```

# Script（脚本）

Script 组件可用于引入内联 JavaScript 或通过 URL 引入 JavaScript 文件。

它使用 [`next/script` 组件](https://nextjs.org/docs/app/api-reference/components/script)来注入脚本，可以安全地与条件渲染配合使用，从而通过状态控制脚本的副作用。

```python
rx.script("console.log('inline javascript')")
```

应避免复杂的内联脚本。
如果要包含的代码超过几行，更易于维护的做法是将其实现为 `assets` 目录中的独立 JavaScript 文件，然后通过 `src` prop 引入。

```python
rx.script(src="/my-custom.js")
```

此组件对于引入统计追踪和社交脚本特别有用。
脚本标签所需的额外属性可以通过 `custom_attrs` prop 提供。

```python
rx.script(
    src="//gc.zgo.at/count.js",
    custom_attrs={"data-goatcounter": "https://reflextoys.goatcounter.com/count"},
)
```

此代码会渲染为类似以下内容，以启用第三方服务的统计计数。

```jsx
<script
  src="//gc.zgo.at/count.js"
  data-goatcounter="https://reflextoys.goatcounter.com/count"
  data-nscript="afterInteractive"
></script>
```
