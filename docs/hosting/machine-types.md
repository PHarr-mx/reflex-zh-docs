```python exec
import reflex as rx
```

# 机器类型（Machine Types）

## 机器类型


要扩展你的应用，你可以选择不同的 VMTypes。VMTypes 是 CPU 和 RAM 的不同配置。

要在 Cloud UI 中扩展你的 VM，点击应用页面 Cloud UI 中的 `Settings` 选项卡，然后点击如下所示的 `Scale` 选项卡。点击 `Change VM` 按钮将允许你扩展你的应用。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/scaling_vms.webp",
    alt="Selecting machine types for scaling VMs",
    padding_bottom="20px",
)
```

### CLI 中的 VMTypes

要获取所有可能的 VMTypes，你可以运行以下命令：

```bash
reflex cloud vmtypes
```

要在部署应用时设置使用哪个 VMType，你可以传递带有 VMType ID 的 `--vmtype` 标志。例如：

```bash
reflex deploy --project f88b1574-f101-####-####-5f########## --vmtype c2m4
```

这将使用 `c2m4` VMType 部署你的应用，为你的应用提供 2 个 CPU 核心和 4 GB RAM。
