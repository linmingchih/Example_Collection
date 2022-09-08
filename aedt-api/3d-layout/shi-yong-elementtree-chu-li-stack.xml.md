---
description: 以下包含xml建立，讀取，改寫，存檔等動作：
---

# 使用ElementTree處理stack.xml

{% code lineNumbers="true" %}
```python
import xml.etree.ElementTree as ET
tree = ET.parse('d:/demo/stackup1.xml')
x = tree.getroot()


y = x.find('./Stackup/ELayers/Layers')
for child in y:
    print(child.get('Elevation'))
    child.set('UnionPrimitives', 'true')
    child.set('Material', 'Copper')
tree.write('d:/demo/stachkup2.xml')
```
{% endcode %}
