# 螺旋電感範例

{% code lineNumbers="true" %}
```python
rin = 10
width = 2
spacing = 1
thickness = 1
Np = 8
Nr = 10
gap = 3
Tsub=6

import matplotlib.pyplot as plt
from math import pi, cos, sin, tan, sqrt

def spiral(rin = 10, pitch = 2, Np = 8, Nr = 10):
    dtheta = 2 * pi / Np
    theta = pi / 2
    pts = [(rin, 0), (rin, rin*tan(dtheta/2))]
    rin = rin*tan(dtheta/2)*2
    
    x = rin
    r = rin
    for i in range(Np):
        r += 1
        theta += dtheta
        x = x + r*cos(theta)
        dr = pitch / (x - rin)
    
    for i in range(Nr*Np-int(Np/2)-1):
        rin += dr
        theta += dtheta
        x0, y0 = pts[-1]
        x1, y1 = x0 + rin*cos(theta), y0 + rin*sin(theta)
        pts.append((x1, y1))
    
    pts.append((x1, 0))
    return pts

from pyaedt import Desktop, Hfss, constants

with Desktop("2021.2", non_graphical=True):
    hfss = Hfss(designname='A1')
    hfss.modeler.model_units = "um"
    p = hfss.modeler.primitives
    def create_line(pts):
        p.create_polyline(pts,
                          xsection_type='Rectangle',
                          xsection_width=width,
                          xsection_height=thickness, matname='copper')       
    
    pts = [(x0, y0, 0) for (x0, y0) in spiral(rin, (width+spacing), Np, Nr)]
    create_line(pts)
    
    x0, y0, z0 = pts[0]
    x1, y1, z1 = pts[-1]
    
    create_line([(x0-width/2, y0, -gap), (abs(x1)+5 , y0 ,-gap)])
    p.create_rectangle(constants.PLANE.YZ, 
                       (abs(x1)+5 , y0-width/2 ,-gap-thickness/2), 
                       (width, -Tsub+gap),
                       name='port1')
    hfss.create_lumped_port_to_sheet(sheet_name='port1', axisdir=constants.AXIS.Z)
    
    create_line([(x1+width/2, y1, 0), (x1-5 , y1 ,0)])
    p.create_rectangle(constants.PLANE.YZ, 
                       (x1-5 , y1-width/2 , -thickness/2), 
                       (width, -Tsub),
                       name='port2')
    hfss.create_lumped_port_to_sheet(sheet_name='port2', axisdir=constants.AXIS.Z)
    
    p.create_box((x0-width/2, y0-width/2, -gap-thickness/2), 
                 (width, width, gap+thickness),
                 matname='copper')
    p.create_box([x1-20, x1-20, -Tsub-thickness/2], 
                 [-2*x1+40, -2*x1+40, Tsub],
                 matname='silicon')
    p.create_box([x1-20, x1-20, -Tsub-thickness/2], 
                 [-2*x1+40, -2*x1+40, -0.1],
                 matname='PEC')
    
    box = p.create_box([x1-20, x1-20, -Tsub-thickness/2-0.1], 
                 [-2*x1+40, -2*x1+40, 100],
                 name='airbox',
                 matname='air')

    hfss.assign_radiation_boundary_to_objects('airbox')
    
    hfss.change_material_override()
    setup1 = hfss.create_setup(setupname='setup1')
    setup1.props['Frequency'] = '10GHz'
    hfss.create_frequency_sweep('setup1', 'GHz', 1e-3, 50)
    setup1.update()
    hfss.save_project()
    hfss.analyze_all()    
    
    L_formula = '1e9*im(1/Y(1,1))/(2*pi*freq)'
    f, m = [], []
    x = hfss.post.get_report_data(L_formula)
    
    for freq in x.solutions_data_real[L_formula]:
        mag = x.solutions_data_real[L_formula ][freq]
        f.append(freq[0])
        m.append(mag)
        
    plt.plot(f, m)
    
    
        
    plt.grid()
    plt.xlabel('Freq')
    plt.ylabel('L(nH)')
    plt.show()

    plt.clf()
    L_formula = 'im(Y(1,1))/re(Y(1,1))'
    f, m = [], []
    x = hfss.post.get_report_data(L_formula)
    hfss.save_project()
    for freq in x.solutions_data_real[L_formula]:
        mag = x.solutions_data_real[L_formula ][freq]
        f.append(freq[0])
        m.append(-mag)
        
    plt.plot(f, m)
        
    plt.grid()
    plt.xlabel('Freq')
    plt.ylabel('Q')
    plt.show()
    
    hfss.save_project()
```
{% endcode %}
