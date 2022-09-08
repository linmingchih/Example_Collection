---
description: 現有的AEDT API不支援載入激發源的csv，這裡我自己寫了一個。
---

# 用來載入激發源設定的csv檔

{% code lineNumbers="true" %}
```python
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oModule = oDesign.GetModule("Solutions")


def loadExcitation(csv_path):
    with open(csv_path) as f:
        text = f.readlines()

    if 'Source,Magnitude,Phase,Terminated,Resistance,Reactance' in text[0]:
        items = []
        for line in text[1:]:
            Source, Magnitude, Phase, Terminated, Resistance, Reactance = line.strip().split(',')
            try:
                if Terminated == '1':
                    items.append([
                        "Name:="   , Source,
                        "Terminated:="    , True,
                        "Resistance:="    , Resistance,
                        "Reactance:="     , Reactance
                    ])
                else:
                    items.append([
                        "Name:="      , Source,
                        "Terminated:="    , False,
                        "Magnitude:="     , Magnitude,
                        "Phase:="     , Phase
                    ])
            except:
                pass
        AddWarningMessage(str(items))
        oModule.EditSources(
            [
                [
                    "UseIncidentVoltage:=" , False,
                    "IncludePortPostProcessing:=", False,
                    "SpecifySystemPower:=" , False
                ]
            ] + items)

    else:
        items = []
        for line in text[1:]:
            try:
                Source ,Magnitude ,Phase = line.strip().split(',')
                items.append([
                    "Name:="      , Source,
                    "Magnitude:="     , Magnitude,
                    "Phase:="     , Phase])
            except:
                pass
        AddWarningMessage(str(items))
        oModule.EditSources(
            [
                [
                    "IncludePortPostProcessing:=", False,
                    "SpecifySystemPower:=" , False
                ]
            ] + items)


loadExcitation('d:/demo/sss2.csv')
```
{% endcode %}
