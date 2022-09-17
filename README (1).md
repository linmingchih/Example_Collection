---
description: 在IDE編寫AEDT API腳本
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

舉例來說，使用者在開啟HFSS之後，切回到Spyder IDE環境，輸入

{% code lineNumbers="true" %}
```python
from win32com import client
oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()

oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
print(oDesign.GetName())
oEditor = oDesign.SetActiveEditor("3D Modeler")
# if the active design is 3D Layout, use GetActiveEditor() to get oEditor, e.g.
# oEditor = oDesign.GetActiveEditor()
```
{% endcode %}

Console會輸出

```
HFSSDesign1
```

開發完成之後的script如果要在AEDT當中執行須將#1-#5刪除或標示為註解，使其無效。
