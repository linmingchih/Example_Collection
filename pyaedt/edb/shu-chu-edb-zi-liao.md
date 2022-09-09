# 輸出EDB資料

{% code lineNumbers="true" %}
```python
from pyaedt import Edb

myedb = Edb("D:/demo_pyaedt/Galileo_G87173_2042.aedb", 'Galileo_G87173_204')

x = myedb.core_nets.nets
print(x.keys())

x = [i.PartName for i in myedb.core_components.get_component_list()]
print(x)

x = myedb.core_stackup.stackup_layers.layers
print(x.keys())

x = myedb.core_padstack.padstacks
print(x.keys())

x = myedb.core_primitives.polygons
y = ([(i.GetNet().GetName(), i.GetLayer().GetName()) for i in x])
print(y)

myedb.close_edb()
```
{% endcode %}
