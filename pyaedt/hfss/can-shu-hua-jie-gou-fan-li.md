# 參數化結構範例

{% code lineNumbers="true" %}
```python
from math import sin, cos
from pyaedt import Q3d

q3d = Q3d(specified_version='2022.1', non_graphical=True)

q3d['r1'] = '150mm'
q3d['r2'] = '50mm'
q3d['dt'] = '1mm'
q3d['u_min'] = '-0.4*pi'
q3d['u_max'] = '0.4*pi'
q3d['du'] = 0.065
q3d['dr'] = '5mm'

q3d['v_min'] = '-0.3*pi'
q3d['v_max'] = '0.3*pi'
q3d['dv'] = 0.25

c1 = q3d.modeler.create_equationbased_curve('(r1+r2*cos(_t))*cos(u_min)', '(r1+r2*cos(_t))*sin(u_min)', 'r2*sin(_t)', 'v_min', 'v_max')
q3d.modeler.sweep_around_axis(c1, 2, 'u_max-u_min')
q3d.modeler.thicken_sheet(c1, 'dt')

spheres = []
for i in range(0, 100, 1):
    for j in range(0, 10, 1):
        x = f'if({i}*du+u_min<=u_max && {j}*dv+v_min<=v_max, (r1+r2*cos({j}*dv+v_min))*cos({i}*du+u_min), r1+r2*cos(v_min)*cos(u_min))'
        y = f'if({i}*du+u_min<=u_max && {j}*dv+v_min<=v_max, (r1+r2*cos({j}*dv+v_min))*sin({i}*du+u_min), r1+r2*cos(v_min)*sin(u_min))'
        z = f'if({i}*du+u_min<=u_max && {j}*dv+v_min<=v_max, r2*sin({j}*dv+v_min),r2*sin(v_min))'
        s = q3d.modeler.create_sphere((x,y,z), 'dr')
        s.color=(255,0,0)
        spheres.append(s)

q3d.modeler.subtract(c1, spheres, False)
q3d.save_project('d:/demo/torus.aedt')
q3d.release_desktop()
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>
