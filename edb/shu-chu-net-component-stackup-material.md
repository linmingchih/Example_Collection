# 輸出net, component, stackup, material

{% code lineNumbers="true" %}
```python
import clr, os, sys, System
AnsysEM_Path = 'C:/Program Files/AnsysEM/AnsysEM20.2/Win64/'
sys.path.append(AnsysEM_Path)
os.environ['PATH'] += ';' + AnsysEM_Path

clr.AddReference('Ansys.Ansoft.Edb')
clr.AddReference('Ansys.Ansoft.SimSetupData')
import Ansys.Ansoft.Edb as edb

edb.Database.SetRunAsStandAlone(True)

DB = edb.Database.Open('D:/demo4/test.aedb', False)

cell = list(DB.TopCircuitCells)
layout = cell[0].GetLayout()

def getNetInfo(layout):
    net_info={}
    for n in layout.Nets:
        netname=n.GetName()
        net_info[netname]={}
        for i in n.PadstackInstances:
            if len(i.GetName())==0:
                continue
            try:
                net_info[netname][i.GetComponent().GetName()]+=[i.GetName()]
            except:
                net_info[netname][i.GetComponent().GetName()]=[i.GetName()]

    return net_info

def getComponentInfo(layout):
    comp_info=[]
    for c in layout.Groups:
        x = ( c.GetName(),
              c.GetComponentDef().GetName(),
              c.GetComponentType().ToString(),
              c.GetNumberOfPins(),
              c.GetPlacementLayer().GetName(),
              )
        comp_info.append(x)

    return comp_info

def get_layer_roughness(layer):
    roughness_info={}
    region_type = { 'Top': edb.Cell.RoughnessModel.Region.Top,
                    'Bottom': edb.Cell.RoughnessModel.Region.Bottom,
                    'Side': edb.Cell.RoughnessModel.Region.Side}

    for i in region_type:
        model = layer.GetRoughnessModel(region_type[i])
        roghness_name = type(model).__name__
        if roghness_name == 'HurrayRoughnessModel':
            roughness_info[i] = ('HurrayRoughnessModel', float(model.NoduleRadius), float(model.SurfaceRatio))
        elif roghness_name == 'GroisseRoughnessModel':
            roughness_info[i] = ('GroisseRoughnessModel', float(model.Roughness))
        else:
            pass
    return roughness_info

def getStackupInfo(layout):
    lc = layout.GetLayerCollection()

    layer_info = []
    for i in lc.Layers(edb.Cell.LayerTypeSet.AllLayerSet):
        if i.IsStackupLayer():
            x = (i.GetName(),
                 i.GetThickness(),
                 i.GetMaterial(),
                 i.GetFillMaterial(),
                 i.IsRoughnessEnabled(),
                 float(i.GetEtchFactor()),
                 get_layer_roughness(i)
                 )
            layer_info.append(x)

    return layer_info

def getMaterialInfo(database):
    material_info={}
    for i in database.MaterialDefs:
        for p in i.GetAllProperties():
            if p.ToString().isnumeric():
                continue
            v = clr.Reference[float]()
            i.GetProperty(p, v)
            try:
                material_info[i.GetName()] += [(p.ToString(), v.ToString())]
            except:
                material_info[i.GetName()] = [(p.ToString(), v.ToString())]

    return material_info

print(getMaterialInfo(DB))
print(getNetInfo(layout))
print(getComponentInfo(layout))
print(getStackupInfo(layout))
```
{% endcode %}
