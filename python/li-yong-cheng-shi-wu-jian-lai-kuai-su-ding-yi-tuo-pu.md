---
description: 使用者可以指定每個格點的材料來定義馬達截面的拓樸結構。可搭配PyAEDT快速生成Maxwell2D的設計並完成模擬。
---

# 利用程式物件來快速定義拓樸

{% code lineNumbers="true" %}
```python
from matplotlib.patches import Wedge
from matplotlib.collections import PatchCollection
import matplotlib.pyplot as plt

m1 = 90
m2 = 45

class topology():
    def __init__(self):
        self.materials = {}
        self.wedges = []
        dx = 90 / 20
        dy = 0.5 / 20

        for i in range(1, 21):
            for j in range(1, 20 + 1):
                self.materials[i, j] = 0
                w = Wedge((0, 0), 0.5 + j * dy, (i-1) * dx, i * dx, dy)
                self.wedges.append(w)

    def getmaterials(self):
        result = []
        for i in range(1, 21):
            for j in range(1, 20 + 1):
                result.append(self.materials[i, j])
        return result

    def show(self):
        cmap = plt.cm.get_cmap('jet')
        p = PatchCollection(s.wedges, alpha=1, cmap=cmap, )
        p.set_clim(0, 100)
        p.set_array(self.getmaterials())
        p.set_color('w')
        fig, ax = plt.subplots(figsize=(15, 15))
        ax.add_collection(p)

s = topology()
s.materials[1, 20] = m1
s.materials[10, 10] = m2
s.materials[20, 1] = m2
for i in [5, 6, 7, 8]:
    s.materials[i, 14] = m1
s.show()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>
