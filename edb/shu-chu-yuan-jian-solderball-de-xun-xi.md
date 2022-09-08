---
description: >-
  在不打開AEDT的狀況之下，我們可以透過直接讀取3D Layout的.aedb方式取得版圖相關訊息。這裡示範如何取得某一個IC元件的Solder
  Ball的類別、直徑及高度。
---

# 輸出元件SolderBall的訊息

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

{% code lineNumbers="true" %}
```python
import clr, os, sys, System
AnsysEM_Path = 'C:/Program Files/AnsysEM/AnsysEM20.2/Win64/'
sys.path.append(AnsysEM_Path)
os.environ['PATH'] += ';' + AnsysEM_Path

clr.AddReference('Ansys.Ansoft.Edb')
clr.AddReference('Ansys.Ansoft.SimSetupData')
import Ansys.Ansoft.Edb as edb

edb.Database.SetRunAsStandAlone(True)

DB = edb.Database.Open('D:/demo/test.aedb', False)

cell = list(DB.TopCircuitCells)
layout = cell[0].GetLayout()

u2a5 = edb.Cell.Hierarchy.Component.FindByName(layout, 'U2A5')
u2a5_property = u2a5.GetComponentProperty().GetSolderBallProperty()
print(u2a5_property.GetShape())
print(u2a5_property.GetDiameter())
print(u2a5_property.GetHeight())

comp_names=[]
for c in layout.Groups:
    comp_names.append(c.GetName())
print(comp_names)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>
