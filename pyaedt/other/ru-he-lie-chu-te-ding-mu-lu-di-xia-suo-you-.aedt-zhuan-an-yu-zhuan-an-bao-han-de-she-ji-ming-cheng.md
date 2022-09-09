---
description: >-
  當累積的專案數量很多時，想要查找某個設計是放在哪一個.aedt檔當中相當花時間，用下面pyaedt腳本可以輸出所有專案與專案當中的設計名稱與設計類型(HFSS,
  Q3D, Circuit...)。不須開啟GUI便可完成輸出。
---

# 如何列出特定目錄底下所有.aedt專案與專案包含的設計名稱

{% code lineNumbers="true" %}
```python
folder = r"D:\demo\Examples"

import os
import pyaedt
from pyaedt import Desktop

aedts = []
for dirPath, dirNames, fileNames in os.walk(folder):
    for f in fileNames:
        if f.endswith('.aedt'):
            aedts.append(os.path.join(dirPath, f))

info = {}
desktop = Desktop(specified_version='2022.1', non_graphical=True)
for n, i in enumerate(aedts):
    if os.path.isfile(i + '.lock'):
        os.remove(i + '.lock')
    print(f'{n}/{len(aedts)}:{i}')
    try:
        oproject = desktop.odesktop.OpenProject(i)
        designs = oproject.GetChildNames()
        types = [oproject.GetChildObject(j).GetDesignType() for j in designs]
        info[i] = list(zip(designs, types))
        oproject.Close()
    except:
        pass
desktop.close_desktop()

# %%
design_map = {'HFSS': pyaedt.Hfss,
              'HFSS 3D Layout Design': pyaedt.Hfss3dLayout,
              'Q3D Extractor': pyaedt.Q3d,
              '2D Extractor': pyaedt.Q2d,
              'Circuit Design': pyaedt.Circuit,
              'Maxwell 2D': pyaedt.Maxwell2d,
              'Maxwell 3D': pyaedt.Maxwell3d,
              'Icepak': pyaedt.Icepak,
              'Twin Builder': pyaedt.TwinBuilder,
              'Mechanical': pyaedt.Mechanical,
              'EMIT': pyaedt.Emit,
              'RMxprt': pyaedt.Rmxprt
              }

k = 0
result = {}
for i, design_info in info.items():
    result[i] = []
    for m, n in design_info:
        try:
            app = design_map[n](i, m, specified_version='2022.1')
            image_path = os.path.abspath(f'{k}.jpg')
            app.export_design_preview_to_jpg(image_path)
            k += 1
            app.close_project()

            result[i].append((m, n, image_path))
        except:
            raise

# %%

css = '''
<style>
* {
  box-sizing: border-box;
}

/* Create three equal columns that floats next to each other */
.column {
  float: left;
  width: 25%;
  padding: 10px;
  height: 300px; /* Should be removed. Only for demonstration */
}

/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}
</style>
'''

with open('summary.html', 'w') as f:
    f.write(css)
    for aedt_path, data in result.items():
        f.write(f'<H2 style="color:blue;">{aedt_path}</H2>\n')
        f.write('<div class="row">\n')
        for design_name, design_type, image in data:
            f.write('<div class="column">\n')
            f.write(f'<H3>{design_name} ({design_type})</H3>\n')
            f.write(f'<img src="{image}" width="200">\n')
            f.write('</div>\n')
        f.write('</div>\n')
os.system('summary.html')
print(result)
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
