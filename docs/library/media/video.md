---
components:
  - rx.video
---

# 视频（Video）

```python exec
import reflex as rx
```

视频组件（Video）可以通过传入 src 路径作为参数来显示视频。该路径可以是 assets 文件夹中的本地路径，也可以是外部链接。

```python demo
rx.video(src="https://www.youtube.com/embed/9bZkp7q19f0", width="400px", height="auto")
```

如果我们在 `assets` 文件夹中有一个名为 `test.mp4` 的本地文件，可以设置 `url="/test.mp4"` 来查看该视频。

```md alert info
# 如何让用户上传视频

要让用户向你的应用上传视频，请查看[上传文档](/docs/library/forms/upload)。
```
