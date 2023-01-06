# 將3D Layout Polygon轉HFSS

{% code lineNumbers="true" %}
```python
from math import sqrt
from win32com import client
from math import sin, cos, atan, radians, degrees, sqrt, pi

oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()

oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.GetActiveEditor()

obj = 'poly_471'
data = []
for i in oEditor.GetProperties('BaseElementTab', obj):
    if 'Pt' in i:
        x, y = oEditor.GetPropertyValue('BaseElementTab', obj, i).split(',')
        data.append((float(x), float(y)))
    elif 'ArcHeight' in i:
        v = oEditor.GetPropertyValue('BaseElementTab', obj, i)
        data.append(float(v[:-2]))

#%%
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor("3D Modeler")
unit = oEditor.GetModelUnits()

n_seg = 0
def PolylinePoints(x, y, z=0):
    return ["NAME:PLPoint",
            "X:="			, "{}{}".format(x, unit),
            "Y:="			, "{}{}".format(y, unit),
            "Z:="			, "{}{}".format(z, unit)]

def PolylineSegment(num):
    global n_seg
    if num==2:
        stype = "Line" 
        dn_seg = 1
    else:
        stype = "Arc"
        dn_seg = 2
    
    result = ["NAME:PLSegment",
			  "SegmentType:="		, stype,
			  "StartIndex:="		, n_seg,
			  "NoOfPoints:="		, num]
    n_seg += dn_seg
    return result
    
def create_arc(x1, y1, x2, y2, ds):
    xc = (x1 + x2) / 2
    yc = (y1 + y2) / 2
    mag = sqrt((x2 - x1)**2 + (y2 - y1)**2)
    print(mag)
    
    x, y = xc-ds*(y2-y1)/mag, yc+ds*(x2-x1)/mag
    return x, y
        
def convert():
    z = 0
    polys = []
    segs = []
    pts = data + [data[0]]
    
    for n, i in enumerate(pts):
        if n  == len(pts) - 1:
            break
        
        if type(i) == tuple:
            if type(pts[n+1]) == tuple:
                x, y = i
                polys.append(PolylinePoints(x, y, z))
                segs.append(PolylineSegment(2))            
            
            elif type(pts[n+1]) == float:
                x1, y1 = i
                ds = pts[n+1]
                x2, y2 = pts[n+2]
                x3, y3 = create_arc(x1, y1, x2, y2, ds)
                polys.append(PolylinePoints(x1, y1, z))
                polys.append(PolylinePoints(x3, y3, z))
                segs.append(PolylineSegment(3)) 
        
        elif type(i) == float:
            continue
        
    polys.append(PolylinePoints(data[0][0], data[0][1], z))    
        
    
    oEditor.CreatePolyline(
    	[
    		"NAME:PolylineParameters",
    		"IsPolylineCovered:="	, True,
    		"IsPolylineClosed:="	, True,
    		[
    			"NAME:PolylinePoints",
    
    		] + polys,
    		[
    			"NAME:PolylineSegments",
    
    		] + segs,
    		[
    			"NAME:PolylineXSection",
    			"XSectionType:="	, "None",
    			"XSectionOrient:="	, "Auto",
    			"XSectionWidth:="	, "0mm",
    			"XSectionTopWidth:="	, "0mm",
    			"XSectionHeight:="	, "0mm",
    			"XSectionNumSegments:="	, "0",
    			"XSectionBendType:="	, "Corner"
    		]
    	], 
    	[
    		"NAME:Attributes",
    		"Name:="		, "Polyline2",
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

convert()
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>
