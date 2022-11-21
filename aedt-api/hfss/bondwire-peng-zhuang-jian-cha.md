---
description: 在XY平面有交叉的bondwire有效
---

# Bondwire碰撞檢查

```python
from math import sqrt
from win32com import client
from itertools import combinations

oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()

oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.SetActiveEditor("3D Modeler")

class bondwire:
    def __init__(self, obj_name):
        self.obj_name = obj_name
        self.get_pts()

    def getCenter(self, vertexes):
        xs, ys, zs = [], [], []
        
        for v in vertexes:
            x, y, z = [float(i) for i in oEditor.GetVertexPosition(v)]
            xs.append(x)
            ys.append(y)
            zs.append(z)
        
        x0, y0, z0 = sum(xs)/len(xs), sum(ys)/len(ys), sum(zs)/len(zs)
        
        distance = []
        for x, y, z in zip(xs, ys, zs):
            r = sqrt((x-x0)**2 + (y-y0)**2 + (z-z0)**2)
            distance.append(r)
        return x0, y0, z0, min(distance)

    def get_pts(self):
        faces = oEditor.GetFaceIDs(self.obj_name)
        
        data = []
        for j in faces:
            data.append((len(oEditor.GetVertexIDsFromFace(j)), j))
        data.sort()
        
        edge_info = {e:oEditor.GetVertexIDsFromEdge(e) for e in oEditor.GetEdgeIDsFromObject(self.obj_name)}
        
        face0 = data[-1][1]
        face_vertexes = oEditor.GetVertexIDsFromFace(face0)
        
        self.pts = []
        rs = []
        while face_vertexes:
            x, y, z , r = self.getCenter(face_vertexes)
            self.pts.append((x, y, z))        
            rs.append(r)
            
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
        self.radius = min(rs)

    def check_intersect(self, lines):
        id_dic = {j:i for i,j in list(enumerate(lines))}    
        
        def ccw(A, B, C):
            return (C[1]-A[1])*(B[0]-A[0]) > (B[1]-A[1])*(C[0]-A[0])
        
        def intersect(A,B,C,D):
            return ccw(A,C,D) != ccw(B,C,D) and ccw(A,B,C) != ccw(A,B,D)
        
        result = []
        for i, j in combinations(lines, 2):
            (x0, y0, x1, y1), (x2, y2, x3, y3) = (i, j)
            if intersect((x0, y0), (x1, y1), (x2, y2), (x3, y3)):
                result.append((id_dic[i], id_dic[j]))
        
        return result

    def calculate_shortest_distance(self, p1, p2, p3, p4):
        x1, y1, z1 = p1
        a1, b1, c1 = [i-j for i, j in zip(p2, p1)]
        x2, y2, z2 = p3
        a2, b2, c2 = [i-j for i, j in zip(p4, p3)]
        d = ((((x2-x1)*b1*c2 + (z2-z1)*a1*b2 + (y2-y1)*a2*c1) - 
             ((z2-z1)*a2*b1 + (x2-x1)*c1*b2 + (y2-y1)*a1*c2))/
             sqrt((b1*c2-b2*c1)**2+(c1*a2-c2*a1)**2+(a1*b2-a2*b1)**2))
    
        return abs(d)
    
    
    def check_collision(self, other):
        for m, (x0, y0, z0) in enumerate(self.pts):
            if m < len(self.pts)-1:
                x1, y1, z1 = self.pts[m+1]
                for n, (x2, y2, z2) in enumerate(other.pts):
                    if n < len(other.pts)-1:
                        x3, y3, z3 = other.pts[n+1]
                        if self.check_intersect([(x0, y0, x1, y1), (x2, y2, x3, y3)]):
                            gap = self.calculate_shortest_distance((x0, y0, z0), (x1, y1, z1), (x2, y2, z2), (x3, y3, z3))
                            if gap < self.radius + other.radius:
                                return True
        return False
                        
        
        
    
b1 = bondwire('Net_59_bwire_0')
b2 = bondwire('Net_16_bwire_2')
print(b1.pts)
print(b1.radius)
print(b1.check_collision(b2))


```
