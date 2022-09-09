# EDB建立PinGroup

{% code lineNumbers="true" %}
```python
from pyaedt import Edb

edb = Edb(edbpath=r"D:\demo\Galileo_G87173_20454.aedb", edbversion='2022.1')
pins = edb.core_components.get_pin_from_component('U2A5', 'GND')

edb.core_components.create_pingroup_from_pins(pins, 'group1')
edb.save_edb_as('d:/demo/test.aedb')
```
{% endcode %}
