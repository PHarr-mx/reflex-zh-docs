---
components:
  - rx.moment
---

# 时间显示（Moment）

显示日期和相对时间有时可能比必要的更加复杂。

为了简化这一过程，Reflex 将 [react-moment](https://www.npmjs.com/package/react-moment) 封装为 `rx.moment`。

```python exec
import reflex as rx
from reflex.utils.serializers import serialize_datetime
```

## 示例

使用状态变量中的日期作为值，我们将通过 `rx.moment` 以几种不同的方式显示它。

`date_now` 状态变量在站点部署时初始化。下方的按钮可用于将该变量更新为当前日期时间，更新后会反映在后续的示例中。

```python demo exec
from datetime import datetime, timezone


class MomentState(rx.State):
    date_now: datetime = datetime.now(timezone.utc)

    @rx.event
    def update(self):
        self.date_now = datetime.now(timezone.utc)


def moment_update_example():
    return rx.button(
        "Update", rx.moment(MomentState.date_now), on_click=MomentState.update
    )
```

### 原样显示日期：

```python demo
rx.moment(MomentState.date_now)
```

### 人性化时间间隔

有时我们不想只显示原始日期，而是想要更直观的显示方式。这时我们可以使用 `from_now` 和 `to_now`。

```python demo
rx.moment(MomentState.date_now, from_now=True)
```

```python demo
rx.moment(MomentState.date_now, to_now=True)
```

你还可以使用 `from_now_during` 设置一个持续时间（以毫秒为单位），在此期间日期将以相对时间显示，之后将按照 `format` 中定义的格式显示。

```python demo
rx.moment(
    MomentState.date_now, from_now_during=100000
)  # 100 秒后，日期将正常显示
```

### 格式化日期

```python demo
rx.moment(MomentState.date_now, format="YYYY-MM-DD")
```

```python demo
rx.moment(MomentState.date_now, format="HH:mm:ss")
```

### 日期偏移

通过 `add` 和 `subtract` 属性，你可以传入一个 `rx.MomentDelta` 对象来修改显示的日期，而不会影响状态中存储的日期。

#### 增加

```python demo
rx.vstack(
    rx.moment(
        MomentState.date_now,
        add=rx.MomentDelta(years=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        add=rx.MomentDelta(quarters=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        add=rx.MomentDelta(months=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        add=rx.MomentDelta(weeks=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now, add=rx.MomentDelta(days=2), format="YYYY-MM-DD - HH:mm:ss"
    ),
    rx.moment(
        MomentState.date_now,
        add=rx.MomentDelta(hours=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        add=rx.MomentDelta(minutes=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        add=rx.MomentDelta(seconds=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
)
```

#### 减少

```python demo
rx.vstack(
    rx.moment(
        MomentState.date_now,
        subtract=rx.MomentDelta(years=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        subtract=rx.MomentDelta(quarters=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        subtract=rx.MomentDelta(months=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        subtract=rx.MomentDelta(weeks=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        subtract=rx.MomentDelta(days=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        subtract=rx.MomentDelta(hours=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        subtract=rx.MomentDelta(minutes=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
    rx.moment(
        MomentState.date_now,
        subtract=rx.MomentDelta(seconds=2),
        format="YYYY-MM-DD - HH:mm:ss",
    ),
)
```

### 时区

你还可以设置日期在特定时区中显示：

```python demo
rx.vstack(
    rx.moment(MomentState.date_now, tz="America/Los_Angeles"),
    rx.moment(MomentState.date_now, tz="Europe/Paris"),
    rx.moment(MomentState.date_now, tz="Asia/Tokyo"),
)
```

### 客户端定期更新

如果没有向 `rx.moment` 传入日期，它将使用客户端的当前时间。

如果你想每秒更新日期，可以使用 `interval` 属性。

```python demo
rx.moment(interval=1000, format="HH:mm:ss")
```

更好的是，你可以将事件处理程序绑定到 `on_change` 属性，每次日期更新时都会调用它：

```python demo exec
class MomentLiveState(rx.State):
    updating: bool = False

    @rx.event
    def on_update(self, date):
        return rx.toast(f"Date updated: {date}")

    @rx.event
    def set_updating(self, value: bool):
        self.updating = value


def moment_live_example():
    return rx.hstack(
        rx.moment(
            format="HH:mm:ss",
            interval=rx.cond(MomentLiveState.updating, 5000, 0),
            on_change=MomentLiveState.on_update,
        ),
        rx.switch(
            is_checked=MomentLiveState.updating,
            on_change=MomentLiveState.set_updating,
        ),
    )
```
