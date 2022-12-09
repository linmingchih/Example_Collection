# 找出線邊緣並設定Edge Port

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

{% code lineNumbers="true" %}
```python
import os
import numpy as np
from win32com import client
oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()
oProject = oDesktop.GetActiveProject()

oDesign = oProject.GetActiveDesign()
oEditor = oDesign.GetActiveEditor()


def create_line(x0, y0, x1, y1, line_width, layer, name='temp'):
    return oEditor.CreateLine(
        	[
        		"NAME:Contents",
        		"lineGeometry:="	, 
                ["Name:=", name,
                 "LayerName:=" , layer,			
                 "lw:="			, line_width,			
                 "endstyle:="		, 0,			
                 "StartCap:="		, 0,			
                 "n:="			, 2,			
                 "U:="			, "mm",			
                 "x:="			, x0,			
                 "y:="			, y0,			
                 "x:="			, x1,			
                 "y:="			, y1,			
                 "MR:="			, "600mm"]
        	])
def create_edge_port(line):
    oEditor.CreateEdgePort(
	[
		"NAME:Contents",
		"edge:="		, ["et:=", "pe",			
                           "prim:="		, line,			
                           "edge:="		, 0],
		"external:="		, True,
		"btype:="		, 0
	])


for line in oEditor.FindObjects('Type', 'Line'):
    net_name = oEditor.GetPropertyValue('BaseElementTab', line, 'Net')
    if 'PCIE_RX' in net_name:
        layer = oEditor.GetPropertyValue('BaseElementTab', line, 'PlacementLayer')
        line_width = oEditor.GetPropertyValue('BaseElementTab', line, 'LineWidth')
        
        pts = [i for i in oEditor.GetProperties('BaseElementTab', line) if i.startswith('Pt')]
        x, y = oEditor.GetPropertyValue('BaseElementTab', line, pts[-1]).split(',')
        objs = oEditor.FindObjectsByPoint(oEditor.Point().Set(float(x)*1e-3, float(y)*1e-3), layer)
        z = [i for i in objs if i != line]
        p = oEditor.GetPropertyValue('BaseElementTab', z[0], 'Type')
        
        if p == 'Pin':
            x0, y0 = oEditor.GetPropertyValue('BaseElementTab', line, pts[-1]).split(',')
            x1, y1 = oEditor.GetPropertyValue('BaseElementTab', line, pts[-2]).split(',')
            
        else:
            x0, y0 = oEditor.GetPropertyValue('BaseElementTab', line, pts[0]).split(',')
            x1, y1 = oEditor.GetPropertyValue('BaseElementTab', line, pts[1]).split(',')            
        
        w = create_line(float(x0), float(y0), float(x1), float(y1), line_width, layer)
        create_edge_port(w)
```
{% endcode %}
