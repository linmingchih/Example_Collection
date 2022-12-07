# 如何取得net及其類別和net上面的物件名

編寫Q3D自動化程式需要取得設計當中的net name及類別(signal, ground, floating)。也必須知道物件所對應的net名稱。底下程式碼可以取得以上所提到的資訊：

{% code lineNumbers="true" %}
```python
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor('3D Modeler')
oModule = oDesign.GetModule('BoundarySetup')
x = oModule.GetExcitations()
info = zip(x[0::2], x[1::2])
AddWarningMessage(str(info))

signal_nets = [i for i, j in info if j=='SignalNet']

result = {}
for s in signal_nets:
    result[s] = []
    for i in oModule.GetExcitationAssignment(s):
        objname = oEditor.GetObjectNameByID(int(i))
        result[s].append(objname)

AddWarningMessage(str(result))
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (4) (2).png" alt=""><figcaption></figcaption></figure>
