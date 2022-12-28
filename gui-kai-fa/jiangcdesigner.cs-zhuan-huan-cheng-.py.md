---
description: 用來將GUI的.cs轉換成.py
---

# 將C# Designer.cs轉換成.py

我們在Visual Studio完成GUI設計時，存檔Form1.Designer.cs。接著便可以用以下方式將其轉換為可以用在AEDT的Python GUI。可以用在Windows/Linux AEDT。

```
python convert.py c:/demo/Form1.Designer.cs c:/demo/gui.py
```

{% code title="convert.py" lineNumbers="true" %}
```python
# coding=UTF-8
import sys
import re
import codecs
from collections import OrderedDict

c_sharp_path = sys.argv[1]
output_path = sys.argv[2]

head = '''# coding=utf-8
import os, re, sys, clr, json, math, logging
os.chdir(os.path.dirname(__file__))
logging.basicConfig(filename='gui.log', filemode='w', encoding='utf-8', level=logging.DEBUG)
clr.AddReference('System.Drawing')
clr.AddReference('System.Windows.Forms')

from System import Drawing, Array, ComponentModel, Diagnostics, IO
from System.Windows import Forms
from System.Drawing import Color
import System.Object as object
import System.String as string
from System.Windows.Forms import DialogResult, OpenFileDialog ,SaveFileDialog, FolderBrowserDialog
#----------------------------------------------------------------------------
try:
    image_path = './ANSYS.png'
    oDesktop.ClearMessages("", "", 2)
    AddWarningMessage('Welcome')    
except:
    pass

#----------------------------------------------------------------------------
class MyForm(Forms.Form):
    def __init__(self):
'''

tail = '''
if __name__ == '__main__':
    try:
        form = MyForm()
        form.ShowDialog()
        form = MyForm()
        form.Dispose()
        #form.Show()
        #oDesktop.PauseScript()
    except:
        logging.exception('ERROR!')
'''

    
with open(c_sharp_path, encoding="utf-8") as f:
    text = f.read()

funcs = []
mapping = OrderedDict()
mapping[';'] = ''
mapping['this'] = 'self'
mapping['true'] = 'True'
mapping['false'] = 'False'
mapping['null'] = 'None'
mapping['System.'] = ''
mapping['Windows.'] = ''
mapping['new '] = ''
mapping['//'] = '#'
mapping[', ((byte)(254))'] = ''
mapping[', ((byte)(0))'] = ''
mapping['(Drawing.FontStyle)'] = ''
mapping['(Forms.AnchorStyles)'] = ''
mapping['(Forms.ColumnHeader)'] = ''
mapping['(ComponentModel.ISupportInitialize)'] = ''

preprocess = []
for line in text.splitlines():
    line = line.strip()
    if 'private void InitializeComponent()' in line:
        preprocess = []
        continue
    if '#endregion' in line:
        break
    if len(line) <= 2:
        continue
    try:
        preprocess.append(line)
    except:
        pass
    
second_process = []
temp = []   
for line in preprocess:
    if line.startswith('//'):
        second_process.append(line)
        continue        
    if line.endswith(';') and temp:
        temp.append(line)
        line = ''.join(temp)
        second_process.append(line)
        temp = []
        continue
    if not line.endswith(';'):
        temp.append(line)
        continue
    second_process.append(line)


third_process = []
for line in second_process:        
    for i in mapping:
        j = mapping[i]
        line = line.replace(i, j)
    
    if 'EventHandler' in line:
        m = re.search('(.*)\s\+=.*\((.*?)\)', line)
        line = '{} += {}\n'.format(m.group(1), m.group(2))
        funcs.append(m.group(2).replace('self.', ''))
    
    line = re.sub('\(\(int\)\(\(\(byte\)\((\d+)\)\)\)\)', '\g<1>', line)
    line = re.sub('(\d+)F', '\g<1>', line)
    line = re.sub('(\d+)D', '\g<1>', line)
    line = re.sub('(.*)decimal\(int\[\] \{(\d+),-?\d+\}\)','\g<1>\g<2>', line)
    line = re.sub("(.*)\(\(Drawing.Image\).*\)", '\g<1>' + 'Drawing.Image.FromFile(image_path)', line)
    third_process.append(line)
    
result = []
for line in third_process:
    if '.AddRange' in line:
        name, data = line.split('.AddRange')        
        m = re.search('{(.*)}', data)
        datalist = m.group(1)
        for i in datalist.split(','):
            line = '{}.Add({})'.format(name, i)
            result.append(line)    
        result.append('#{}.SelectedIndex = 0'.format(name.replace('.Items', '')))
        continue

    elif line.startswith('Forms.TreeNode '):
        x = len('Forms.TreeNode ')
        line = line[x:]
        line = line.replace('Forms.TreeNode[] ', 'Array[Forms.TreeNode]')
        line = line.replace('{', '([').replace('}', '])')
        result.append(line)
    
    elif line.startswith('ComponentModel.ComponentResourceManager '):
        x = len('ComponentModel.ComponentResourceManager ')
        line = line[x:]
        result.append('#' + line)
        
    elif line.startswith('Forms.ListViewItem '):
        x = len('Forms.ListViewItem ')
        line = line[x:]
        line = line.replace('string[] ', 'Array[string]')
        line = line.replace('{', '([').replace('}', '])')
        result.append(line)
    
    elif line.startswith('Forms.ListViewGroup '):
        x = len('Forms.ListViewGroup ')
        line = line[x:]
        result.append(line)
        
    else:
        result.append(line)

append_code = {}
valid_code = {}
valid_funcs = {}

try:
    with open('d:/demo/GUI.py') as f:
        text = f.readlines()
        
    for line in text:
        if '__init__' in line:
            continue
            
        if ' def ' in line:
            m = re.search('def\s+(\w+)', line)
            funckey = m.group(1)
            append_code[funckey] = []
            valid_code[funckey] = 0
            continue
        
        if 'funckey' not in locals():
            continue
        
        if line.isspace():
            append_code[funckey].append(line)
            continue

        if line.strip() == 'pass':
            continue
            
        if "'__main__'" in line :
            break
            
        append_code[funckey].append(line)
        valid_code[funckey] += 1

    for f in append_code:
        if valid_code[f] > 0:
            valid_funcs[f] = ''.join(append_code[f]).rstrip() + '\n'
except:
    pass
    
script_text = head + '\n'.join([' '*8 + i for i in result]) + '\n'

for f in list(set(funcs).union(set(valid_funcs.keys()))):
    script_text += '\n    def {}(self, sender, e):\n'.format(f)
    
    if f in valid_funcs:
        script_text += valid_funcs[f]
    else:
        script_text += ' '*8 + 'pass\n'

script_text += tail

with codecs.open(output_path, 'w', "utf-8") as f:
    f.write(script_text)

print('output: {}'.format(output_path))
```
{% endcode %}

以下是一個轉換出來的GUI範例：

<figure><img src="../.gitbook/assets/image (2) (6).png" alt=""><figcaption></figcaption></figure>
