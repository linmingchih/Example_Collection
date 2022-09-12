# SIwave

在IDE開發SIwave可以用下面四行連結SIwave主程式

{% code lineNumbers="true" %}
```python
from win32com import client

oApp = client.Dispatch("SIwave.Application.2022.1")
oApp.RestoreWindow()
oDoc = oApp.GetActiveProject()
```
{% endcode %}
