---
description: 選擇多個物件，執行下面script，即可輸出選取物件的XYZ邊界座標。
---

# 計算選取物件的XYZ邊界

{% code lineNumbers="true" %}
```python
import ScriptEnv

ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor("3D Modeler")


def setModel(x):
    objs = {True: [i for i in x if x[i]], False: [i for i in x if not x[i]]}
    for b, o in objs.items():
        if o:
            oEditor.ChangeProperty(
                [
                    "NAME:AllTabs",
                    [
                        "NAME:Geometry3DAttributeTab",
                        [
                            "NAME:PropServers",
                        ] + o,
                        [
                            "NAME:ChangedProps",
                            [
                                "NAME:Model",
                                "Value:="  , b
                            ]
                        ]
                    ]
                ])

def getBoundingBox(objs):
    db = {}
    for i in range(oEditor.GetNumObjects()):
        name = oEditor.GetObjectName(i)
        p = oEditor.GetPropertyValue('Geometry3DAttributeTab', name, 'Model')
        db[name] = p

    setModel({i :(i in oEditor.GetSelections()) for i in db.keys()})
    result = oEditor.GetModelBoundingBox()
    setModel(db)
    return result

size = getBoundingBox(oEditor.GetSelections())
AddWarningMessage(str(size))
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>
