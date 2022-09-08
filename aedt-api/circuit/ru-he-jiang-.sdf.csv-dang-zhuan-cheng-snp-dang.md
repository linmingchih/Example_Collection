---
description: 如何將.sdf.csv檔轉成sNp檔
---

# 如何將.sdf.csv檔轉成sNp檔

{% code lineNumbers="true" %}
```python
with open ('netlist.cir.sdf.csv') as f:
    header = next(f)
    N = int(math.sqrt(((len(header.split(','))-2)/2)))
    with open('result.s{}p'.format(N), 'w') as fsnp:
        fsnp.writelines('# Hz S RI R 50.000000\n\n')
        for line in f:
            data = line.split(',')
            fsnp.write(' '.join(data[1:]))
```
{% endcode %}
