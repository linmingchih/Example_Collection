# 類別屬性檢查

{% code lineNumbers="true" %}
```python
class test:
    def __init__(self):
        self._score = 60

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if value < 0:
            self._score = 0
        if value > 100:
            self._score = 100


x = test()
print(x.score)
x.score = 110
print(x.score)
x.score = -10
print(x.score)
```
{% endcode %}

```
60
100
0
```
