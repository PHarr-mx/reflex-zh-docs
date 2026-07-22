```python exec
import reflex as rx


REGIONS_DICT = {
    "ams": "Amsterdam, Netherlands",
    "arn": "Stockholm, Sweden",
    "bom": "Mumbai, India",
    "cdg": "Paris, France",
    "dfw": "Dallas, Texas (US)",
    "ewr": "Secaucus, NJ (US)",
    "fra": "Frankfurt, Germany",
    "gru": "Sao Paulo, Brazil",
    "iad": "Ashburn, Virginia (US)",
    "jnb": "Johannesburg, South Africa",
    "lax": "Los Angeles, California (US)",
    "lhr": "London, United Kingdom",
    "nrt": "Tokyo, Japan",
    "ord": "Chicago, Illinois (US)",
    "sjc": "San Jose, California (US)",
    "sin": "Singapore, Singapore",
    "syd": "Sydney, Australia",
    "yyz": "Toronto, Canada",
}

COUNTRIES_CODES = {
    "ams": "NL",
    "arn": "SE",
    "bom": "IN",
    "cdg": "FR",
    "dfw": "US",
    "ewr": "US",
    "fra": "DE",
    "gru": "BR",
    "iad": "US",
    "jnb": "ZA",
    "lax": "US",
    "lhr": "GB",
    "nrt": "JP",
    "ord": "US",
    "sjc": "US",
    "sin": "SG",
    "syd": "AU",
    "yyz": "CA",
}
```

# 区域（Regions）

## 区域

要扩展你的应用，你可以选择不同的区域。区域是你的应用可以部署的全球不同位置。

要在 Cloud UI 中将你的应用扩展到多个区域，点击应用页面 Cloud UI 中的 `Settings` 选项卡，然后点击如下所示的 `Regions` 选项卡。点击 `Add new region` 按钮将允许你将应用扩展到多个区域。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/scaling_regions.webp",
    alt="Selecting deployment regions for scaling",
    padding_bottom="20px",
)
```

下表显示了可以部署的所有区域。

```python eval
rx.el.table(
    rx.el.thead(
        rx.el.tr(
            rx.el.th(
                rx.el.div(
                    "Region",
                ),
                class_name="px-6 py-3 text-left text-sm font-semibold text-secondary-12 text-nowrap",
            ),
            rx.el.th(
                rx.el.div(
                    "Country",
                ),
                class_name="px-6 py-3 text-left text-sm font-semibold text-secondary-12 text-nowrap",
            ),
        ),
        class_name="bg-secondary-2",
    ),
    rx.el.tbody(
        *[
            rx.el.tr(
                rx.el.td(
                    rx.el.div(
                        region,
                        class_name="h-5 rounded-md border justify-start items-center inline-flex bg-secondary-1 text-xs font-medium shrink-0 px-1.5 w-fit text-secondary-12 border-secondary-6",
                    ),
                    class_name="px-6 py-3",
                ),
                rx.el.td(
                    rx.el.div(
                        rx.image(
                            src=f"https://build.reflex.dev/flags/{COUNTRIES_CODES[region]}.svg",
                            alt="Region country flag",
                            class_name="rounded-[2px] mr-2 w-5 h-4",
                        ),
                        REGIONS_DICT[region],
                        class_name="flex flex-row items-center gap-2",
                    ),
                    class_name="px-6 py-3 text-sm font-medium text-secondary-9",
                ),
                class_name="even:bg-secondary-2 odd:bg-secondary-1 hover:bg-secondary-3",
            )
            for region in REGIONS_DICT.keys()
        ],
        class_name="divide-y divide-secondary-4",
    ),
    class_name="w-full table-fixed rounded-xl overflow-hidden divide-y divide-secondary-4",
)
```

### 在 CLI 中选择要部署的区域

以下是在多个区域中部署应用的示例：

```bash
reflex deploy --project f88b1574-f101-####-####-5f########## --region sjc --region iad
```

默认情况下，如果没有给出其他区域，所有应用都部署在 `sjc`。如果你想在另一个区域或多个区域部署，你可以传递带有区域代码的 `--region` 标志（`-r` 也可以）。查看下面我们可以部署到的所有区域：


## 配置文件

要为你的应用创建 `config.yml` 文件，运行以下命令：

```bash
reflex cloud config
```

这将创建一个类似于下面的 yaml 文件，你可以在其中编辑应用配置：

```yaml
name: medo
description: ''
regions:
  sjc: 1
  lhr: 2
vmtype: c1m1
hostname: null
envfile: .env
project: null
packages:
- procps
```

