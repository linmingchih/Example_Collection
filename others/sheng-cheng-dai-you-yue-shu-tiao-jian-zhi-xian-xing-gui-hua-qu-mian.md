# 生成帶有約束條件之線性規劃曲面

{% code lineNumbers="true" %}
```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits import mplot3d

fig = plt.figure(figsize=(8,6))
ax3d = plt.axes(projection="3d")

def func(x, y):
    result = (3*x+4*y)
    return result

constraint = lambda x, y: (x+y<5)*(x+y>1)

xdata = np.linspace(0,3,100)
ydata = np.linspace(0,3,100)
X,Y = np.meshgrid(xdata,ydata)
Z = func(X, Y)/constraint(X, Y)


ax3d = plt.axes(projection='3d')
ax3d.plot_surface(X, Y, Z,cmap='jet')
ax3d.set_title('Surface Plot in Matplotlib')
ax3d.set_xlabel('X')
ax3d.set_ylabel('Y')
ax3d.set_zlabel('Z')

plt.show()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
