# 找出特定目錄底下最早及最近被修改過的檔案

{% code lineNumbers="true" %}
```python
import os

import datetime
import pathlib

data = []
for path, subdirs, files in os.walk(r'C:\Program Files\AnsysEM\AnsysEM20.2\Win64'):
    for name in files:
        fname = pathlib.Path(os.path.join(path, name))
        mtime = datetime.datetime.fromtimestamp(fname.stat().st_mtime)
        data.append((mtime, fname))
data.sort()
print(data[0])
print(data[-1])
```
{% endcode %}

{% code overflow="wrap" %}
```
(datetime.datetime(1999, 12, 31, 20, 0), WindowsPath('C:/Program Files/AnsysEM/AnsysEM20.2/Win64/common/ROMViewer/resources/app/services/System.Diagnostics.Process.dll'))
(datetime.datetime(2022, 5, 30, 19, 54, 27, 280105), WindowsPath('C:/Program Files/AnsysEM/AnsysEM20.2/Win64/syslib/ACT/GLAD_Wizard.xml'))
```
{% endcode %}
