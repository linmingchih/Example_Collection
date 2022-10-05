# 輸出Excitations及其對應的Pin

{% code lineNumbers="true" %}
```python
from win32com import client

oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.GetActiveEditor()


oModule = oDesign.GetModule('Excitations')
x = oModule.GetAllPortsList()
for port in oModule.GetAllPortsList():
    pin = oEditor.GetPropertyValue('BaseElementTab', port, 'Component Pin')
    print(port, pin)
```
{% endcode %}

```
U2A5.B24.CLN_SD_DAT<2> B24
U2A5.C23.CLN_SD_DAT<0> C23
U2A5.C26.CLN_SD_DAT<3> C26
U2A5.D24.CLN_SD_DAT<1> D24
```
