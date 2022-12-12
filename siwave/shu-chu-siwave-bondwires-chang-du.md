# 輸出SIwave Bondwires長度

{% code lineNumbers="true" %}
```python
from math import sqrt

siw_path = oApp.GetActiveProject().GetFilePath()
cmp_path = siw_path.replace('.siw', '.cmp')
oApp.GetActiveProject().ScrExportComponentFile(cmp_path)
csv_path = siw_path.replace('.siw', '.csv')
#%%
with open(siw_path) as f:
    text = f.readlines()
    
profile = {}
key = '' 
for line in text:
    if 'B_BONDWIRE_3D_PROFILES_MOD_GEN' in line:
        key = 'else'
        
    elif 'E_BONDWIRE_3D_PROFILES_MOD_GEN' in line:
        break
    
    elif key:
        try:
            _, bid, _, _ = line.split()
            key = bid
            profile[int(key)] = []
        except:
            x, y, z = line.split()
            profile[int(key)] += [(float(x), float(y), float(z))]    

#%%
net_name = {}
key = ''
for line in text:
    if 'B_CUSTOM_COAX_PORTS' in line:
        key = 'else'
        
    elif 'E_CUSTOM_COAX_PORTS' in line:
        break
    
    elif key:
        try:
            x = line.split()
            net_name[int(x[2].replace('"',''))] = x[3]
        except:
            pass

#%%
pin_info = {}
with open(cmp_path) as f:
    text = f.readlines()
    
for line in text:
    s = line.strip().split()
    try:
        x, y, z = [float(i) for i in s[5:8]]
        ref_des, part, pin_name = s[8:11]
        pin_info[(x, y)] = pin_name
    except:
        pass

#%%
mapping = {}
for i, locations in profile.items():
    x, y, z = locations[0]
    if (x, y) in pin_info:
        pin_name = pin_info[(x, y)]
        mapping[i] = int(pin_name)

            
#%%
length = {}
for i, locations in profile.items():
    dl = 0
    for n, (x0, y0, z0) in enumerate(locations[:-1]):
        x1, y1, z1 = locations[n+1]
        dl += sqrt((x1-x0)**2+(y1-y0)**2+(z1-z0)**2)
    name = net_name[mapping[i]]

    length[(mapping[i], name)] = dl

#%%

n = max([len(j) for (i,j) in length.keys()])
out = '{:>' + str(n) + '}: {:.6f}\n'

with open(csv_path, 'w') as f:
    for i, net_name in sorted(length.keys()):
        print(i)
        f.write(out.format(net_name, length[(i, net_name)]))

oApp.GetActiveProject().ScrAddInfo('{} is saved!'.format(csv_path))
```
{% endcode %}
