# 等距複製到曲面上面

1.在中心建立pattern

2\. 在XY平面第一象限建立多段線

3.執行腳本，腳本會建立弧面並投影pattern到等間距位置。

<figure><img src="../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

{% code lineNumbers="true" %}
```python
import numpy as np
from win32com import client
from math import sin, cos, atan, radians, degrees, sqrt, pi

oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()

oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor("3D Modeler")
unit = oEditor.GetModelUnits()

oDesktop.ClearMessages('', '', 2)

pitch_x = 2
pitch_y = 2
cone_profile = 'Polyline1'
pattern = 'Polygon1'
mode = 'project'
cone_thickness = '-0.1mm'
pattern_thickness = '0.1mm'

points = oEditor.GetVertexIDsFromObject(cone_profile)
points_position = [list(map(float, oEditor.GetVertexPosition(i))) for i in points]
xc, yc, zx = zip(*points_position)
poly_func = np.poly1d(np.polyfit(xc, yc, len(xc)-1))


xsample = np.arange(xc[0]+pitch_x/2, xc[-1]-pitch_x/2, 0.001)
ysample = [poly_func(i) for i in xsample]

lsample = [sqrt((i-j)**2 + (0.001)**2)  for i, j in zip(ysample[1:], ysample[:-1])]
y_sum = 0
x_grid = [xc[0]+pitch_x/2]
for i, j in zip(lsample, xsample):
    y_sum += i
    if y_sum > pitch_y:
        x_grid.append(j)
        y_sum = 0
    else:
        pass

grid = []
for x in x_grid:
    y=poly_func(x)
    for phi in np.arange(0, 2*pi-pitch_y/y/2, pitch_y/y):
        grid.append((x, phi))


xf = [i/1000 for i in xc]
yf = [i/1000 for i in yc]
pf = np.poly1d(np.polyfit(xf, yf, len(xf)-1))

formula = []
for n, i in enumerate(list(pf)[::-1]):
    if n >= 1:    
        formula.append(f'({i}*_t^{n})')
    else:
        formula.append(str(i))
formula = '+'.join(formula)

#%%
cone = oEditor.CreateEquationCurve(
	[
		"NAME:EquationBasedCurveParameters",
		"XtFunction:="		, "_t",
		"YtFunction:="		, formula,
		"ZtFunction:="		, 0,
		"tStart:="		, str(min(xf)),
		"tEnd:="		, str(max(xf)),
		"NumOfPointsOnCurve:="	, "0",
		"Version:="		, 1,
		[
			"NAME:PolylineXSection",
			"XSectionType:="	, "None",
			"XSectionOrient:="	, "Auto",
			"XSectionWidth:="	, "0",
			"XSectionTopWidth:="	, "0",
			"XSectionHeight:="	, "0",
			"XSectionNumSegments:="	, "0",
			"XSectionBendType:="	, "Corner"
		]
	], 
	[
		"NAME:Attributes",
		"Name:="		, "EquationCurve1",
		"Flags:="		, "",
		"Color:="		, "(143 175 143)",
		"Transparency:="	, 0,
		"PartCoordinateSystem:=", "Global",
		"UDMId:="		, "",
		"MaterialValue:="	, "\"vacuum\"",
		"SurfaceMaterialValue:=", "\"\"",
		"SolveInside:="		, True,
		"ShellElement:="	, False,
		"ShellElementThickness:=", "0mm",
		"IsMaterialEditable:="	, True,
		"UseMaterialAppearance:=", False,
		"IsLightweight:="	, False
	])


oEditor.SweepAroundAxis(
	[
		"NAME:Selections",
		"Selections:="		, cone,
		"NewPartsModelFlag:="	, "Model"
	], 
	[
		"NAME:AxisSweepParameters",
		"DraftAngle:="		, "0deg",
		"DraftType:="		, "Round",
		"CheckFaceFaceIntersection:=", False,
		"SweepAxis:="		, "X",
		"SweepAngle:="		, "360deg",
		"NumOfSegments:="	, "0"
	])

oEditor.ThickenSheet(
	[
		"NAME:Selections",
		"Selections:="		, cone,
		"NewPartsModelFlag:="	, "Model"
	], 
	[
		"NAME:SheetThickenParameters",
		"Thickness:="		, cone_thickness,
		"BothSides:="		, False,
	])
#%%

dx = 0.001

for x, phi in grid:
    y = poly_func(x)
    dy = poly_func(x+dx) - poly_func(x)
    theta = atan(dy/dx)
    
    oEditor.Copy(
    	[
    		"NAME:Selections",
    		"Selections:="		, pattern
    	])
    copied_pattern = oEditor.Paste()[0]
    
    oEditor.Rotate(
    	[
    		"NAME:Selections",
    		"Selections:="		, copied_pattern,
    		"NewPartsModelFlag:="	, "Model"
    	], 
    	[
    		"NAME:RotateParameters",
    		"RotateAxis:="		, "X",
    		"RotateAngle:="		, "90deg"
    	])
    oEditor.Rotate(
    	[
    		"NAME:Selections",
    		"Selections:="		, copied_pattern,
    		"NewPartsModelFlag:="	, "Model"
    	], 
    	[
    		"NAME:RotateParameters",
    		"RotateAxis:="		, "Z",
    		"RotateAngle:="		, theta
    	])

    r=1
    a_r = (-sin(theta)*r, cos(theta)*r)
    print(degrees(theta))
    print(a_r)

    oEditor.Move(
    	[
    		"NAME:Selections",
    		"Selections:="		, copied_pattern,
    		"NewPartsModelFlag:="	, "Model"
    	], 
    	[
    		"NAME:TranslateParameters",
    		"TranslateVectorX:="	, str(x+a_r[0])+unit,
    		"TranslateVectorY:="	, str(y+a_r[1])+unit,
    		"TranslateVectorZ:="	, str(0)+unit
    	])
    
    oEditor.Rotate(
    	[
    		"NAME:Selections",
    		"Selections:="		, copied_pattern,
    		"NewPartsModelFlag:="	, "Model"
    	], 
    	[
    		"NAME:RotateParameters",
    		"RotateAxis:="		, "X",
    		"RotateAngle:="		, phi
    	])
    
    if mode == 'project':
        oEditor.ProjectSheet(
        	[
        		"NAME:Selections",
        		"Selections:="		, "{},{}".format(copied_pattern, cone)
        	], 
        	[
        		"NAME:ProjectSheetParameters",
        		"Thickness:="		, pattern_thickness,
        		"DraftAngle:="		, "0deg",
        		"KeepOriginals:="	, False
        	])  
    elif mode == 'wrap':
        oEditor.WrapSheet(
         	[
        		"NAME:Selections",
        		"Selections:="		, "{},{}".format(copied_pattern, cone)
         	], 
         	[
        		"NAME:WrapSheetParameters",
        		"Imprinted:="		, False
         	])
        oEditor.ThickenSheet(
        	[
        		"NAME:Selections",
        		"Selections:="		, copied_pattern,
        		"NewPartsModelFlag:="	, "Model"
        	], 
        	[
        		"NAME:SheetThickenParameters",
        		"Thickness:="		, pattern_thickness,
        		"BothSides:="		, False,
        	])
    else:
        pass
```
{% endcode %}
