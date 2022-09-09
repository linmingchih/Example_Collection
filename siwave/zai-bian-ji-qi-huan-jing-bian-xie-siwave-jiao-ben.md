---
description: 在Spyder當中使用win32com可以直接編輯SIwave代碼。可以pip install pypiwin32安裝win32com模組。
---

# 在編輯器環境編寫SIwave腳本

{% code lineNumbers="true" %}
```python
from win32com import client


oApp = client.Dispatch("SIwave.Application.2022.1")
oApp.RestoreWindow()
oDoc = oApp.GetActiveProject()
oDoc.ScrDrawCircle(10, 0, 20, 'METAL-1', 'NET-1', 'mm')
oDoc.ScrSaveProjectAs ('d:/demo/test215.siw')
oApp.Quit()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>
