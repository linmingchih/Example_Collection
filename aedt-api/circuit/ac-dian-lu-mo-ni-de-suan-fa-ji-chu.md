# AC電路模擬的算法基礎

圖一是AEDT Designer當中一個簡單的RLC網路模擬及V2的電壓頻率響應。我手動將網表轉成矩陣形式A，邊界條件(1V電壓源)為B。將A做反矩陣運算得到D。D與B做相乘即可以得到\[V1, V2, I1, I2, I3, I4]未知矩陣的解。將V2取出來畫出曲線與AEDT Designer所計算的結果相同。

當中運算量最大的是np.linalg.inv()反矩陣運算。這個例子可以說明為何反矩陣運算在頻域數值模擬當中扮演著非常重要的角色。

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

以下為電壓電流方程組：

$$
V_1=1\\I_1-I_2-I_3=0\\I_3-I_4=0\\V_2-RI_4=0\\V_1-\frac{I_2}{jwC}=0\\V_1-V_2-jwLI_3=0
$$

以下為電壓電流方程式矩陣表示法：

$$
\begin{bmatrix}1 & 0 & 0 & 0 & 0 & 0 
\\0 & 0 & 1 & -1 & -1 & 0 
\\0 & 0 & 0 & 0 & 1 & -1
\\0 & 1 & 0 & 0 & 0 & -R
\\1 & 0 & 0 & \frac{-1}{jwC} & 0 & 0
\\1 & -1 & 0 & 0 & -jwL & 0
\end
{bmatrix}\left(\begin{array}{c}

V_1\\ 
V_2\\
I_1\\
I_2\\
I_3\\
I_4
\end
{array}\right)
=\left(\begin{array}{c}1\\0\\0\\0\\0\\0\end{array}\right)
$$

{% code lineNumbers="true" %}
```python
import numpy as np
from numpy import pi

v2 = []
freq = np.arange(1e6, 1e9 + 1e7, 1e7)
for f in freq:
    w = 2 * pi * f
    R = 100
    Z1 = 1 / (1j * w * 1e-10)
    Z2 = 1j * w * 1e-7
    A = np.array([[1, 0, 0, 0, 0, 0],
                  [0, 0, 1, -1, -1, 0],
                  [0, 0, 0, 0, 1, -1],
                  [0, 1, 0, 0, 0, -R],
                  [1, 0, 0, -Z1, 0, 0],
                  [1, -1, 0, 0, -Z2, 0]])

    B = np.array([1, 0, 0, 0, 0, 0])
    D = np.linalg.inv(A)
    E = np.dot(D, B)
    v2.append(E[1])

import matplotlib.pyplot as plt

plt.plot(freq, [np.abs(i) for i in v2])
plt.ylim(0, 1)
plt.grid()
plt.show()
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (21) (1).png" alt=""><figcaption><p>Python計算結果</p></figcaption></figure>
