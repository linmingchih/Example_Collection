---
description: 在Spyder當中修改padstack definition某一層的屬性
---

# 修改padstack definition某一層的屬性

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

aedb_path = "D:\demo2\Galileo_G87173_20431_12.aedb"
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
    padstack_instances = [i for i in layout.PadstackInstances]
    padstack_def = {}
    for i in padstack_instances:
        if i.GetPadstackDef().GetName() not in padstack_def:
            padstack_def[i.GetPadstackDef().GetName()] = i.GetPadstackDef()

    dimension = System.Collections.Generic.List[edb.Utility.Value]()
    dimension.Add(edb.Utility.Value(0))

    for i, d in padstack_def.items():
        new_data = edb.Definition.PadstackDefData(d.GetData())

        new_data.SetPadParameters('Default',
                                  edb.Definition.PadType.RegularPad,
                                  edb.Definition.PadGeometryType.NoGeometry,
                                  dimension,
                                  edb.Utility.Value(0),
                                  edb.Utility.Value(0),
                                  edb.Utility.Value(0))
        new_data.SetPadParameters('Default',
                                  edb.Definition.PadType.AntiPad,
                                  edb.Definition.PadGeometryType.NoGeometry,
                                  dimension,
                                  edb.Utility.Value(0),
                                  edb.Utility.Value(0),
                                  edb.Utility.Value(0))
        d.SetData(new_data)
        DB.Save()
except:
    raise
finally:
    DB.Close()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>
