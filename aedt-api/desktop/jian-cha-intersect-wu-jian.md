---
description: 使用3D Modeler的HFSS, Q3D, Maxwell, Icepak, Mechanical等適用
---

# 檢查 intersect物件

{% code lineNumbers="true" %}
```python
import re

from win32com import client
oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()

oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()

oDesktop.ClearMessages("","",2)
oDesign.ValidateDesign()
x = oDesktop.GetMessages(oProject.GetName(), oDesign.GetName(), 2)
oDesktop.ClearMessages("","",2)

data = re.findall('Parts\s"(.*?)"\sand\s"(.*?)"\sintersect', ''.join(x))
print(data)
```
{% endcode %}

{% code overflow="wrap" %}
```
[('Box2', 'Cylinder4'), ('Box2', 'Sphere2'), ('Box1', 'Cylinder1'), ('Cylinder3', 'Cylinder2'), ('Sphere2', 'Sphere1')]
```
{% endcode %}
