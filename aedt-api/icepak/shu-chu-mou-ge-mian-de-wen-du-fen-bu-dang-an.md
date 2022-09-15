# 輸出某個面的溫度分布檔案

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

{% code lineNumbers="true" %}
```python
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()

oModule = oDesign.GetModule("FieldsReporter")
oModule.CalcStack("clear")
oModule.CopyNamedExprToStack("Temperature")
oModule.EnterSurf("Rectangle1")
oModule.CalcOp("Value")

oModule.CalculatorWrite("D:/demo/temp.fld", 
	[
		"Solution:="		, "Setup1 : SteadyState"
	], [])

oModule.CalcStack("clear")

with open("D:/demo/temp.fld") as f:
    text = f.readlines()
    
temp = {}    
for line in text[2:]:
    x, y, z, t = line.split()
    temp[(float(x), float(y), float(z))] = t

AddWarningMessage(str(temp.values()[:100]))
```
{% endcode %}
