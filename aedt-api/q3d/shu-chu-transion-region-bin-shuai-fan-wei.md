---
description: 選擇金屬厚度邊，執行腳本即可輸出到訊息視窗。
---

# 輸出Transion Region頻率範圍

{% code lineNumbers="true" %}
```python
import math
import ScriptEnv

oDesktop.ClearMessages("", "", 2)
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor("3D Modeler")

try:
    selected = oEditor.GetSelections()
    edge_id = selected[0].replace('Edge', '')

    obj = oEditor.GetObjectNameByEdgeID(edge_id)
    material = oEditor.GetPropertyValue('Geometry3DAttributeTab', obj, 'Material').replace('"', '')
    AddWarningMessage('Material: {}'.format(material))

    unit_map = {'meter': 1, 'mm': 1e-3, 'um': 1e-6, 'nm': 1e-9, 'mil': 2.54e-5, 'in': 2.54e-2}
    freq_map = [(1, 'Hz'), (1e3, 'KHz'), (1e6, 'MHz'), (1e9, 'GHz'), (1e12, 'THz')]

    unit = oEditor.GetModelUnits()
    thickness = float(oEditor.GetEdgeLength(edge_id)) * unit_map[unit]
    AddWarningMessage('Thicknetss: {}(m)'.format(thickness))

    oDefinitionManager = oProject.GetDefinitionManager()
    conductivity = float(
        oDefinitionManager.GetPropertyValue('MaterialPropTab', 'Materials:' + material, 'Bulk Conductivity'))
    permeability = float(
        oDefinitionManager.GetPropertyValue('MaterialPropTab', 'Materials:' + material, 'Relative Permeability'))
    AddWarningMessage('Conductivity: {}'.format(conductivity))
    AddWarningMessage('Relative Permeability: {}'.format(permeability))

    u0 = 4 * math.pi * 1e-7

    ac_high = 9 / (math.pi * conductivity * permeability * u0 * thickness ** 2)

    for n, (scale, unit) in enumerate(freq_map):
        if ac_high / scale < 1:
            scale, unit = freq_map[n - 1]
            _ac_low = str(round(ac_high / 9 / scale, 3)) + unit
            _ac_high = str(round(ac_high / scale, 3)) + unit
            break

    AddWarningMessage("Transition Region: {} - {}".format(_ac_low, _ac_high))

except:
    AddErrorMessage("Please Select Metal Edge!")
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>
