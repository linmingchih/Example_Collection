# 如何在AEDT比較不同專案設計的S參數

{% code lineNumbers="true" %}
```python
prj_design = [('bp_filter', 'HFSSDesign1', 'S(2,1)'), 
              ('OptimTee', 'TeeModel', 'S(1,1)') ]


#%%
from pyaedt import Hfss
import matplotlib.pyplot as plt

color = ['r', 'b', 'g', 'y', 'm', 'c']

cases = []
for prj, design, quantity in prj_design:
    cases.append((Hfss(projectname=prj, designname=design, specified_version='2022.1',), quantity))

#%%

fig, ax = plt.subplots(subplot_kw={'projection': 'polar'})
for (i, q), c in zip(cases, color):
    result = i.post.get_report_data(f'polar({q})')
    ax.plot(result.data_imag(), result.data_real(), color=c)

fig.show()

#%%
fig, ax = plt.subplots()
plt.grid()
for (i, q), c in zip(cases, color):
    result = i.post.get_report_data(f'db({q})')
    ax.plot(result.sweeps['Freq'], result.data_real(), color=c)

fig.show()

#%%
fig, ax = plt.subplots()
plt.grid()
for (i, q), c in zip(cases, color):
    result = i.post.get_report_data(f'ang_deg({q})')
    ax.plot(result.sweeps['Freq'], result.data_real(), color=c)

fig.show()
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>
