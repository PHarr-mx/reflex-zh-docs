---
title: Deploying to Production
---

_reflex-enterprise v0.9.1 新增。_

# 部署到生产环境（Deploying to Production）

[快速开始](/docs/enterprise/auth/overview/)在 `localhost` 上开箱即用，但将同一个应用迁移到针对真实身份提供商（IdP）的实际部署环境有三个要求：通过 HTTPS 提供应用、在 IdP 中注册确切的回调 URL，以及在反向代理后正确报告公开源。

## HTTPS 是必需的

所有四个认证 Cookie（访问令牌、ID 令牌、刷新令牌和已授予的作用域）都以 `Secure; SameSite=Strict; HttpOnly` 属性签发。这些属性是固定的，**不可配置**。由于浏览器会丢弃通过纯 HTTP 发送的 `Secure` Cookie，在任何非 localhost 部署中，应用**必须**通过 HTTPS 提供给浏览器。本地 `reflex run` 无需 TLS 即可工作，仅因为浏览器将 `localhost` 排除在 `Secure` 规则之外。

```md alert warning
# 症状：看似登录循环
如果应用通过纯 HTTP 在非 localhost 主机上提供服务，IdP 到 `/callback` 的重定向会完成，但浏览器会丢弃 `Secure` 认证 Cookie。用户未通过认证，会被送回 `/login`。请通过 HTTPS 提供应用。
```

在 TLS 终止反向代理后面，在代理处终止 TLS，并确保面向浏览器的源是 `https`。浏览器根据浏览器可见的源来强制执行 `Secure`。

## 注册回调 URL

OAuth 重定向 URI 在运行时从浏览器可见的页面 URL 构建，路径替换为插件的 `auth_callback_endpoint`（默认为 `/callback`）。它**不是**配置值：其协议、主机和端口跟随请求源。这是[概述](/docs/enterprise/auth/overview/)中第 3 步的具体版本。

在 IdP 的客户端设置中，将**完整的**回调 URL（包括协议、主机、端口和路径）注册为允许的重定向 URI，并注册你实际使用的每个源：

```bash
http://localhost:3000/callback      # 开发环境
https://your-app.com/callback       # 生产环境
```

与已注册值不匹配的重定向 URI 会产生 IdP 最常见的错误 `redirect_uri_mismatch`。

```md alert info
# 在 TLS 终止代理后面
确保应用通过代理的 forwarded-proto 处理将请求识别为 `https`。否则它会构建一个 `http://...` 重定向 URI，与已注册的 `https://...` 值不匹配。
```

## 反向代理后 / 规模化部署

认证模型的两个属性决定了它在代理后或多个副本间的行为。

**公开源必须正确。** 重定向 URI 和登录后的索引 URI 都从浏览器可见的请求源派生。反向代理必须转发真实的公开协议和主机。如果代理报告 `http` 或内部主机名，运行时重定向 URI 将与在 IdP 注册的值不匹配。后端（`api_url`）也必须从浏览器可达：登录后客户端会向 `/_reflex/cookies/sync` 发起带凭证的请求以持久化认证 Cookie。

**无需粘性会话。** 认证密钥存储在客户端 HTTP Cookie 中。LocalStorage 标志通知其他标签页重新拉取 Cookie。没有服务器端会话存储，因此多个后端副本可以在负载均衡器后运行而无需粘性会话配置。为每个副本提供相同的 OIDC 客户端配置（相同的 `OIDC_*` / `{PROVIDER}_*` 环境变量）。

## 连接真实身份提供商

每个提供商都从其颁发者的 `.well-known/openid-configuration` 发现，始终运行带 PKCE（`S256`）的授权码流程，默认请求 `openid email profile`。由于 `client_secret` 是可选的，每个 IdP 客户端可以是**公开的**（仅 PKCE，无密钥）或**机密的**（PKCE 加密钥）。每个 IdP 的机制相同；只有颁发者 URL 不同。

以 Google 为例：

1. 在 Google Cloud 控制台中，创建一个类型为 **Web 应用**的 **OAuth 2.0 客户端 ID**。
2. 将你的回调 URL（参阅[注册回调 URL](#registering-the-callback-url)）添加为**已授权的重定向 URI**，例如 `https://your-app.com/callback`。
3. 配置应用。由于 Google 会签发客户端密钥，通过设置所有三个变量以机密模式运行客户端（省略 `OIDC_CLIENT_SECRET` 则以 PKCE 公开模式运行）：

```bash
OIDC_ISSUER_URI=https://accounts.google.com
OIDC_CLIENT_ID=your-client-id.apps.googleusercontent.com
OIDC_CLIENT_SECRET=your-client-secret
```

4. 要接收用于授权检查的组声明，通过 `extra_scopes` 添加相关作用域（参阅[提供商](/docs/enterprise/auth/providers/#scopes-and-refresh-tokens)）。

其他 IdP 遵循相同的模式，只是使用各自的颁发者 URL：

| IdP | `OIDC_ISSUER_URI` 格式 |
| --- | --- |
| Google | `https://accounts.google.com` |
| Azure AD | `https://login.microsoftonline.com/<tenant>/v2.0` |
| Auth0 | `https://<tenant>.auth0.com` |
| Okta | `https://<org>.okta.com` |

## 故障排除

| 症状 | 原因 | 修复方法 |
| --- | --- | --- |
| `redirect_uri_mismatch`（来自 IdP） | 重定向 URI 从请求协议 + 主机 + `auth_callback_endpoint` 派生；在 IdP 注册的值与之不匹配。 | 注册确切的回调 URL；在代理后面，确保应用看到公开的协议和主机。 |
| `invalid_client`（来自 IdP 的令牌交换） | IdP 客户端是机密类型，但 `OIDC_CLIENT_SECRET` 未设置。它默认为空，且不会在本地启动时报错。 | 设置客户端密钥，或将 IdP 客户端切换为公开 / PKCE 模式。 |
| 已登录但被弹回 `/login`（登录循环） | `Secure; SameSite=Strict` Cookie 在非 localhost 主机的纯 HTTP 下被丢弃。 | 通过 HTTPS 提供应用（`localhost` 除外）。 |
| 启动时 `ConfigError` | 使用了普通的 `rx.App()` 配合插件，或者两个或更多提供商都回退到共享的 `OIDC_*` 配置来获取颁发者或客户端 ID。 | 使用 `rxe.App()`；对于多提供商，设置提供商特定的 `{PROVIDER}_*` 变量（参阅[提供商](/docs/enterprise/auth/providers/#multiple-providers)）。 |

## 相关内容

- [认证概述](/docs/enterprise/auth/overview/)：快速开始和登录流程。
- [默认安全](/docs/enterprise/auth/secure-by-default/)：执行机制和授权检查。
- [提供商](/docs/enterprise/auth/providers/)：提供商配置、环境变量、作用域和多提供商配置。
- [自定义页面](/docs/enterprise/auth/custom-pages/)：自定义登录、回调、退出登录和禁止访问页面构建器。
- [测试](/docs/enterprise/auth/testing/)：受保护界面和模拟 IdP 测试。
