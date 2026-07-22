# 遥测（Telemetry）

Reflex 收集匿名使用统计数据——编译时使用的组件和内置功能的计数，以及 Reflex、Python 和主机操作系统的版本——以便我们优先修复 bug 并指导框架的未来方向。我们**不**收集你的源文件内容、状态或你的应用在运行时处理的任何数据。

有关完整详情，请参阅我们的[隐私政策](https://build.reflex.dev/privacy-policy)和[服务条款](https://build.reflex.dev/terms-of-use)。

如果你登录 [Reflex Cloud](/docs/hosting/deploy-quick-start/)，你的 Cloud 账户标识符将与该机器后续的使用事件关联。

## 选择退出

在你的 `rxconfig.py` 中：

```python
import reflex as rx

config = rx.Config(
    app_name="my_app",
    telemetry_enabled=False,
)
```

或通过环境变量：

```bash
REFLEX_TELEMETRY_ENABLED=false reflex init --template blank
```

环境变量是 reflex 命令在应用目录上下文之外执行时唯一支持的选择退出方法，例如 `reflex login` 和 `reflex init`。
