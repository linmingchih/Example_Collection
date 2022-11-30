---
description: 本範例可以將兩個可參數化圓(中心點座標及半徑)輸出成為淚滴狀結構
---

# 可參數化淚滴狀結構

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

```python
from pyaedt import Hfss
hfss = Hfss(specified_version='2022.2')

hfss['x1'] = 0
hfss['y1'] = 0
hfss['r1'] = 10
 
hfss['x2'] = 20
hfss['y2'] = 10
hfss['r2'] = 5

c1 = hfss.modeler.create_circle(2, ('x1', 'y1', 0), 'r1', matname='copper')
c2 = hfss.modeler.create_circle(2, ('x2', 'y2', 0), 'r2', matname='copper')

hfss['dist'] = 'sqrt((x1-x2)^2+(y1-y2)^2)'
hfss['dphi'] = 'acos(abs(r1-r2)/dist)'
pt1_x = '((x2-x1)*cos(dphi)+(y2-y1)*sin(dphi))*(r1/dist)+x1'
pt1_y = '((x2-x1)*-sin(dphi)+(y2-y1)*cos(dphi))*(r1/dist)+y1'
pt2_x = '((x2-x1)*cos(dphi)+(y2-y1)*sin(dphi))*(r2/dist)+x2'
pt2_y = '((x2-x1)*-sin(dphi)+(y2-y1)*cos(dphi))*(r2/dist)+y2'


pt3_x = '((x2-x1)*cos(-dphi)+(y2-y1)*sin(-dphi))*(r1/dist)+x1'
pt3_y = '((x2-x1)*-sin(-dphi)+(y2-y1)*cos(-dphi))*(r1/dist)+y1'
pt4_x = '((x2-x1)*cos(-dphi)+(y2-y1)*sin(-dphi))*(r2/dist)+x2'
pt4_y = '((x2-x1)*-sin(-dphi)+(y2-y1)*cos(-dphi))*(r2/dist)+y2'


c3 = hfss.modeler.create_polyline([(pt1_x, pt1_y, 0), 
                                   (pt2_x, pt2_y, 0),
                                   (pt4_x, pt4_y, 0),
                                   (pt3_x, pt3_y, 0),],
                                  close_surface=True,
                                  cover_surface=True,
                                  matname='copper')

hfss.modeler.unite([c1, c2, c3])
```
