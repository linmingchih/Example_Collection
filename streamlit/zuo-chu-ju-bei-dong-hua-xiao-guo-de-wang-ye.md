---
description: >-
  透過st.empty()，只要以下幾行程式碼就可以做出數學運算的動畫網頁。有趣的是，這個計算是在伺服器完成運算並主動刷新使用者頁面，類似推播的功能。而非由使用者端刷新頁面。
---

# 做出具備動畫效果的網頁

{% code lineNumbers="true" %}
```python
import streamlit as st
import numpy as np
import matplotlib.pyplot as plt

plt.figure(figsize=(6,6))
st.title('Animation')
t = 0
x, y = [], []
n = st.number_input('w:', value=5)
start = st.button('Start!')
container = st.empty()

if start:
    while(True):    
        plt.clf()
        plt.title(f't={round(t,2)}')

        x.append(np.sin(t))
        y.append(np.cos(n*t))

        plt.xlim(-1.2,1.2)
        plt.ylim(-1.2,1.2)
        plt.plot(x, y, color='red')
        container.pyplot(plt)
        t+=0.02
```
{% endcode %}
