# TDR 模擬

{% code lineNumbers="true" %}
```python
#啟動非圖形模式
from pyaedt import Circuit
circuit = Circuit(non_graphical=True)

#網表內容
template = '''
.model diff_line S TSTONEFILE="D:/OneDrive - ANSYS, Inc/Models/S_Parameter/diff_line.s4p"
+ INTERPOLATION=LINEAR INTDATTYP=MA HIGHPASS=10 LOWPASS=10 convolution=0 enforce_passivity=0 Noisemodel=External

S2 net_1 net_2 net_3 net_4 FQMODEL="diff_line"
A3 net_2 net_1 COMPONENT=TDR_Differential_Ended enabled=1
R4 net_3 0 50 
R7 net_4 0 50 

.print TRAN V(*) O(*,z*) 

.end
'''
#輸出template網表到檔案當中
with open('d:/demo/tdr.net', 'w') as f:
    f.write(template)

#將網表加入到電路當中    
oModule = circuit.add_netlist_datablock("D:/demo/tdr.net")

#加上Transient模擬設定並執行模擬
transient = circuit.create_setup('mytransient', circuit.SETUPS.NexximTransient)
transient.props['TransientData'] = ['0.1ns', '20ns']
circuit.analyse_nominal()

#取值並繪圖
data = circuit.post.get_solution_data('O(a3:zdiff)', domain='Time')
data.plot(math_formula='re')

#釋放license
```
{% endcode %}
