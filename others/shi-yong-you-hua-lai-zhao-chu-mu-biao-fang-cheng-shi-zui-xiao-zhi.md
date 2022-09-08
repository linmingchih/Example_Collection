---
description: 參考 https://www.youtube.com/watch?v=cXHvC_FGx24
---

# 使用優化來找出目標方程式最小值

目標方程式為四元(x1, x2, x3, x4)非線性方程式x1_x4_(x1+x2+x3)+x3。有兩條約束式，變數範圍1到5。求解滿足約束方程式的目標方程式最大值。

{% code lineNumbers="true" %}
```python
# -*- coding: utf-8 -*-
import numpy as np
from scipy.optimize import minimize

def objective(x):
    x1, x2, x3, x4 = x
    return x1*x4*(x1+x2+x3)+x3

def constraint1(x):
    x1, x2, x3, x4 = x
    return x1*x2*x3*x4-25

def constraint2(x):
    x1, x2, x3, x4 = x
    return 40 - x1**2 - x2**2 - x3**2 - x4**2

bnds = ((1, 5), (1, 5), (1, 5), (1, 5))

con1 = {'type':'ineq', 'fun':constraint1}
con2 = {'type':'eq', 'fun':constraint2}

x0 = [1, 5, 5, 1]
sol = minimize(objective, x0, bounds = bnds, constraints=[con1, con2], tol=1e-6, options = {'maxiter':100, 'disp':True})
print(sol)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
