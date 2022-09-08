# 抓取疊層相關資料

{% code lineNumbers="true" %}
```python
import clr, os, sys, System, json
AnsysEM_Path = 'C:/Program Files/AnsysEM/AnsysEM20.2/Win64/'
sys.path.append(AnsysEM_Path)
os.environ['PATH'] += ';' + AnsysEM_Path

clr.AddReference('Ansys.Ansoft.Edb')
clr.AddReference('Ansys.Ansoft.SimSetupData')
import Ansys.Ansoft.Edb as edb

edb.Database.SetRunAsStandAlone(True)

DB = edb.Database.Open('D:/demo/test.aedb', False)

cell = list(DB.TopCircuitCells)
layout = cell[0].GetLayout()

x = layout.GetLayerCollection()
zids =x.GetZoneIds()
print(zids)
y=[]
y = x.Layers([-2, 20])

layer_type ={'SignalLayer': "signal",
             'DielectricLayer': "dielectric",
             'MeasureLayer': "measures",
             'OutlineLayer': "outline",
             'SIwaveHFSSSolverRegions': "SIwave HFSS regions",
             'AirlinesLayer': "rat",
             'ErrorsLayer': "error",
             'SymbolLayer': "symbol",
             'PostprocessingLayer': "Postprocessing",
             'AssemblyLayer': "assembly",
             'SilkscreenLayer': "silkscreen",
             'SolderPasteLayer': "solderpaste",
             'GlueLayer': "glue",
             'UserLayer': "user",
            }
tb_type = {0: 'top', 1: 'neither', 2:'bottom'}
result = []
for i in y:
    ltype = layer_type[str(i.GetLayerType())]
    if ltype in ['signal', 'dielectric']:
        info = ["NAME:stackup layer"]
    else:
        info = ["NAME:layer"]
    info += ["Name:=", i.GetName()]
    info += ["ID:=", i.GetLayerId()]

    info += ["Type:=", ltype]
    info += ["Top Bottom:=", tb_type[int(i.GetTopBottomAssociation())]]
    R, G, B = i.GetColor()
    colorcode = int(R) + int(G)*(256) + int(B)*(256**2)
    info += ["Color:=", colorcode]
    info += ["Transparency:=", i.GetTransparency()]
    info += ["Pattern:=", 1]
    info += ["VisFlag:=", int(i.GetVisibilityMask())]
    info += ["Locked:=", i.GetLocked()]
    info += ["DrawOverride:=", int(i.GetDrawOverride())]
    inzone =[id for id in zids if i.IsInZone(id)]
    info += ["Zones:=", inzone]

    if ltype in ['signal', 'dielectric']:
        values = ["NAME:Sublayer",]
        values += ["Thickness:=", str(i.GetThicknessValue())]
        values += ["LowerElevation:=", str(i.GetLowerElevationValue())]
        values += ["Roughness:=", "0um"]
        values += ["BotRoughness:=", "0um"]
        values += ["SideRoughness:=", "0um"]
        values += ["Material:=", i.GetMaterial()]
        if ltype == 'signal':
            values += ["FillMaterial:=", i.GetFillMaterial()]
        info.append(values)

        if i.GetNegative():
            info += ["Neg:=", True]

        if i.GetUseSolverProperties():
            info += ["Usp:=", True]
            solver = i.GetHFSSSolverProperties()
            solveinside = 'true' if solver.SolveInside else 'false'
            dc_type = int(solver.DCType)
            dc_thickness = str(solver.DCThickness)
            s1 = [
                "NAME:Sp",
                "Sn:="       , "HFSS",
                "Sv:="       , "so(si={}, dt={}, dtv=\'{}\')".format(solveinside, dc_type, dc_thickness)
            ]
            s2= [
                "NAME:Sp",
                "Sn:="       , "PlanarEM",
                "Sv:="       , "so(ifg=false, vly=false)"
            ]
            info.append(s1)
            info.append(s2)
        if i.IsEtchFactorEnabled():
            info += ["Etch:=", str(i.GetEtchFactor())]
            info += ["UseEtch:=", True]

        if i.IsRoughnessEnabled():
            info += ["UseR:=", str(i.IsRoughnessEnabled())]
            location = [(edb.Cell.RoughnessModel.Region.Top, "RMdl:=", "NR:=", "HRatio:=", 6),
                        (edb.Cell.RoughnessModel.Region.Bottom, "BRMdl:=", "BNR:=", "BHRatio:=", 8),
                        (edb.Cell.RoughnessModel.Region.Side, "SRMdl:=", "SNR:=", "SHRatio:=", 10),
                        ]
            for loc, loc_name, nodule_radius, surface_ratio, rough_index in location:
                model = i.GetRoughnessModel(loc)

                if type(model).__name__ == 'HurrayRoughnessModel':
                    info += [loc_name, "Huray"]
                    info += [nodule_radius, str(model.NoduleRadius)]
                    info += [surface_ratio, str(model.SurfaceRatio)]
                else:
                    info += [loc_name, "Groisse"]
                    info += [nodule_radius, "0um"]
                    info += [surface_ratio, "1"]
                    values[rough_index] = str(model.Roughness)

    result.append(info)

with open('d:/demo/testing.txt', 'w') as f:
    for i in result:
        f.writelines( str(i) + '\n')
        print(str(i))
```
{% endcode %}

