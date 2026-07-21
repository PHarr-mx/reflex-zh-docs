
# 身份验证概述

许多应用需要身份验证来管理用户。在 Reflex 中有几种不同的方式来实现这一点：

目前框架核心之外已存在以下解决方案：

1. **本地身份验证（Local Auth）**：使用你自己的数据库：https://github.com/masenf/reflex-local-auth
2. **Google 身份验证（Google Auth）**：使用 Google 登录：https://github.com/masenf/reflex-google-auth
3. **验证码（Captcha）**：生成人类能通过但自动化系统无法通过的测试：https://github.com/masenf/reflex-google-recaptcha-v2
4. **魔法链接身份验证（Magic Link Auth）**：一种无密码登录方法，向用户邮箱发送唯一的、一次性使用的 URL：https://github.com/masenf/reflex-magic-link-auth
5. **Clerk 身份验证（Clerk Auth）**：社区成员封装了该组件并将其接入此应用：https://github.com/TimChild/reflex-clerk-api
6. **Descope 身份验证（Descope Auth）**：使用 Descope 启用身份验证，支持无密码、社交登录、SSO 和 MFA：https://github.com/descope-sample-apps/reflex-descope-auth

如果你正在使用 AI Builder，你也可以使用内置的[身份验证集成](/docs/ai/integrations/overview)，其中包括 Azure Auth、Google Auth、Okta Auth 和 Descope。

## 实现身份验证的指导建议

- 将敏感的用户令牌和信息存储在[仅后端变量（backend-only vars）](/docs/vars/base-vars#backend-only-vars)中。
- 对每个执行已验证操作的事件处理器以及所有访问私有数据的计算变量（computed vars）或加载器事件（loader events），验证用户会话和权限。
- 在前端静态渲染的所有内容（例如，硬编码或在编译时加载到 UI 中的数据）都是公开可用的，即使页面重定向到登录页或使用 `rx.cond` 隐藏内容。
- 只有源自状态（state）的数据才能真正保持私有并受到保护。
- 使用 cookie 或本地存储时，签名的 JWT 可以检测并失效任何本地篡改。

更多身份验证文档即将推出。敬请期待！
