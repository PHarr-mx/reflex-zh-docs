```python exec
import reflex as rx
```

# 项目

项目是应用程序（应用/网站）的集合。

每个项目都有自己的计费页面，管理员可以访问。



## 添加团队成员

要查看项目的团队成员，点击项目页面 Cloud UI 中的 `Members` 选项卡。

如果你是用户，你可以创建、部署和删除应用，但你没有从该项目添加或删除用户的权限。你必须是管理员才能做到这一点。

作为管理员，你将在屏幕右上角看到一个 `Add user` 按钮，如下图所示。点击此按钮将允许你向项目添加用户。你需要输入你想添加的用户的电子邮件地址。

```python eval
rx.image(
    src="https://web.reflex-assets.dev/other/hosting_adding_team_members.webp",
    alt="Adding team members to Reflex Cloud",
)
```

```python eval
rx.box(height="20px")
```

```md alert warning
# 目前，用户必须先登录一次才能被添加到项目中。
目前用户必须登录才能被添加到项目中。将来将自动发送电子邮件邀请以添加从未登录过的新用户。
```


## 其他项目设置

点击项目页面 Cloud UI 中的 `Settings` 选项卡允许用户更改`项目名称`、查看`项目 ID`，以及如果项目不是你的默认项目，则删除项目。
