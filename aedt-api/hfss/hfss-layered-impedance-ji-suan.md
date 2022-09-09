---
description: 這裡透過遞迴完成計算，公式可以參考HFSS Help Layered Impedance章節
---

# HFSS Layered Impedance計算

{% code lineNumbers="true" %}
```python
from cmath import pi, sinh, cosh, tan, sqrt

u0 = 4 * pi * 1e-7
e0 = 8.854187817e-12

x = [(1, 1, 0, 0, 8670000, 1e-6),
     (1, 1.00018, 0, 0, 1820000, 2e-6),
     (4.4, 1, 0.02, 0, 0, 3e-6),
     (1, 1, 0, 0, 0, 1e9)]

frequency = 1e9
w = 2 * pi * frequency
k0 = 2 * pi * frequency * sqrt(u0 * e0)

def calculate(x):
    erk1, urk1, lte, ltm, sigmak, dk = x[0]

    erk2 = -(sigmak) / (w * e0) - erk1 * lte
    urk2 = -urk1 * ltm

    erk = erk1 + 1j * erk2
    urk = urk1 + 1j * urk2

    Zwk = sqrt((u0 * urk) / (e0 * erk))

    if len(x) > 1:
        Zinput = calculate(x[1:])
        rk = 1j * k0 * sqrt(erk * urk)
        return Zwk * ((Zinput * cosh(rk * dk) + Zwk * sinh(rk * dk)) / (Zinput * sinh(rk * dk) + Zwk * cosh(rk * dk)))

    else:
        return Zwk
Z = calculate(x)
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (36) (1).png" alt=""><figcaption></figcaption></figure>
