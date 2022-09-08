---
description: >-
  當要檢查大量的電容Z11曲線，直接呼叫nexxim.exe執行AC模擬並繪圖會是一個比較有效率的做法。以下是處理單一一個電容的Python範例。加上迴圈可以一次處理大量的Z11計算及繪圖。
---

# 如何背景執行電容Z11模擬並繪圖

{% code lineNumbers="true" %}
```python
import os
import matplotlib.pyplot as plt
os.chdir(os.path.dirname(__file__))

netlist_template = r'''
.inc "{}"

x1 net_2 0 "{}"
I3 0 net_2 DC=0 AC 1 0

.LNA
+ 100000 100000000 10000
+ FLAG='LNA'

.print AC V(net_2) 
.end'''

model_file = r'D:\demo\CL05A226MQ5QUN_Precise_DC0V_25degC_H.lib'
model_name = 'CL05A226MQ5QUN_Precise_DC0V_25degC'

netlist = netlist_template.format(model_file, model_name)
with open('./ZP.net', 'w') as f:
    f.write(netlist)

os.environ['PATH'] = r'C:\Program Files\AnsysEM\AnsysEM21.2\Win64'
os.system("nexxim ZP.net")
os.system("sdf2csv ZP.net.sdf")

with open("ZP.net.sdf.csv") as f:
    text = f.readlines()
data = []
for line in text[1:]:
    try:
        _, freq, Zre, Zim = line.strip().split(',')
        data.append((float(freq), complex(float(Zre), float(Zim))))
    except:
        pass
freq, Z11 = zip(*data)
plt.plot(freq, [abs(i) for i in Z11])
plt.grid()
plt.show()
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>
