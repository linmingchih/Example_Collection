---
description: 這些訊息包含via/pin的名稱、歸屬的net-name、padstack的類型，位置XY座標及旋轉角度。
---

# 取得PCB上Via及Pin的相關訊息

{% code lineNumbers="true" %}
```python
import clr, os, sys, System, json
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
data = []

for i in layout.PadstackInstances:
    pad_name = i.GetName()
    cmp_name = i.GetComponent().GetName()
    net_name = i.GetNet().GetName()
    padstacktype = i.GetPadstackDef().GetName()
    _, location, angle = i.GetPositionAndRotationValue()

    if cmp_name:
        pad_name = cmp_name + '-' + pad_name

    data.append((pad_name, net_name, padstacktype, str(location), str(angle)))
with open('d:/demo/pcb.log', 'w') as f:
    json.dump(data, f, indent=4)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (1) (3) (1).png" alt=""><figcaption></figcaption></figure>
