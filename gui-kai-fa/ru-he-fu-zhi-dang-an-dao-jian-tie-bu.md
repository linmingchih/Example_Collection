---
description: 之前介紹過複製字串到剪貼簿，這裡是複製檔案到剪貼簿。
---

# 如何複製檔案到剪貼簿

{% code title="copyFileswithTXT.bat" lineNumbers="true" %}
```batch
if not DEFINED IS_MINIMIZED set IS_MINIMIZED=1 && start "" /min "%~dpnx0" %* && exit

SET PATH=C:\Program Files\AnsysEM\AnsysEM20.2\Win64\common\IronPython\
ipy64  copyFileswithTXT.py
```
{% endcode %}

{% code title="copyFileswithTXT.py" lineNumbers="true" %}
```python
# coding=UTF-8
import re
import os
import clr
from shutil import copyfile

clr.AddReference('System.Windows.Forms')
from System.Windows.Forms import Clipboard, MessageBox
import System
paths = System.Collections.Specialized.StringCollection()

input = Clipboard.GetText()
os.system('start d:/demo')

for src in input.splitlines():
    try:
        src = src.strip().strip('"')

        dst = 'd:/demo/{}.txt'.format(os.path.basename(src))
        print(src, dst)
        with open(src) as f:
            text = f.read()
        with open(dst, 'w') as f:
            f.write(text)
        paths.Add(dst)
    except:
        pass

Clipboard.SetFileDropList(paths)
print(paths)
```
{% endcode %}

