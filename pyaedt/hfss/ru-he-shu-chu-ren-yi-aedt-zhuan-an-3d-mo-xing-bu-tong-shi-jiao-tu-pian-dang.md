---
description: 範例碼如下(當中export3DModel在0.4.26版輸出.obj仍有問題，需手動修改)：
---

# 如何輸出任一AEDT專案3D模型不同視角圖片檔



{% code lineNumbers="true" %}
```python
import os
from pyaedt import Hfss
from pyaedt.generic.plot import ModelPlotter

hfss = Hfss('d:/demo/RADHAZ_MIL-STD-461C_ICNIRP.aedt', 'HERP', non_graphical=True)

data  = {}
for i in hfss.modeler.object_list:
    key = (i.color, i.transparency) 
    if key in data:
        data[key].append(i)
    else:
        data[key] = [i]

cad = {}
for key, objs in data.items():
    filename = objs[0].name
    hfss.export3DModel(filename, hfss.temp_directory, '.obj', object_list=[i.name for i in objs])
    cad[key] = os.path.join(hfss.temp_directory, filename + '.obj')

hfss.close_desktop()

model = ModelPlotter()
model.bounding_box = False
model.show_legend = False
model.show_grid = True
model.show_axes = False
model.off_screen = True
model.background_color = (240,240,240)
model.zoom = 1

for (color, transparency), cad_path in cad.items():
    model.add_object(cad_path, cad_color=color, opacity=1-transparency)

for angle in range(0, 360, 30):
    model.set_orientation('yz', 0, angle, 30)
    model.plot('d:/demo2/view_{}.png'.format(angle))
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (2) (5).png" alt=""><figcaption></figcaption></figure>
