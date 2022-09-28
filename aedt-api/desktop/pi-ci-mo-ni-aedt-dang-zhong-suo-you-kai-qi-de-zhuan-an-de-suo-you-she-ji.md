---
description: 手動逐個加入設計到Queue有點小麻煩，在AEDT當中直接執行本腳本，所有開啟的專案的所有設計都會依序模擬，模擬完成之後會存檔。
---

# 批次模擬AEDT當中所有開啟的專案的所有設計

{% code title="run_all_designs.py" lineNumbers="true" %}
```python
# from win32com import client
# oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.1")
# oDesktop = oApp.GetAppDesktop()
# oDesktop.RestoreWindow()

oDesktop.ClearMessages('', '', 2)
data = {}

total_designs = 0
for project in oDesktop.GetProjectList():
    oProject = oDesktop.SetActiveProject(project)
    data[project] = []
    for design in oProject.GetTopDesignList():
        if ';' in design:
            design = design.split(';')[1]
        
        data[project].append(design)
        total_designs += 1
        
process_design = 0
for project, designs in data.items():
    oProject = oDesktop.SetActiveProject(project)
    for design in designs:
        process_design += 1
        AddWarningMessage('{}/{}: {} - {}'.format(process_design, total_designs, project, design)) 
        
        oDesign = oProject.SetActiveDesign(design)
        oDesign.AnalyzeAll()
        oProject.Save()
          
```
{% endcode %}
