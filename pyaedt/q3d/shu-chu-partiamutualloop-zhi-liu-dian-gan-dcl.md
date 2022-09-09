# 輸出Partia/Mutual/Loop直流電感(DCL)

{% code lineNumbers="true" %}
```python
from pyaedt import Q3d

q3d = Q3d()
q3d.change_validation_settings(skip_intersections=True)

q3d['height'] = '10mm'
q3d['length'] = '100mm-2*height'

x = q3d.create_setup('mysetup')
y = x.add_sweep('mysweep')
y.props['RangeStart'] = '0GHz'
y.props['RangeEnd'] = '1GHz'
y.props['RangeStep'] = '0.1GHz'
y.update()

x1 = q3d.modeler.create_polyline([(0, 0, '1mm'),
                                  (0, 0, 'height'),
                                  (0, 'length', 'height'),
                                  (0, 'length', '1mm')],
                                 xsection_type='Circle',
                                 xsection_bend_type='5mm')

_, x2 = q3d.modeler.duplicate_and_mirror(x1, (0, 0, 0), (0, 0, 1))
q3d.assign_net([x1], net_name='net_top', net_type='signal')
q3d.assign_net([x2[0]], net_name='net_bot', net_type='signal')

face = q3d.modeler.get_faceid_from_position((0, 0, '1mm'))
q3d.assign_source_to_objectface(face, source_name='top_source', net_name='net_top')

face = q3d.modeler.get_faceid_from_position((0, 'length', '1mm'))
q3d.assign_sink_to_objectface(face, sink_name='top_sink', net_name='net_top')

face = q3d.modeler.get_faceid_from_position((0, 0, '-1mm'))
q3d.assign_source_to_objectface(face, source_name='bot_source', net_name='net_bot')

face = q3d.modeler.get_faceid_from_position((0, 'length', '-1mm'))
q3d.assign_sink_to_objectface(face, sink_name='bot_sink', net_name='net_bot')

oModule = q3d.odesign.GetModule("ReduceMatrix")
oModule.InsertRM("ReturnPathMatrix", "ReturnPath(\'top_source\')")

result = {}
for h in ['5mm', '10mm', '15mm', '20mm', '25mm']:
    q3d['height'] = h

    q3d.analyze_nominal(num_cores=4)

    data = q3d.post.get_solution_data('DCL(net_bot:bot_source,net_bot:bot_source)')
    partial_L = data.data_real()[0]

    data = q3d.post.get_solution_data('DCL(net_top:top_source,net_bot:bot_source)')
    mutual_L = data.data_real()[0]

    data = q3d.post.get_solution_data_per_variation(soltype="Matrix",
                                                    setup_sweep_name='mysetup : mysweep',
                                                    ctxt="ReturnPathMatrix",
                                                    sweeps=None,
                                                    expression="DCL(net_bot:bot_source,net_bot:bot_source)")
    loop_L = data.data_real()[0]
    result[h] = (partial_L, mutual_L, loop_L)
```
{% endcode %}
