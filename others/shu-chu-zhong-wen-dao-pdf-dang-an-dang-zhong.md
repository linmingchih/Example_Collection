# 輸出中文到PDF檔案當中

{% code lineNumbers="true" %}
```python
# -*- coding: utf-8 -*-
from fpdf import FPDF

pdf = FPDF()
pdf.add_page()

pdf.add_font('kaiu', '', fname='c:/windows/fonts/kaiu.ttf', uni=True)
pdf.set_font('kaiu', size = 15)
 
pdf.multi_cell(200, 10, txt = '哈囉，世界。', align = 'C')

pdf.output('d:/demo/result.pdf') 
```
{% endcode %}

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
