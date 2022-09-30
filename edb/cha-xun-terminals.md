# 查詢Terminals

{% code lineNumbers="true" %}
```python
import clr, os, sys, System

AnsysEM_Path = 'C:/Program Files/AnsysEM/v221/Win64/'
sys.path.append(AnsysEM_Path)
os.environ['PATH'] += ';' + AnsysEM_Path

clr.AddReference('Ansys.Ansoft.Edb')
clr.AddReference('Ansys.Ansoft.SimSetupData')
import Ansys.Ansoft.Edb as edb

edb.Database.SetRunAsStandAlone(True)

aedb_path = r"D:\demo\Galileo_G87173_20454.aedb"
DB = edb.Database.Open(aedb_path, False)

cells = list(DB.TopCircuitCells)
layout = cells[0].GetLayout()

terminals = [i.GetName() for i in list(layout.Terminals)]
print(terminals)
```
{% endcode %}

{% code overflow="wrap" %}
```
['U2A5.AT3.M_DQ<8>', 'U2A5.AJ6.M_DQ<5>', 'U2A5.AG6.M_DQ<4>', 'U2A5.AL15.M_DQ<2>', 'U2A5.AH15.M_DQ<3>', 'U2A5.AH12.M_DQ<6>', 'U2A5.AK12.M_DQ<7>', 'U2A5.AK11.M_DQ<1>']
```
{% endcode %}
