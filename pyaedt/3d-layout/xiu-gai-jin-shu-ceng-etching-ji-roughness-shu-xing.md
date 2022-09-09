# 修改金屬層Etching及Roughness屬性

{% code lineNumbers="true" %}
```python
from pyaedt import Hfss3dLayout

hfss = Hfss3dLayout(specified_version='2022.2')

layers = {}
for i, obj in hfss.modeler.layers.layers.items():
    x =  hfss.modeler.layers.layers[i]
    layers[x.name] = obj

layers['TOP'].useetch = True
layers['TOP'].etch = 1.1
layers['TOP'].thickness = 5e-5

layers['TOP'].user = True
layers['TOP'].NR = 0.7
layers['TOP'].SHRatio = 3.0

layers['TOP'].update_stackup_layer()
```
{% endcode %}
