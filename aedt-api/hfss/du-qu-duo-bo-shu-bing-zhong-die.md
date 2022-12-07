# 讀取多波束並重疊

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

{% code lineNumbers="true" %}
```python
from collections import OrderedDict
from math import sin, cos, radians, sqrt, degrees, atan
from cmath import polar
import sys
import clr
clr.AddReference("System.Windows.Forms")

from System.Windows.Forms import DialogResult, OpenFileDialog, SaveFileDialog

def split(x):
    for i in range(len(x), 0, -1):
        try:            
            return float(x[:i]), x[i:]
        except:
            pass

oDesktop.ClearMessages("", "", 2)
dialog = OpenFileDialog()
dialog.Multiselect = True
dialog.Title = "Select Beams"
dialog.Filter = "csv files (*.csv)|*.csv"

if dialog.ShowDialog() == DialogResult.OK:
    csv_files = dialog.FileNames
    excitations = OrderedDict()
    
    total_power = 0
    for file in csv_files:
        with open(file) as f:
            text = f.readlines()
            header = text[0].strip()
            
            for line in text[1:]:
                port, mag, phase = line.strip().split(',')
                mag, mag_unit = split(mag)
                phase, phase_unit = split(phase)

                if phase_unit == 'deg':
                    phase = radians(phase)
                    
                if port not in excitations:
                    excitations[port] = [(mag, phase)]
                else:
                    excitations[port] += [(mag, phase)]
                total_power += mag
    
    new_power = 0
    result = []
    for port, combination in excitations.items():
        v_real, v_imag = 0, 0
        
        for mag, phase in combination:
            v_real += mag*cos(phase)
            v_imag += mag*sin(phase)
            
        value, angle = polar(v_real + 1j*v_imag)
                  
        if phase_unit == 'deg':
            angle = degrees(angle)
            
        result.append((port, value, mag_unit, angle, phase_unit))
        new_power += value
    
    AddWarningMessage(str(total_power))
    AddWarningMessage(str(new_power))
    result = [header] + ['{},{}{},{}{}'.format(port, value*(total_power/new_power), mag_unit, angle, phase_unit) for port, value, mag_unit, angle, phase_unit in result]
    
    dialog = SaveFileDialog()
    dialog.Filter = "csv file (*.csv)|*.csv"
    
    if dialog.ShowDialog() == DialogResult.OK:
        csv_path = dialog.FileName
        with open(csv_path, 'w') as f:
            f.write('\n'.join(result))
            
        AddWarningMessage('{} is saved!'.format(csv_path))
    else:
        pass
else:
    pass
```
{% endcode %}
