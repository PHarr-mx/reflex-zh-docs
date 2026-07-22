# 单端口代理（Single Port Proxy）

通过将后端代理到前端端口来启用单端口部署。

## 配置

```python
import reflex_enterprise as rxe

config = rxe.Config(
    use_single_port=True,
)
```

这允许你的应用在单个端口上运行，适用于只能暴露一个端口的部署场景。
