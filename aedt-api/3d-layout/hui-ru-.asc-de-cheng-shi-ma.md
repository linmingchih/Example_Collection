---
description: PADS .asc轉檔程式已沒有更新，不建議使用
---

# 匯入.asc的程式碼

{% code lineNumbers="true" %}
```python
import os

os.environ['path'] = 'C:\Program Files\AnsysEM\AnsysEM21.2\Win64'

os.system('padstoanf.exe d:/demo/41266AAB-JOB.asc')
os.system('anfv4toanfv2.exe d:/demo/PadsLayout.anf')

oTool = oDesktop.GetTool("ImportExport")
oTool.ImportANFV2("D:/demo/PadsLayout_V2.anf", "D:/temp/PadsLayout_V21.aedb", "", "")
```
{% endcode %}
