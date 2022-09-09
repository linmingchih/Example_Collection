---
description: 在IDE編寫AEDT API腳本
---

# AEDT API



{% code lineNumbers="true" %}
```python
from win32com import client

oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.1")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()
oProject = oDesktop.NewProject()
oDesign = oProject.InsertDesign("HFSS", "HFSSDesign1", "HFSS Terminal Network", "")
oEditor = oDesign.SetActiveEditor("3D Modeler")
oEditor.CreateCylinder(
   [
      "NAME:CylinderParameters",
      "XCenter:="       , "0mm",
      "YCenter:="       , "-0.3mm",
      "ZCenter:="       , "0mm",
      "Radius:="    , "0.282842712474619mm",
      "Height:="    , "0.8mm",
      "WhichAxis:="     , "Z",
      "NumSides:="      , "0"
   ], 
   [
      "NAME:Attributes",
      "Name:="      , "Cylinder1",
      "Flags:="     , "",
      "Color:="     , "(143 175 143)",
      "Transparency:="   , 0,
      "PartCoordinateSystem:=", "Global",
      "UDMId:="     , "",
      "MaterialValue:="  , "\"vacuum\"",
      "SurfaceMaterialValue:=", "\"\"",
      "SolveInside:="       , True,
      "ShellElement:="   , False,
      "ShellElementThickness:=", "0mm",
      "IsMaterialEditable:=" , True,
      "UseMaterialAppearance:=", False,
      "IsLightweight:="  , False
   ])
#oDesktop.QuitApplication()
```
{% endcode %}

<figure><img src=".gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

