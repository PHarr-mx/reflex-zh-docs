---
tags: DevTools
description: 将 Reflex Build 连接到 GitHub，以版本化你的应用、在本地同步代码，并恢复到以前的检查点。
---

# 连接到 GitHub

```python exec
import reflex as rx
```

将你的应用连接到 GitHub 可以为其提供版本历史记录，并让你在本地编辑代码。每次同步都是普通 Git 仓库中的一个提交。

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/connecting_to_github.avif",
        alt="Connecting a Reflex AI Builder app to GitHub",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
)
```

GitHub 集成允许你：

- 将应用进度保存为你控制的提交
- 在本地处理代码并将本地更改推送回 Reflex Build
- 将其他地方所做的更改拉回编辑器
- 恢复到应用的任何以前版本

## 连接如何工作

Reflex Build 通过使用 OAuth 的 `reflex-build` GitHub App 连接到 GitHub。第一次连接时，GitHub 会提示你安装该应用并授权访问。然后它将你的 GitHub 账户链接到 Reflex Build，后者存储加密的访问令牌并使用它为你创建仓库和推送提交。

推送、拉取和恢复使用 Reflex Build 按需请求的短期 GitHub App 安装令牌。你的个人 OAuth 令牌用于识别你的身份并刷新访问权限。两者在静态时都经过加密。

## 团队和多用户访问

**每个用户连接自己的 GitHub 账户。** 连接按用户存储；没有共享或组织级别的令牌。如果一个队友连接了 GitHub，另一个想要推送或拉取的队友必须运行相同的连接流程并使用自己的账户授权 `reflex-build` 应用。

提交归属于进行提交的 GitHub 用户，每个人对仓库的访问遵循其自己的 GitHub 权限。

GitHub App 安装与按用户授权是分开的。组织所有者可以安装一次 `reflex-build` 应用并选择它可以访问哪些仓库，这控制了从 Reflex Build 可以访问什么。每个用户仍然需要授权一次应用以链接自己的账户，然后才能推送或拉取。

## 仓库创建位置

当你将 Build 应用连接到 GitHub 时，Reflex Build 会为其创建一个 Git 仓库：

- 如果 `reflex-build` 应用安装在 **GitHub 组织**上，仓库将在该组织内创建。
- 否则，仓库将在你的**个人 GitHub 账户**下创建。

新仓库使用 `main` 分支并且是私有的，除非你在连接时将其设为公开。

## 推送和拉取更改

编辑器中的 GitHub 弹出窗口支持双向同步：

- **Push**（推送）：提交应用的当前状态并将其推送到 GitHub。
- **Pull**（拉取）：从 GitHub 获取最新提交并更新编辑器中的文件。
- **Switch branch**（切换分支）：检出不同的分支并将其文件同步到编辑器中。

你也可以克隆仓库，在本地编辑，然后将更改推送回去。

## GitHub 提交历史

提交历史是查看你对应用所做更改的好方法。你也可以从这里恢复到应用的以前版本。

```python eval
rx.el.div(
    rx.image(
        src="https://web.reflex-assets.dev/ai_builder/github_commit_history.avif",
        alt="GitHub commit history for a Reflex AI Builder app",
        class_name="rounded-md h-auto",
        border=f"0.81px solid {rx.color('slate', 5)}",
    ),
)
```

恢复会将应用的文件重置为较早的提交。

## 其他 Git 提供商

要连接托管在 GitHub 以外的仓库，如 GitLab、Bitbucket 或自托管 Git 服务器，请改用通用 Git 连接。详情请参阅[连接到 Git 提供商](/docs/ai/features/connect-to-git-providers/)。

## 要求

Git 集成在包含 Git 连接功能的计划中可用。如果你的计划不包含它，连接时将提示你升级。
