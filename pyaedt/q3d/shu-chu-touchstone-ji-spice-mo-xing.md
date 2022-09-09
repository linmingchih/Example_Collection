# 輸出Touchstone及SPICE模型

{% code lineNumbers="true" %}
```python
from pyaedt import Q3d
import string, random

design_name = ''.join(random.sample(string.ascii_lowercase, 10))

q3d = Q3d(designname=design_name)
q3d.change_validation_settings(skip_intersections=True)

faces = {}
q3d.modeler.delete(q3d.modeler.object_list)

x1 = q3d.modeler.create_box((-10, 0, 0), (20, 1, 1), matname='copper')
x2 = q3d.modeler.create_box((0, 0, 0), (1, 10, 1), matname='copper')
q3d.modeler.unite([x1, x2])
q3d.assign_net([x1], net_name='net_a', net_type='signal')
faces['net_a'] = [x1.top_face_x, x1.bottom_face_x, x1.top_face_y]

x3 = q3d.modeler.create_box((-10, -2, 0), (15, 1, 1), matname='copper')
x4 = q3d.modeler.create_box((-5, -2, 0), (1, 1, 5), matname='copper')
q3d.modeler.unite([x3, x4])
q3d.assign_net([x3], net_name='net_b', net_type='signal')
faces['net_b'] = [x3.top_face_x, x3.bottom_face_x, x3.top_face_z]

case = {'net_a': ['source', 'source', 'sink'],
        'net_b': ['source', 'source', 'sink']}

for net in case:
    for face, _type, name in zip(faces[net], case[net], ['_A', '_B', '_C']):
        if _type == 'source':
            q3d.assign_source_to_objectface(face, source_name=net + name, net_name=net)
        if _type == 'sink':
            q3d.assign_sink_to_objectface(face, sink_name=net + name, net_name=net)

x = q3d.create_setup()
y = x.add_sweep()
y.props['RangeStart'] = '0GHz'
y.props['RangeEnd'] = '1GHz'
y.props['RangeStep'] = '0.1GHz'
y.update()

q3d.analyze_nominal(num_cores=4)

data = q3d.post.get_solution_data('C(net_a,net_a)')
freqs = [1e9 * i for i in data.primary_sweep_values]

q3d.odesign.ExportNetworkData("", q3d.nominal_sweep, f"D:/demo/{design_name}.s6p", "Original", 50, freqs, "MagPhase", 0)

for freq in freqs:
    q3d.oanalysis.ExportCircuit(q3d.nominal_sweep, "", f"D:/demo/{design_name}_{freq}.cir",
                                [
                                    "NAME:CircuitData",
                                    "MatrixName:=" , "Original",
                                    "NumberOfCells:="  , "1",
                                    "UserHasChangedSettings:=", True,
                                    "IncludeCap:="    , True,
                                    "IncludeCond:="       , True,
                                    [
                                        "NAME:CouplingLimits",
                                        "CouplingLimitType:="  , "None"
                                    ],
                                    "IncludeDCR:="    , False,
                                    "IncudeDCL:="     , False,
                                    "IncludeACR:="    , False,
                                    "IncludeACL:="    , True,
                                    "ADDResistance:="  , True,
                                    "ParsePinNames:="  , False,
                                    "IncludeCPP:="    , False,
                                    [
                                        "NAME:CPPInfo",
                                        "PackageType:="       , "wirebond dieup",
                                        "RelativeCS:="    , "",
                                        "LengthUnits:="       , "mm",
                                        [
                                            "NAME:Pins"
                                        ]
                                    ]
                                ], q3d.project_name, freq)
```
{% endcode %}
