---
description: 可用在匯入與內建模型
---

# 取得bondwire軌跡座標點

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

{% code lineNumbers="true" %}
```python
from win32com import client
oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()

oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor("3D Modeler")

def getCenter(vertexes):
    xs, ys, zs = [], [], []
    
    for v in vertexes:
        x, y, z = [float(i) for i in oEditor.GetVertexPosition(v)]
        xs.append(x)
        ys.append(y)
        zs.append(z)
        
    return sum(xs)/len(xs), sum(ys)/len(ys), sum(zs)/len(zs)

def get_pts(obj_name):
    faces = oEditor.GetFaceIDs(obj_name)
    
    data = []
    for j in faces:
        data.append((len(oEditor.GetVertexIDsFromFace(j)), j))
    data.sort()
    
    edge_info = {e:oEditor.GetVertexIDsFromEdge(e) for e in oEditor.GetEdgeIDsFromObject(obj_name)}
    
    face0 = data[-1][1]
    face_vertexes = oEditor.GetVertexIDsFromFace(face0)
    
    pts = []
    while face_vertexes:
        pts.append(getCenter(face_vertexes))        
        temp = []
        new_face_vertexes = []
        
        for e, (v0, v1) in edge_info.items():
            if v0 in face_vertexes and v1 in face_vertexes:
                temp.append(e)
            
            if v0 in face_vertexes and v1 not in face_vertexes:
                new_face_vertexes.append(v1)
                temp.append(e)
            
            if v1 in face_vertexes and v0 not in face_vertexes:
                new_face_vertexes.append(v0) 
                temp.append(e)
        
        face_vertexes = new_face_vertexes
        
        for i in temp:
            del(edge_info[i])
    
    return pts

x = get_pts('Net_52_bwire_0')
print(x)
```
{% endcode %}

{% code overflow="wrap" %}
```
[(-0.5345, -2.02142, 0.282), (-0.5345000000000001, -2.02142, 0.434), (-0.7770619999999999, -2.1687429999999996, 0.434), (-2.475, -3.1999999999999997, 0.161), (-2.475, -3.1999999999999997, 0.15199999999999997)]
```
{% endcode %}
