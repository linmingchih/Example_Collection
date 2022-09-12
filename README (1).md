---
description: 在IDE編寫AEDT API腳本
cover: >-
  https://previews.123rf.com/images/davidoff205020/davidoff2050202012/davidoff205020201200326/160522284-background-of-multicolored-binary-codes-animation-beautiful-background-with-multi-colored-numbers-of.jpg
coverY: 0
---

# AEDT API

開發者可以在IDE當中開發API Script。加入#1-#5即可連結開啟中的AEDT。

{% code lineNumbers="true" %}
```python
from win32com import client

oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.1")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()
```
{% endcode %}

開發完成之後的script如果要在AEDT當中執行須將#1-#5刪除或標示為註解，使其無效。
