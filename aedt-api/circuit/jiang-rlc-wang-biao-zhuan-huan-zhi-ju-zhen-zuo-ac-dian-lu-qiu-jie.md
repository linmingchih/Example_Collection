---
description: >-
  上一篇我介紹了如何從電路矩陣計算出頻率響應，這裡的例子介紹如何將網表(netlist)轉成矩陣。這個範例程式碼只能處理由Vsin, R, L,
  C所構成的任意電路。從這個例子我們可以知道如何找到足夠多的方程式來構成足以求解的矩陣。這個範例的未知變數共14個。因此矩陣大小為14x14。
---

# 將RLC網表轉換至矩陣做AC電路求解

{% code lineNumbers="true" %}
```python
# -*- coding: utf-8 -*-
"""
Created on Thu Aug  5 07:42:35 2021

@author: mlin
"""
from cmath import rect
from math import radians
import numpy as np
from numpy import pi
import copy
import matplotlib.pyplot as plt

netlist = '''
L2 net_1 net_6 1e-08
V4 net_1 0 SIN(0 0 1000000000 0 0 0) AC 1 0
R8 0 net_3 10 
C11 net_4 net_6 1e-11
C14 net_6 net_1 1e-10
R17 0 net_4 10 
L20 net_6 net_3 1e-08
C23 net_7 net_3 1e-11
L26 0 net_7 1e-08
'''

current = {}
nodes = {}
for i in netlist.splitlines():
    if not i.strip():
        continue
    if i[0] == 'R':
        cmp_name, node1, node2, resistance = i.split()
        current[cmp_name] = (node1, node2, '-{}'.format(resistance))
    elif i[0] == 'L':
        cmp_name, node1, node2, inductance = i.split()
        current[cmp_name] = (node1, node2, '-1j*w*{}'.format(inductance))
    elif i[0] == 'C':
        cmp_name, node1, node2, capacitance = i.split()
        current[cmp_name] = (node1, node2, '-1/(1j*w*{})'.format(capacitance))
    elif i[0] == 'V':
        cmp_name, node1, node2, *_, mag, phase = i.split()
        current[cmp_name] = (node1, node2, str(rect(float(mag), radians(float(phase)))))
    else:
        continue

    for node, p in [(node1, '1'), (node2, '-1')]:
        try:
            nodes[node] += [(cmp_name, p)]
        except:
            nodes[node] = [(cmp_name, p)]

del (nodes['0'])
unknown_name = list(nodes.keys()) + list(current.keys())

unknown_array = []
for name in unknown_name:
    if name[0] == 'V':
        unknown_array.append(current[name][2])
    else:
        unknown_array.append('0')

matrix = []
for name1 in unknown_name:
    row = ['0'] * len(unknown_name)
    if name1 in nodes:
        for branch, polarization in nodes[name1]:
            row[unknown_name.index(branch)] = polarization
        matrix.append(row)
    else:
        node1, node2, value = current[name1]
        if name1[0] == 'V':
            value = '0'
        row[unknown_name.index(name1)] = value

        if node1 in unknown_name:
            row[unknown_name.index(node1)] = '1'
        if node2 in unknown_name:
            row[unknown_name.index(node2)] = '-1'
        matrix.append(row)

result = []
evaluated_matrix = copy.deepcopy(matrix)
freq = np.arange(0.1, 1e9 + 1e6, 1e6)
for f in freq:
    w = 2 * pi * f

    for i in range(len(unknown_name)):
        for j in range(len(unknown_name)):
            evaluated_matrix[i][j] = eval(matrix[i][j])

    A = np.array(evaluated_matrix)
    B = np.array([eval(i) for i in unknown_array])
    D = np.linalg.inv(A)
    E = np.dot(D, B)
    result.append(E)

result = list(zip(*result))
plt.plot(freq, [abs(i) for i in result[2]])
# plt.ylim(0, 1)
plt.grid()
plt.show()
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>
