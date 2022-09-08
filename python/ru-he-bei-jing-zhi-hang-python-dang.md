---
description: >-
  如果我們想執行一隻Python程式，不會跳出任何視窗，只在背景模式執行，比方說監控電腦狀態並輸出log檔，可以使用pythonw.exe搭配start，就不會跳出任何視窗。
---

# 如何背景執行Python檔

{% code title="test.bat" lineNumbers="true" %}
```batch
path=D:\myvenv\Scripts
start pythonw .\test.py
```
{% endcode %}

{% code title="test.py" lineNumbers="true" %}
```python
import os
import time
os.chdir(os.path.dirname(__file__))

while True:
    with open('test.log', 'a') as f:
        f.writelines(str(time.time()) + '\n')
    time.sleep(1)
```
{% endcode %}

進一步我們可以將該程式(.bat)設定為開機執行。選擇該程式並按Windows鍵+R，跳出下面視窗再按確定即可。

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

