# 如何在匯出Pin Group的設定，並在另一個設計匯入

{% code title="exportPG_v2.py" lineNumbers="true" %}
```python
import os
import json
import logging

os.chdir(os.path.dirname(__file__))
logging.basicConfig(filename='simulation.log', encoding='utf-8', filemode='w', level=logging.DEBUG)

oDoc = oApp.GetActiveProject()
oDoc.ScrExportComponentFile('temp.cmp')
with open('temp.cmp') as f:
    text = f.readlines()

pg = {}
for line in text:
    if line.startswith('B_IC') or line.startswith('B_IO'):
        _, refdes, part = line.split()

    if line.startswith('B_PIN_GROUP '):
        group_name = line.split()[1]
        key = ','.join([part[1:-1], refdes[1:-1], group_name])
        continue

    if 'E_PIN_GROUP' in line:
        if 'key' in locals():
            del (key)
        continue

    if 'key' in locals():
        pg[key] = line.split()

logging.info(pg)

with open('pg_info.json', 'w') as f:
    json.dump(pg, f, indent=4)
```
{% endcode %}

{% code title="importPG_v2.py" lineNumbers="true" %}
```python
import os
import json
import logging

os.chdir(os.path.dirname(__file__))
logging.basicConfig(filename='simulation.log', encoding='utf-8', filemode='w', level=logging.DEBUG)

oDoc = oApp.GetActiveProject()
with open('pg_info.json') as f:
    data = json.load(f)

logging.info(data)
for key, pins in data.items():
    part_name, refdes_name, group_name = key.split(',')    
    oDoc.ScrCreatePinGroups(part_name, refdes_name, pins, group_name, False)
```
{% endcode %}

