# 一條線周圍等間距加上vias

{% code lineNumbers="true" %}
```python
from math import sin, cos, atan, pi, sqrt, degrees, acos
import matplotlib.pyplot as plt

plt.figure(figsize=(6, 6))

distance = 5
line = [(0, 0), (50, 0), (50, 50), (0, 50), (0, 10), (25, 25)]


def getAngle(v1, v2):
    v1_mag = sqrt(v1[0] ** 2 + v1[1] ** 2)
    v2_mag = sqrt(v2[0] ** 2 + v2[1] ** 2)
    dotsum = v1[0] * v2[0] + v1[1] * v2[1]
    if v1[0] * v2[1] - v1[1] * v2[0] > 0:
        scale = 1
    else:
        scale = -1
    dtheta = scale * acos(dotsum / (v1_mag * v2_mag))
    print(v1, v2)
    print(degrees(dtheta))

    return dtheta


def getParalletLines(pts, distance):
    leftline = []
    rightline = []

    x0, y0 = pts[0]
    x1, y1 = pts[1]
    vector = (x1 - x0, y1 - y0)
    orientation1 = getAngle((1, 0), vector)

    leftturn = orientation1 + pi / 2
    righrturn = orientation1 - pi / 2
    leftPt = (x0 + distance * cos(leftturn), y0 + distance * sin(leftturn))
    leftline.append(leftPt)
    rightPt = (x0 + distance * cos(righrturn), y0 + distance * sin(righrturn))
    rightline.append(rightPt)

    for n in range(1, len(pts) - 1):
        print('hello')
        x0, y0 = pts[n - 1]
        x1, y1 = pts[n]
        x2, y2 = pts[n + 1]

        v1 = (x1 - x0, y1 - y0)
        v2 = (x2 - x1, y2 - y1)
        dtheta = getAngle(v1, v2)
        orientation1 = getAngle((1, 0), v1)
        print(degrees(dtheta))

        leftturn = orientation1 + dtheta / 2 + pi / 2
        righrturn = orientation1 + dtheta / 2 - pi / 2

        distance2 = distance / sin((pi - dtheta) / 2)
        leftPt = (x1 + distance2 * cos(leftturn), y1 + distance2 * sin(leftturn))
        leftline.append(leftPt)
        rightPt = (x1 + distance2 * cos(righrturn), y1 + distance2 * sin(righrturn))
        rightline.append(rightPt)

    x0, y0 = pts[-2]
    x1, y1 = pts[-1]

    vector = (x1 - x0, y1 - y0)
    orientation1 = getAngle((1, 0), vector)
    leftturn = orientation1 + pi / 2
    righrturn = orientation1 - pi / 2
    leftPt = (x1 + distance * cos(leftturn), y1 + distance * sin(leftturn))
    leftline.append(leftPt)
    rightPt = (x1 + distance * cos(righrturn), y1 + distance * sin(righrturn))
    rightline.append(rightPt)
    return leftline, rightline


def getLocations(line, gap):
    location = [line[0]]
    residual = 0

    for n in range(len(line) - 1):
        x0, y0 = line[n]
        x1, y1 = line[n + 1]
        length = sqrt((x1 - x0) ** 2 + (y1 - y0) ** 2)
        dx, dy = (x1 - x0) / length, (y1 - y0) / length
        x = x0 - dx * residual
        y = y0 - dy * residual
        length = length + residual
        while length >= gap:
            x += gap * dx
            y += gap * dy
            location.append((x, y))
            length -= gap

        residual = length

    return location


plt.plot(*zip(*line), c='black', linewidth=3)
leftline, rightline = getParalletLines(line, 2)
plt.plot(*zip(*leftline), c='r')
plt.plot(*zip(*rightline), c='b')

locations = getLocations(rightline, 8)
plt.scatter(*zip(*locations), s=20)

locations = getLocations(leftline, 8)
plt.scatter(*zip(*locations), s=20)

plt.show()
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>
