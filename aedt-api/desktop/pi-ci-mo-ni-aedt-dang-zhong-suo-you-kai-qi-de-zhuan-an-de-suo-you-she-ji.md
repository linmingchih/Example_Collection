---
description: 手動逐個加入設計到Queue有點小麻煩，在AEDT當中直接執行本腳本，所有開啟的專案的所有設計都會依序模擬，模擬完成之後會存檔。
---

# 批次模擬AEDT當中所有開啟的專案的所有設計

{% code title="run_all_designs.py" lineNumbers="true" %}
```python
# from win32com import client
# oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
# oDesktop = oApp.GetAppDesktop()
# oDesktop.RestoreWindow()

oDesktop.ClearMessages('', '', 2)
data = []

total_designs = 0
for project in oDesktop.GetProjectList():
    oProject = oDesktop.SetActiveProject(project)

    for design in oProject.GetTopDesignList():
        if ';' in design:
            design = design.split(';')[1]
        
        data.append((design, project))
        total_designs += 1

data.sort()
#%%
process_design = 0

for design, project in data:
    oProject = oDesktop.SetActiveProject(project)
    process_design += 1
    AddWarningMessage('{}/{}: {} - {}'.format(process_design, total_designs, project, design)) 
    
    oDesign = oProject.SetActiveDesign(design)
    try:
        oDesign.AnalyzeAll()
        oProject.Save()
        AddWarningMessage('Successful!')
    except:
        AddWarningMessage('Failed!')
```
{% endcode %}
