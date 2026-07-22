---
title: End-to-End Testing
---

_reflex-enterprise v0.9.2 新增。_

# 端到端测试（End-to-End Testing）

reflex-enterprise 提供了一个 pytest 插件，它使用 `reflex run` 启动现有的 Reflex 应用并将实时 URL 传递给测试。测试通过浏览器自动化（如 [pytest-playwright](https://playwright.dev/python/docs/test-runners)）驱动运行中的应用——包括前端和后端。

该插件管理应用进程并返回其 URL。浏览器自动化由测试套件选择和控制。

## 安装

```bash
uv add "reflex-enterprise[testing]>=0.9.2"
uv add pytest-playwright         # 你选择的浏览器驱动
uv run playwright install chromium
```

`[testing]` extra 会安装 `pytest`，但不包含浏览器驱动。下面的示例使用 `pytest-playwright`，但任何能驱动实时 URL 的浏览器自动化框架都兼容。

该 fixture 通过 `pytest11` 入口点注册，因此无需 `conftest.py` 配置。

## 快速开始

将测试放在包含 `rxconfig.py` 的目录或其子目录中的任何位置：

```text
my_app/
  rxconfig.py
  my_app/
    my_app.py
  tests/
    test_homepage.py
```

```python
from playwright.sync_api import Page, expect

from reflex_enterprise.testing import ReflexApp


def test_homepage(reflex_app: ReflexApp, page: Page):
    page.goto(reflex_app.url)
    expect(page.get_by_role("heading", name="Welcome")).to_be_visible()
```

运行：

```bash
uv run pytest
```

第一个请求 `reflex_app` 的测试会启动应用。冷启动会编译前端；后续运行会重用缓存的工作目录并快速启动。运行中的应用由会话中的所有测试共享，并在会话结束时关闭。

```md alert warning
# 共享应用进程，隔离每个测试的状态。

应用进程在测试用例之间保持存活，但状态不会在它们之间传递。每个浏览器上下文使用自己的 `client_token` 连接，而 Reflex 通过该令牌键控后端状态，因此每个打开新 `page`（函数作用域的 `pytest-playwright` fixture）的测试都会与一个干净的、独立的状态实例通信。

这意味着测试永远不会看到彼此的状态，但这也意味着测试在 UI 中所做的任何操作——登录、导航、打开对话框——都不会持续到下一个测试。每个测试都需要重现所需的起始条件（参阅[重用设置步骤](#reusing-setup-steps)）。在多个测试间重用同一个 `page`/上下文会共享 `client_token` 并在它们之间泄漏状态；请保持每个测试一个上下文。
```

## 工作原理

- **发现** — 被测应用通过从测试文件向上遍历到最近的 `rxconfig.py` 来找到。该目录即为应用根目录。
- **隔离** — 应用使用重定位的 `REFLEX_WEB_WORKDIR` / `REFLEX_STATES_WORKDIR` 运行，遥测被禁用，工作目录位于代码仓库之外，因此编译的前端和状态产物永远不会落入工作树中。`reflex run` 本身可能仍会在 `rxconfig.py` 旁边写入 `reflex.lock`，对于纯测试应用你可以将其加入 `.gitignore`。
- **重用与限制** — 会话作用域的管理器按应用根目录和运行模式缓存运行中的应用。它会重用健康的运行中应用，最多保持 `max_apps` 个存活，并淘汰最近最少使用的以为不同的应用腾出空间。`reflex_app` fixture 是函数作用域的，但从管理器借用，因此应用的生命周期超过单个测试。
- **端口** — `reflex run` 自行选择端口（从 3000/8000 自动递增），插件从其输出中读取实际 URL。
- **就绪** — 只有当前端和后端都接受连接时 fixture 才会被满足。如果任何一方无法启动，fixture 会在 setup 时抛出异常，消息中包含捕获的 `reflex run` 输出，测试被报告为错误。相同的契约管理重用：如果缓存的应用的前端或后端不再可达，它会被拆除并重启后再交给测试。

## `reflex_app` fixture

`reflex_app` fixture 返回一个 `ReflexApp` 对象，具有小而稳定的接口：

| 属性 | 描述 |
|---|---|
| `reflex_app.url` | 实时前端 URL（无尾部斜杠）——传递给 `page.goto(...)`。 |
| `reflex_app.backend_url` | 实时后端 URL（如果可用）。 |
| `reflex_app.app_root` | 解析后的应用根目录（包含 `rxconfig.py` 的目录）。 |
| `reflex_app.logs()` | 捕获的 `reflex run` 输出（stdout 和 stderr 合并，最近的行）。 |

## 重用设置步骤

由于每个测试都从干净的状态开始（参阅上面的警告），测试依赖的任何起始条件——已登录的会话、已填充的表单、已打开的对框——都必须为该测试重新建立。将这些步骤提取到函数作用域的 fixture 中，这样每个测试体都可以假设应用已经处于它关心的状态。

例如，一个通过点击菜单打开设置模态框的 fixture，使测试以模态框已打开的状态开始：

```python
import pytest
from playwright.sync_api import Page, expect

from reflex_enterprise.testing import ReflexApp


@pytest.fixture
def settings_modal(reflex_app: ReflexApp, page: Page) -> Page:
    """Open the settings modal and hand the test a page where it is visible."""
    page.goto(reflex_app.url)
    page.get_by_role("button", name="Menu").click()
    page.get_by_role("menuitem", name="Settings").click()
    expect(page.get_by_role("dialog", name="Settings")).to_be_visible()
    return page


def test_change_theme(settings_modal: Page):
    # 模态框已经打开；直接进行断言。
    settings_modal.get_by_label("Dark mode").check()
    expect(settings_modal.get_by_label("Dark mode")).to_be_checked()
```

该 fixture 依赖于 `reflex_app` 和 `page`，两者都是函数作用域的，因此它针对每个测试自己的 `client_token` 运行一次——设置对每个测试重复执行，但状态永远不会在它们之间泄漏。这样的 fixture 可以组合：`logged_in` fixture 可以从登录页面导航，而 `settings_modal` 可以依赖它以已认证用户身份打开模态框。

## 调试失败

前端断言可能因服务端问题而失败——启动警告、编译错误、事件期间的后端回溯。插件在三个位置显示服务器输出：

- **当测试失败时**，测试借用的每个应用的捕获 `reflex run` 输出会作为附加部分附在报告中，与失败信息一起打印，类似于捕获的 stdout：

  ```text
  ------- captured `reflex run` output (my_app) -------
  ...
  App running at: http://localhost:3000
  ERROR:  Traceback (most recent call last): ...
  ```

- **当应用无法启动时**，fixture setup 期间抛出的 `ReflexAppStartError` 嵌入了完整的捕获输出，因此原因（错误的 rxconfig、缺少依赖、端口冲突）是错误的一部分。

- **编程访问** — `reflex_app.logs()` 返回相同的捕获输出，用于自定义断言或日志记录。

## 配置

每个选项都可以通过命令行标志、pytest ini 选项或环境变量设置。优先级为 CLI > 环境变量 > ini > 默认值。

| 设置 | CLI | ini | 环境变量 | 默认值 |
|---|---|---|---|---|
| 最大运行应用数 | `--reflex-max-apps` | `reflex_max_apps` | `REFLEX_TEST_MAX_APPS` | `1` |
| 运行模式 | `--reflex-run-mode` | `reflex_run_mode` | `REFLEX_TEST_RUN_MODE` | `dev` |
| 工作目录策略 | `--reflex-workdir-strategy` | `reflex_workdir_strategy` | `REFLEX_TEST_WORKDIR_STRATEGY` | `persistent` |
| 工作目录根路径 | `--reflex-workdir-root` | `reflex_workdir_root` | `REFLEX_TEST_WORKDIR_ROOT` | `<tmp>/reflex-enterprise-testing` |
| 启动超时（秒） | `--reflex-start-timeout` | `reflex_start_timeout` | `REFLEX_TEST_START_TIMEOUT` | `300` |
| 额外 `reflex run` 参数 | `--reflex-run-arg`（可重复） | `reflex_run_args` | `REFLEX_TEST_RUN_ARGS` | *（无）* |
| 额外子进程环境变量 | `--reflex-run-env`（可重复） | `reflex_run_env` | `REFLEX_TEST_RUN_ENV` | *（无）* |
| 共享/隔离 `REFLEX_DIR` | `--reflex-share-reflex-dir` / `--reflex-isolate-reflex-dir` | `reflex_share_reflex_dir` | `REFLEX_TEST_SHARE_REFLEX_DIR` | 共享 |

- **`run_mode`** — `reflex run` 的环境模式：`dev`（默认）、`prod`，或逗号分隔的列表（`--reflex-run-mode=dev,prod`）。使用多个模式时，每个使用 `reflex_app` 的测试都会被参数化为每种模式运行一次，测试被分组以便一种模式的所有测试在下一模式开始前运行。同一应用的 dev 和 prod 实例分别缓存（每个都计入 `max_apps`），使用独立的持久工作目录，因此它们的构建产物永远不会混合。测试可以通过会话作用域的 `reflex_run_mode` fixture 读取当前模式（`"dev"` 或 `"prod"`）。在 `prod` 模式下，reflex 在单个地址上提供前端和后端服务，因此 `reflex_app.backend_url` 会回退到 `reflex_app.url`。
- **`workdir_strategy`** — `persistent` 在会话间重用按应用、按运行模式的缓存目录（热启动，大约比冷启动快 6 倍）；`tmp` 每次启动使用新的临时目录（干净但总是冷启动）。
- **`reflex_run_args`** — 附加到 `reflex run` 的额外 CLI 参数。`--frontend-only` / `--backend-only` 会被立即拒绝，因为部分运行永远无法满足就绪检查。插件会在这些参数之后追加自己的 `--env <mode>`，因此这里的 `--env` 会被覆盖——请改用 `--reflex-run-mode` 设置模式。
- **共享/隔离 `REFLEX_DIR`** — 默认情况下，全局 bun/node/reflex 依赖与主机共享以避免缓慢的重新下载。传递 `--reflex-isolate-reflex-dir` 进行完全密封（较慢）的运行，或 `--reflex-share-reflex-dir` 强制共享以覆盖 ini/环境变量设置（CLI 始终优先）。

`pyproject.toml` 示例：

```toml
[tool.pytest.ini_options]
reflex_max_apps = "2"
reflex_workdir_strategy = "persistent"
reflex_start_timeout = "180"
```

### 从 fixture 调整设置

对于静态选项无法表达的内容，会话作用域的 `reflex_app_manager` fixture 返回 `AppManager`，其 `settings` 属性（`ReflexAppTestSettings`）是受支持的集成点——从你自己的 fixture 中修改它。设置在应用启动时被消费，因此在第一次使用 `reflex_app` 之前应用更改，例如在 autouse 会话 fixture 中：

```python
import pytest

from reflex_enterprise.testing import AppManager


@pytest.fixture(scope="session", autouse=True)
def _reflex_settings(reflex_app_manager: AppManager) -> None:
    reflex_app_manager.settings.start_timeout = 120.0
    reflex_app_manager.settings.extra_env = {"MY_FEATURE_FLAG": "1"}
```

`ReflexAppTestSettings` 是一个数据类，保存上表中每个选项的解析值（CLI/环境变量/ini 已应用），未设置时使用相同的默认值：

```python
@dataclass
class ReflexAppTestSettings:
    max_apps: int = 1
    run_modes: tuple[str, ...] = ("dev",)
    workdir_strategy: Literal["persistent", "tmp"] = "persistent"
    workdir_root: Path = ...
    start_timeout: float = 300.0  # 秒
    reflex_run_args: tuple[str, ...] = ()
    extra_env: Mapping[str, str] = field(default_factory=dict)
    share_reflex_dir: bool = True
```

`AppManager`、`ReflexApp` 和 `ReflexAppTestSettings` 都可以从 `reflex_enterprise.testing` 导入，用于上述 fixture 的正确类型标注。

## 测量覆盖率

由于应用代码在 fixture 启动的 `reflex run` 子进程中执行，pytest 本身可能不会直接导入或执行任何应用代码。要记录覆盖率，需要启用 coverage.py 的 `subprocess` 补丁来测量子进程数据并将其合并到最终报告中。

在 `pyproject.toml` 中配置——对于名为 `my_app` 的应用：

```toml
[tool.coverage.run]
# 显式测量应用代码，因为它可能不会在主 pytest 进程中直接导入。
source = ["my_app"]
# 子进程会写入单独的 .coverage.* 文件，由 pytest-cov 合并。
parallel = true
patch = [
  "subprocess",  # 将 coverage 注入 `reflex run` 和 worker
  "_exit",       # 在 os._exit() 时刷新 coverage 数据
]
# 测试框架使用 SIGTERM 停止应用。
sigterm = true
# 如果测试用例从未直接导入 `my_app`，忽略警告。
disable_warnings = ["no-data-collected"]
```

然后使用 [pytest-cov](https://pytest-cov.readthedocs.io/) 运行：

```bash
uv add "pytest-cov>=7.1"
uv run pytest --cov
```

每个应用子进程写入自己的 `.coverage.*` 数据文件；pytest-cov 在会话结束时合并它们并报告应用包的覆盖率。

## 注意事项

- **pytest-xdist** — 每个 worker 进程获得自己的管理器，因此运行应用的有效上限为 `max_apps * num_workers`。
- **并发 `pytest` 运行** — 在 `persistent` 模式下，每个应用的工作目录带有 pid 锁文件。如果另一个 `pytest` 进程已在使用某应用的工作目录，第二次运行会透明地回退到自己的临时工作目录，这样两者永远不会重新编译或覆盖同一个 `.web`。过期的锁（来自崩溃的运行）会被自动回收。
- **`frontend_path`** — `reflex_app.url` 会尊重配置的 `frontend_path`（从 reflex 自身的 "App running at:" 行读取）。
- 插件逐个启动应用并等待就绪，这是 `reflex run` 的端口自动递增在多个应用运行时正确工作所必需的。
