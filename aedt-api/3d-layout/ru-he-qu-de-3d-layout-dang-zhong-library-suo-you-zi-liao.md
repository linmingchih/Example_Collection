# 如何取得3D Layout當中Library所有資料

{% code lineNumbers="true" %}
```python
import ScriptEnv

ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oDesktop.ClearMessages("", "", 2)
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.GetActiveEditor()

oDefinitionManager = oProject.GetDefinitionManager()
lib = ['Component', 'Material', 'Padstack', 'Bondwire', 'Model', 'Symbol', 'Footprint', 'Script',]
result = {}
for i in lib:
    oManager = oDefinitionManager.GetManager(i)
    result[i] = {}
    for j in oManager.GetNames():    
        result[i][j] = oManager.GetData(j)

AddWarningMessage(str(result))
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>
