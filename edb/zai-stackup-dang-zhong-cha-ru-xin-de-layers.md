---
description: 注意需要將layer複製，才能修改屬性。
---

# 在stackup當中插入新的layers

{% code lineNumbers="true" %}
```python
import clr, os, sys, System
from distutils.dir_util import copy_tree

AnsysEM_Path = 'C:/Program Files/AnsysEM/v221/Win64/'
sys.path.append(AnsysEM_Path)
os.environ['PATH'] += ';' + AnsysEM_Path

clr.AddReference('Ansys.Ansoft.Edb')
clr.AddReference('Ansys.Ansoft.SimSetupData')
import Ansys.Ansoft.Edb as edb

edb.Database.SetRunAsStandAlone(True)

aedb_path = "D:\demo2\Galileo_G87173_20431.aedb"
cell_name = 'Galileo_G87173_204'
layer_name = 'UNNAMED_004'
up_height = '10mil'
low_height = '12mil'

# %%----------------------------------------------------------------------------
n = 1
while True:
    new_aedb_path = aedb_path.replace('.aedb', '_{}.aedb'.format(n))
    if not os.path.isdir(new_aedb_path):
        break
    n += 1
copy_tree(aedb_path, new_aedb_path)

middle_layer_name = layer_name + '_1'
low_layer_name = layer_name + '_2'

DB = edb.Database.Open(new_aedb_path, False)

try:
    cells = [i for i in DB.CircuitCells]
    for cell in cells:
        if cell.GetName() == cell_name:
            break

    layout = cell.GetLayout()
    raw_layercollection = layout.GetLayerCollection()
    new_layercollection = edb.Cell.LayerCollection(raw_layercollection)

    all_layers = raw_layercollection.Layers(edb.Cell.LayerTypeSet.AllLayerSet)

    for layer in all_layers:
        if layer.GetName() == layer_name:
            layer_material = layer.GetMaterial()
            layer2 = edb.Cell.StackupLayer(middle_layer_name, edb.Cell.LayerType.SignalLayer, edb.Utility.Value('0'),
                                           edb.Utility.Value(0), "COPPER")
            layer3 = edb.Cell.StackupLayer(low_layer_name, edb.Cell.LayerType.DielectricLayer,
                                           edb.Utility.Value(low_height), edb.Utility.Value(0), layer_material)
            new_layercollection.AddLayerBelow(layer2, layer_name)
            new_layercollection.AddLayerBelow(layer3, middle_layer_name)

    empty_layercollection = edb.Cell.LayerCollection()
    for layer in new_layercollection.Layers(edb.Cell.LayerTypeSet.AllLayerSet):
        if layer.GetName() == layer_name:
            layer = layer.Clone()
            layer.SetThickness(edb.Utility.Value(up_height))
            empty_layercollection.AddLayerBottom(layer)
        else:
            empty_layercollection.AddLayerBottom(layer)

    layout.SetLayerCollection(empty_layercollection)

    DB.Save()
except:
    raise
finally:
    DB.Close()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
