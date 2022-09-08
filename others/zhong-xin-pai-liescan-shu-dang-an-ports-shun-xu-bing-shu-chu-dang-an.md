# 重新排列S參數檔案ports順序並輸出檔案

{% code lineNumbers="true" %}
```python
import re, itertools

class SNP:
    def __init__(self, touchstone_path):
        with open(touchstone_path) as f:
            text = f.readlines()

        self.data = []
        self.ports = {}
        for line in text:
            try:
                [float(i) for i in line.strip().split()]
                self.data += line.strip().split()
            except:
                if line.startswith('#'):
                    self.header = line
                else:
                    m = re.search('!\sPort\[(\d+)\]\s=\s(.*)$', line)
                    if m:
                        num = int(m.group(1))
                        name = m.group(2)
                        self.ports[num] = name

    def reorder(self, order, touchstone_path):
        num = len(self.ports)
        frequencies = self.data[::2 * num ** 2 + 1]

        matrix = list(itertools.product(order, repeat=2))
        new_touchstone = [self.header]

        touchstone_path += '.s{}p'.format(len(order))
        print('\n' + touchstone_path)
        for n, i in enumerate(order, 1):
            port = '! Port[{}] = {}'.format(n, self.ports[i])
            print(port)
            new_touchstone.append(port)

        for n, f in enumerate(frequencies):
            line = [self.data[n * (num ** 2 * 2 + 1)]]
            for i, j in matrix:
                i -= 1
                j -= 1
                line.append(self.data[n * (num ** 2 * 2 + 1) + 1 + (2 * num * i) + (2 * j)])
                line.append(self.data[n * (num ** 2 * 2 + 1) + 1 + (2 * num * i) + (2 * j + 1)])

            new_touchstone.append(' '.join(line))

        with open(touchstone_path, 'w') as f:
            f.write('\n'.join(new_touchstone))


snp = SNP(r"D:\demo\dq.s32p")
snp.ports
snp.reorder((23, 9), 'd:/demo/dq0')
snp.reorder((21, 11), 'd:/demo/dq1')
snp.reorder((24, 10), 'd:/demo/dq2')
snp.reorder((19, 12), 'd:/demo/dq3')
snp.reorder((17, 14), 'd:/demo/dq4')
snp.reorder((20, 16), 'd:/demo/dq5')
snp.reorder((18, 13), 'd:/demo/dq6')
snp.reorder((22, 15), 'd:/demo/dq7')
snp.reorder((31, 1), 'd:/demo/dq8')
snp.reorder((27, 3), 'd:/demo/dq9')
snp.reorder((29, 2), 'd:/demo/dq10')
snp.reorder((26, 4), 'd:/demo/dq11')
snp.reorder((32, 6), 'd:/demo/dq12')
snp.reorder((25, 8), 'd:/demo/dq13')
snp.reorder((30, 5), 'd:/demo/dq14')
snp.reorder((28, 7), 'd:/demo/dq15')
```
{% endcode %}
