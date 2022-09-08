---
description: 輸出變數及使用該變數的物件名稱
---

# 輸出變數及使用該變數的物件

{% code lineNumbers="true" %}
```python
import os, re

oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor("3D Modeler")

oDesktop.ClearMessages("", "", 2)

properties = oDesign.GetProperties('LocalVariableTab', "LocalVariables")
prj_path = os.path.join(oProject.GetPath(), oProject.GetName() + '.aedt')
design_name = oDesign.GetName()

with open(prj_path) as f:
    text = f.readlines()

models = []
for n, line in enumerate(text):
    if "$begin 'HFSSModel'" in line:
        model = []
    elif "$end 'HFSSModel'" in line:
        models.append(model)
        del (model)

    try:
        model.append(line)
    except:
        pass

data = {}
for model in models:
    if design_name not in model[2]:
        continue
    for prop_name in properties:
        for line in model:
            m = re.search("Name=(.*)", line)
            if m:
                obj = m.group(1).replace("'", '')
                continue

            if prop_name in line and '=' in line and 'obj' in locals():
                try:
                    if obj not in data[prop_name]:
                        data[prop_name] += [obj]
                except:
                    data[prop_name] = [obj]

for i, v in data.items():
    AddWarningMessage(str(i) + ': ' + str(v))
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>
