---
description: 只要25行，就可以輕鬆建立一個精美的拍頻訊號互動式頁面，布置在任一台電腦上面，就可以從遠端登入網頁使用。
---

# 建立有滑桿，多欄位，Latex方程式的互動式網頁

{% code lineNumbers="true" %}
```python
import streamlit as st
import numpy as np
import matplotlib.pyplot as plt

st.title('Beat Signal Demo')

c1, c2 = st.columns(2)
with c1:
    a1 = st.slider('A1', 0.0, 1.0, 0.5)
    f1 = st.slider('f1(Hz)', 0, 100, 10)

with c2:
    a2 = st.slider('A2', 0.0, 1.0, 0.5)
    f2 = st.slider('f2(Hz)', 0, 100, 20)

time = np.arange(0, 1, 0.001)
x1 = a1 * np.sin(2*np.pi*f1*time)
x2 = a2 * np.sin(2*np.pi*f2*time)

st.latex('f(t) = A1\cdot sin(2\pi f_1 t) + A2\cdot sin(2\pi f_2 t)')
plt.plot(time, x1+x2)
plt.ylabel('f(t)',)
plt.xlabel('t(sec)',)

st.pyplot(plt)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>
