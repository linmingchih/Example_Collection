---
description: 因為目前PyAEDT 3DLayout沒有cutout功能，我仿EDB做了一個。可以不用開啟GUI完成切圖的工作，以下為範例碼。
---

# 3D Layout Cutout

{% code lineNumbers="true" %}
```python
from pyaedt import Hfss3dLayout, Circuit

hfss = Hfss3dLayout(specified_version='2022.2', non_graphical=True)
hfss.import_brd('d:/demo/Galileo_G87173_204.brd')


def cutout(hfss, signal_list=[], reference_list=['GND'], extent_type='Conforming', expansion_size=0.002,
           use_round_corner=False):
    x = []
    for i in signal_list:
        x += ["net:=", [i, False]]
    for i in reference_list:
        x += ["net:=", [i, True]]

    hfss.modeler.oeditor.CutOutSubDesign(
        [
            "NAME:Params",
            "Name:="   , "Test_cutout",
            "CleanupFactor:="  , 0.05,
            "AutoGenExtent:="  , True,
            "Type:="      , extent_type,
            "Expansion:="     , str(expansion_size),
            "RoundCorners:="   , use_round_corner,
            "Increments:="    , 1,
            "UseSelection:="   , False,
            "ExtentSel:="     , [],
            [
                "NAME:Nets",
            ] + x
        ])

cutout(hfss, ['M_DQ<0>', 'M_DQ<1>'], ['GND'], 'Conforming', '2mm')

hfss.save_project('d:/demo/test13.aedt')
hfss.close_desktop()
```
{% endcode %}
