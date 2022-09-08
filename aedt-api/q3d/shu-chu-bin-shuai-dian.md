---
description: 在Q3D當中找不到輸出頻率的API函數。一個方法是在模擬完成之後，用下面程式碼輸出頻率值：
---

# 輸出頻率點

{% code lineNumbers="true" %}
```python
import ScriptEnv
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oDesktop.ClearMessages("", "", 2)
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()

oModule = oDesign.GetModule("ReportSetup")
arr = oModule.GetSolutionDataPerVariation(  
"Matrix", 
"Setup1 : Sweep1", 
["Context:=", "Original"],
['Freq:=', ['All']], 
["mag(Freq)"])

freqs = arr[0].GetRealDataValues("mag(Freq)")
AddWarningMessage(str(freqs))
```
{% endcode %}
