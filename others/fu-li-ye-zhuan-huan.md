---
description: 時域訊號經過傅立葉轉換到頻域，再轉回時域做比較
---

# 傅立葉轉換

{% code lineNumbers="true" %}
```python
import numpy as np

tstop = 10
tstep = 0.01

fstart = -1 / tstep / 2
fstop = 1 / tstep / 2
fstep = 1 / tstop

t = np.arange(0, tstop, tstep)
y0 = np.sin(20 * t)

import matplotlib.pyplot as plt

scale = np.sqrt(2 * np.pi * len(t))
plt.plot(t, y0)
plt.show()
y1 = []
f = np.arange(fstart, fstop, fstep)
for w in f:
    y1.append(np.sum(np.exp(-1j * w * t) * y0) / scale)

plt.plot(f, np.abs(y1))
plt.show()

y2 = []
for t0 in t:
    y2.append(np.sum(np.exp(1j * t0 * f) * y1) / scale)

plt.plot(t, np.real(y2))
plt.show()

plt.plot(t, y0)
plt.plot(t, y2, c='r', linewidth=1)
plt.show()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>
