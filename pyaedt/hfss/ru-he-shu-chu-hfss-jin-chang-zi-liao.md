# 如何輸出HFSS近場資料

{% code lineNumbers="true" %}
```python
import numpy as np
import matplotlib.pyplot as plt
from pyaedt import Hfss
hfss = Hfss(specified_version='2022.1')

data = hfss.post.get_solution_data_per_variation('Near Fields', 'Setup1 : LastAdaptive', "Rectangle1", expression='NearEX')
print(data.units_data)

x_real = data.solutions_data_real['NearEX'].values()
x_imag = data.solutions_data_imag['NearEX'].values()
data.sweeps['_u']
NearEX = [complex(i, j) for i, j in zip(x_real, x_imag)]

NearEX = np.array(NearEX)
x = np.reshape(np.absolute(NearEX), (len(data.sweeps['_u']), len(data.sweeps['_v'])))

u_min, u_max = min(data.sweeps['_u'])*1000, max(data.sweeps['_u'])*1000
v_min, v_max = min(data.sweeps['_v']), max(data.sweeps['_v'])

plt.xlabel('u')
plt.ylabel('v')
plt.imshow(x, cmap='jet', extent = [u_min, u_max, v_min, v_max])
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (2) (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
