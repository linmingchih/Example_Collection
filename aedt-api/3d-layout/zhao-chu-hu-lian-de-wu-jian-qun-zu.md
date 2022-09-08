---
description: 分層找出物理上互連的物件群組，針對沒有netname的物件也可以使用。這裡用到遞迴的函數。
---

# 找出互連的物件群組

{% code lineNumbers="true" %}
```python
import json
import ScriptEnv
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.GetActiveEditor()

objdic = {}
for layer in oEditor.GetStackupLayerNames():
    objdic[layer] = oEditor.FilterObjectList('Type', '*', oEditor.FindObjects('Layer', layer))

result = {}
for layer in objdic:
    for obj in objdic[layer]:
        poly = oEditor.GetPolygon(obj)
        x = oEditor.FindObjectsByPolygon(poly, layer)
        if len(x) > 1:
            result[obj] = x

def getConnect(key, g):
    for i in result[key]:
        if i not in g:
            g.append(i)
            getConnect(i, g)
        else:
            continue
    return g

group = []
for obj in result:
    x = getConnect(obj, [])
    if sorted(x) not in group:
        group.append(sorted(x))

with open('d:/demo/connect.json', 'w') as f:
    json.dump(group, f, indent=4)
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>
