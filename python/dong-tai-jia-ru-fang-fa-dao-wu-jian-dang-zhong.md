# 動態加入方法到物件當中

{% code lineNumbers="true" %}
```python
class foo:
    def __init__(self, v):
        self.value = v


def new_method(self, v=None):
    self.value += v


x = foo(3)
setattr(x, 'add', lambda v: new_method(x, v))

x.add(4)
print(x.value)
```
{% endcode %}

```
7
```
