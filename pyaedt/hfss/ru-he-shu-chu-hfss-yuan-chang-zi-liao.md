---
description: 以下方法可以用在HFSS任何遠場物理量輸出，使用np.ndarray可以加快矩陣運算速度。
---

# 如何輸出HFSS遠場資料

{% code lineNumbers="true" %}
```python
import numpy as np
import matplotlib.pyplot as plt
from pyaedt import Hfss
hfss = Hfss(specified_version='2022.1')

data = hfss.post.get_far_field_data(['rETheta', 'rEPhi'], 
                                    'Setup1 : LastAdaptive', 
                                    '3D')
print(data.units_data)

x_real = data.solutions_data_real['rETheta']
x_imag = data.solutions_data_imag['rETheta']
rETheta = [complex(x_real[i], x_imag[i]) for i in x_real]


x_real = data.solutions_data_real['rEPhi']
x_imag = data.solutions_data_imag['rEPhi']
rEPhi = [complex(x_real[i], x_imag[i]) for i in x_real]

rEPhi = np.array(rEPhi)
x = np.reshape(np.absolute(rEPhi), (361, 181))

plt.xlabel('Phi')
plt.ylabel('Theta')
plt.imshow(x.T, cmap='jet')
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (1) (4) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
