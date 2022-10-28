# 在錐形體上生成花紋

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

{% code lineNumbers="true" %}
```python
xc = [10, 20, 30, 40, 50]
yc = [100, 95, 70, 50, 20]

cell = [(3, 0), (1, 1), (0, 3), (-1, 1) , (-3, 0), (-1, -1), (0, -3), (1, -1)]

pitch_x = 10
pitch_y = 10

#%%
from pyaedt import Hfss
hfss = Hfss(specified_version='2022.2')
hfss.modeler.model_units = 'mm'
import matplotlib.pyplot as plt
import numpy as np
from math import sin, cos, atan, radians, degrees, sqrt

xc = np.array(xc)
yc = np.array(yc)
p = np.poly1d(np.polyfit(xc, yc, len(xc)-1))

result = []
class radome:
    def __init__(self, poly, cell):
        self.poly = poly
        self.cell = cell
        
        
    def create(self, x0, phi_degree):
        r = 1
        phi = radians(phi_degree)
        dx = 1e-6
        dy = self.poly(x0) - self.poly(x0+dx)
        theta = atan(dy/dx)
        #print(degrees(theta))
        
        new_cell = []
        
        for u, v in self.cell:
            pt = (x0*cos(phi), x0*sin(phi), self.poly(x0))
            a_r = (sin(theta)*cos(phi)*r, sin(theta)*sin(phi)*r, cos(theta)*r)
            a_theta = (cos(theta)*cos(phi)*v, cos(theta)*sin(phi)*v, -sin(theta)*v)
            a_phi = (-sin(phi)*u, cos(phi)*u, 0)
            
            _x = pt[0] + a_r[0] + a_theta[0] + a_phi[0]
            _y = pt[1] + a_r[1] + a_theta[1] + a_phi[1]
            _z = pt[2] + a_r[2] + a_theta[2] + a_phi[2]
            
            new_cell.append((_x, _y, _z))
            plt.scatter(_x, _z)
        
        return new_cell            
  
    
rado = radome(p, cell)
r = hfss.modeler.create_polyline(list(zip(xc, [0]*len(xc), yc)))

xf = [i/1000 for i in xc]
yf = [i/1000 for i in yc]
pf = np.poly1d(np.polyfit(xf, yf, 3))

formula = []
for n, i in enumerate(list(pf)[::-1]):
    if n >= 1:    
        formula.append(f'({i}*_t^{n})')
    else:
        formula.append(str(i))
formula='+'.join(formula)
    
r = hfss.modeler.create_equationbased_curve('_t', 0, formula, min(xf), max(xf))
hfss.modeler.sweep_around_axis(r, 2)




xsample = np.arange(xc[0], xc[-1], 0.001)
ysample = [p(i) for i in xsample]

lsample = [sqrt((i-j)**2 + (0.001)**2)  for i, j in zip(ysample[1:], ysample[:-1])]

y_sum = 0
x_grid = []
for i, j in zip(lsample, xsample):
    y_sum += i
    if y_sum > pitch_y:
        x_grid.append(j)
        y_sum = 0
    else:
        pass


ds = []

for x in x_grid:
    phi = 0
    while phi < 360:    
        result = rado.create(x, phi)
        cell = hfss.modeler.create_polyline(result, close_surface=True, cover_surface=True)
        hfss.oeditor.WrapSheet(
     	[
    		"NAME:Selections",
    		"Selections:="		, "{},{}".format(cell.name, r.name)
     	], 
     	[
    		"NAME:WrapSheetParameters",
    		"Imprinted:="		, False
     	])
        phi += degrees(pitch_y/x)

```
{% endcode %}
