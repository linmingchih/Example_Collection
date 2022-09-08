---
description: >-
  SVD是一個很有趣的技術。以下A為100x4矩陣，共400筆數值。做完SVD分解之後，可以拆成U:100x100, sigma:100x4,
  VT矩陣:4x4，乍看數量反而暴增(400 -> 10416)。然而只需保留矩陣的一小部分，(本範例四個奇異值只保留兩個來簡化矩陣，U1:100x2,
  sigma1:2x2, VT1:2x4，總數212)，之後就可以透過矩陣乘法還原出原始的400筆數值，等於
---

# 奇異值分解(SVD, Singular Value Decomposition)

{% code lineNumbers="true" %}
```python
# -*- coding: utf-8 -*-
import numpy as np
from numpy import linalg as la
A=np.array([[i+0,i+1,i+2, i+3] for i in range(100)])

#將A矩陣(100x4)做奇異值分解分成三矩陣相乘
U,sigma,VT=la.svd(A)

#U(100x100)，sigma(100x4),V(4x4)

print('_'*40)
U1 = U[:,:2]
#U1(100x2)

sigma1 = np.array([[sigma[0],0],[0, sigma[1]]])
#sigma1(2x2)

VT1 = VT[:2,:]
#VT1(2x4)

A1 = np.dot(np.dot(U1,sigma1), VT1)
```
{% endcode %}
