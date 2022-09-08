# 類別繼承基本

{% code lineNumbers="true" %}
```python
class mylist(list):
    def __init__(self, x):
        super().__init__(x)

    def multiply(self, x):
        temp = [i for i in self]
        self.clear()
        for i in temp:
            self.append(i * x)


x = mylist([1, 2, 3])
x.append(4)
x.multiply(3)
print(x)
```
{% endcode %}
