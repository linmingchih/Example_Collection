# 找出座標的角度(0-360)

{% code lineNumbers="true" %}
```python
from math import acos, sqrt, degrees, radians, cos, sin

pts = [(cos(radians(theta)), sin(radians(theta))) for theta in range(360)]

def getDegree(x, y):
    theta = acos(x/(sqrt(x**2 + y**2)))
    if y >= 0:
        return round(degrees(theta), 9)
    else:
        return round(360 - degrees(theta), 9)

for x, y in pts:
    print(getDegree(x, y))
```
{% endcode %}
