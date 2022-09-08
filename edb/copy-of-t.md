# 輸出path跟polygon的座標點

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
data = {}
type_group = set()
for i in layout.Primitives:
    name = i.GetId()
    net = i.GetNet().GetName()
    layer = i.GetLayer().GetName()
    ptype = i.GetPrimitiveType()
    key = ','.join([str(net), str(name), str(layer), str(ptype)])
    type_group.add(str(ptype))

    data[key]=[]
    for p in i.GetPolygonData().Points:
        _x = p.X.ToDouble()
        _y = p.Y.ToDouble()
        if _x == sys.float_info.max or _y == sys.float_info.max:
            continue
        data[key].append((_x, _y))
    sk = sorted(data.keys())
    newdata = {str(i):data[i] for i in sk}

print(type_group)
with open('d:/demo/pcb.log', 'w') as f:
    json.dump(newdata, f, indent=4)
```
{% endcode %}
