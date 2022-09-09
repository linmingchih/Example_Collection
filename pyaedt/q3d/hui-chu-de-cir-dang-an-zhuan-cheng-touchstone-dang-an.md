# 匯出的cir檔案轉成TouchStone檔案

{% code lineNumbers="true" %}
```python
import re, os
from pyaedt import Circuit

circuit = Circuit(non_graphical=True, new_desktop_session='True', close_on_exit=True)


def cirtosnp(cir_path, fstart='0GHz', fstop='1GHz', fpoints=1001):
    with open(cir_path) as f:
        text = f.readlines()

    ports = re.findall(r'\*\snode\s(\d+)\s+(.*)', '\n'.join(text))
    snp_path = cir_path.replace('.cir', f'.s{len(ports)}p')
    netlist = f'.inc "{cir_path}"\n\n'

    for line in text:
        if line.startswith('.subckt'):
            _, cmp, *num = line.strip().split()
            break

    temp = ' '.join([i[1] for i in ports])
    netlist += f'x1 {temp} {cmp}\n'
    netlist_path = os.path.join(circuit.temp_directory, 'temp.cir')

    with open(netlist_path, 'w') as f:
        f.write(netlist)
    circuit.add_netlist_datablock(netlist_path)

    for port_id, port_name in ports:
        circuit.modeler.components.create_interface_port(port_name)

    setup1 = circuit.create_setup()
    setup1.props["SweepDefinition"]["Data"] = f"LINC {fstart} {fstop} {fpoints}"
    circuit.analyze_nominal()
    circuit.export_touchstone(file_name=snp_path)
    circuit.close_project()


cirtosnp("D:\demo\yqopvgihel_400000000.0.cir")
```
{% endcode %}
