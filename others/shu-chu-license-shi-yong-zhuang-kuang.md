---
description: 可以讀取ANSYS License Server的logs_backup目錄並輸出一段期間License的使用狀況
---

# 輸出License使用狀況

{% code lineNumbers="true" %}
```python
import os
import codecs
import logging
from datetime import datetime, timedelta

FORMAT = '%(asctime)s %(levelname)s: %(message)s'
logging.basicConfig(filename='simulation.log', filemode='w')

path = r'D:\demo\logs_backup'
data = []
pair = {}
Nin, Nout = 0, 0
for file in os.listdir(path):
    with codecs.open(os.path.join(path, file), 'r', 'utf-8', errors='ignore') as f:
        text = f.readlines()

    for line in text:
        if len(line.strip()) == 0:
            continue

        if '(lmgrd) TIMESTAMP' in line:
            date = datetime.strptime(line.strip().split()[-1], '%m/%d/%Y')

        if 'date' not in locals():
            continue

        x = line.strip().split()[0]

        if not x.count(':') == 2:
            continue

        time = datetime.strptime(x, '%H:%M:%S').time()
        ts = datetime.combine(date, time)

        if 't0' not in locals():
            t0 = ts
            continue
        else:
            if (ts - t0).total_seconds() < 0:
                date += timedelta(days=1)
            t0 = datetime.combine(date, time)

        try:
            time_stamp, _, status, increment, userandserver, pid = line.strip().split()[0:6]
            if status not in ['OUT:', 'IN:']:
                raise Exception
        except:
            continue

        user, server = userandserver.split('@')
        try:
            if status == 'OUT:':
                pair[(user, server, increment, pid)] = t0
            elif status == 'IN:':
                tout = pair[(user, server, increment, pid)]
                tin = t0
                data.append((user, increment, server, str(tout), str(tin), str(tin - tout)))
        except:
            logging.exception('')

with open('d:/demo/usage.csv', 'w') as f:
    width = '{:12}\t{:30}\t{:30}\t{:25}\t{:25}\t{:25}\n'
    title = ['USER', 'License', 'Machine', 'Tstart', 'Tstop', 'Duration']
    f.writelines(width.format(*title))

    for item in data:
        f.writelines(width.format(*item))
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>
