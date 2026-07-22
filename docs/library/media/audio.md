---
components:
  - rx.audio
---

# 音频（Audio）

```python exec
import reflex as rx
```

音频组件（Audio）可以通过传入 src 路径作为参数来显示音频。该路径可以是 assets 文件夹中的本地路径，也可以是外部链接。

```python demo
rx.audio(
    src="https://www.learningcontainer.com/wp-content/uploads/2020/02/Kalimba.mp3",
    width="400px",
    height="32px",
)
```

如果我们在 `assets` 文件夹中有一个名为 `test.mp3` 的本地文件，可以设置 `src="/test.mp3"` 来查看该音频文件。

```md alert info
# 如何让用户上传音频文件

要让用户向你的应用上传音频文件，请查看[上传文档](/docs/library/forms/upload)。
```
