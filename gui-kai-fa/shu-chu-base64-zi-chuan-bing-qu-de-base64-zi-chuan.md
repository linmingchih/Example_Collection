---
description: 這裡的特點便是只透過記憶體傳遞資料，不須透過檔案讀寫便可以在IronPython及Python之間交換資料，可以字串形式傳遞資料或圖片。範例如下：
---

# 輸出Base64字串並取得Base64字串

{% code title="IronPython test1.py" lineNumbers="true" %}
```python
import subprocess
import base64
import os
os.chdir(os.path.dirname(__file__))

os.environ['PATH'] = r'C:\Program Files\AnsysEM\AnsysEM20.2\Win64\commonfiles\CPython\3_7\winx64\Release\python'
proc = subprocess.Popen(['python', 'test2.py', '[1,2,3]', '[1,2,1]'], stdout=subprocess.PIPE, stderr=subprocess.STDOUT)
base64_img = proc.communicate()[0]

print(base64_img)
base64_img_bytes = base64_img.encode('utf-8')
with open('plot.png', 'wb') as file_to_save:
    decoded_image_data = base64.b64decode(base64_img_bytes)
    file_to_save.write(decoded_image_data)
```
{% endcode %}

{% code title="Python test2.py" lineNumbers="true" %}
```python
import matplotlib.pyplot as plt
import io
import sys
x, y = eval(sys.argv[1]), eval(sys.argv[2])

import base64
my_stringIObytes = io.BytesIO()
plt.plot(x, y)
plt.savefig(my_stringIObytes, format='png')
my_stringIObytes.seek(0)
my_base64_jpgData = base64.b64encode(my_stringIObytes.read())
result = str(my_base64_jpgData, encoding = "utf-8")
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>
