# reflex-docs-bundle

Reflex 文档打包为可重新分发的 Python wheel。按需构建；
不发布到 PyPI。

```python
from reflex_docs_bundle import DOCS_DIR, get_doc, list_docs

list_docs()  # ["vars/custom_vars.md", ...]
get_doc("vars/custom_vars.md")  # markdown 源码作为字符串
DOCS_DIR / "vars" / "custom_vars.md"
```
