---
description: >-
  如果沒有封閉的曲線不是一個polyline，而是多個分離的線段(line)，那麼可以先執行Draw > Stitch
  Lines將其轉換成單一Polyline，再執行下面腳本。形成封閉polygon之後，接下來便可以進行電磁模擬。
---

# 將沒有封閉的Polyline封閉成Polygon

{% code lineNumbers="true" %}
```python
oDesktop.ClearMessages("", "", 2)

oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.GetActiveEditor()

selected = oEditor.GetSelections()

for i in selected:
    data = []
    placement_layer = oEditor.GetPropertyValue('BaseElementTab', i, 'PlacementLayer')

    for j in oEditor.GetProperties('BaseElementTab', i):
        if j.startswith('Pt'):
            location = oEditor.GetPropertyValue('BaseElementTab', i, j)
            x, y = [float(k) for k in location.split(',')]
            data += ["x:=", x, "y:=", y]

    name = oEditor.CreatePolygon(
        [
            "NAME:Contents",
            "polyGeometry:=",
            ["Name:=", "poly_1002", "LayerName:=", placement_layer, "lw:=", "0", "n:=", len(data), "U:=", "mm", ] + data
        ])
    AddWarningMessage('{} is created!'.format(name))

oEditor.Delete(selected)
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>
