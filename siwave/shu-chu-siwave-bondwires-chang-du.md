# 輸出SIwave Bondwires長度

{% code lineNumbers="true" %}
```python
from math import sqrt

siw_path = oApp.GetActiveProject().GetFilePath()
#siw_path = "D:\Downloads\BEY_044429AA_REV0R70_1215_FOR_SIM_PCIeG4_SL_ChangeL.siw"
with open(siw_path) as f:
    text = f.readlines()

data = None
key = '' 
for line in text:
    if 'B_BONDWIRE_3D_PROFILES_MOD_GEN' in line:
        data = {}
        key = 'else'
        
    elif 'E_BONDWIRE_3D_PROFILES_MOD_GEN' in line:
        break
    
    elif key:
        try:
            _, bid, _, _ = line.split()
            key = bid
            data[int(key)] = []
        except:
            x, y, z = line.split()
            data[int(key)] += [(float(x), float(y), float(z))]

length = {}
for bw, pts in data.items():
    dl = 0
    for n, (x0, y0, z0) in enumerate(pts[:-1]):
        x1, y1, z1 = pts[n+1]
        dl += sqrt((x1-x0)**2 + (y1-y0)**2 + (z1-z0)**2)
    length[bw] = dl

csv_path = siw_path.replace('.siw', '.csv') 
with open(csv_path, 'w') as f:
    for bw in sorted(length):
        l = length[bw]
        f.write('{:>4}: {:.6f}\n'.format(bw, l))
```
{% endcode %}
