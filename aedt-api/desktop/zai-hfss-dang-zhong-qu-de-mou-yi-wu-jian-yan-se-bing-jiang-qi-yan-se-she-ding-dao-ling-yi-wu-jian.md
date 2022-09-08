---
description: 利用程式取得圓柱體的顏色碼將其轉換成RGB之後設定到方塊物件上。
---

# 在HFSS當中取得某一物件顏色並將其顏色設定到另一物件

{% code lineNumbers="true" %}
```python
import ScriptEnv
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor("3D Modeler")

code = oEditor.GetPropertyValue('Geometry3DAttributeTab', "Cylinder1", 'Color')

AddWarningMessage(str(code))

code = int(code)
R= code % 256
G = ((code - R) % (256**2)) / 256
B = (code - R - 256*G) / 256**2

oEditor.ChangeProperty(
   [
      "NAME:AllTabs",
      [
         "NAME:Geometry3DAttributeTab",
         [
            "NAME:PropServers",
            'Box1'
         ],
         [
            "NAME:ChangedProps",
            [
               "NAME:Color",
               "R:="        , R,
               "G:="        , G,
               "B:="        , B
            ]
         ]
      ]
   ])
```
{% endcode %}
