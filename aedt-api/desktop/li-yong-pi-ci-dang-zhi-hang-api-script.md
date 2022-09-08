# 利用批次檔執行API Script

{% code title="test.bat" %}
```batch
PATH="C:\Program Files\AnsysEM\v221\Win64\common\IronPython";%PATH%

ipy64 .\test.py
pause
```
{% endcode %}

{% code title="test.py" lineNumbers="true" %}
```python
import sys
sys.path.append(r'C:\Program Files\AnsysEM\v221\Win64\PythonFiles\DesktopPlugin')
sys.path.append(r'C:\Program Files\AnsysEM\v221\Win64')

import ScriptEnv
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oProject = oDesktop.NewProject()
```
{% endcode %}
