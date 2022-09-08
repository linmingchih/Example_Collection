# \_\_getattr\_\_()方法動態定義屬性

在初始化物件時，可以先行計算屬性，之後取用時便不需另行計算。但是如果之後沒有用到，之前計算所耗用的資源豈不是浪費？一個比較理想的做法是在用到的時候再計算，並將計算結果儲存起來，等到第二次需要時直接讀取即可。 話雖如此，但是這個機制要如何實做到代碼當中？

在Python當中我們可以使用\_\_getattr\_\_()方法來達到這個目的。首先在初始化時先不要定義屬性。當這個屬性被程式使用到的時候，因為屬性不存在於物件當中，**getattr**()會被呼叫，我們便可以在\_\_getattr\_\_()方法當中進行屬性的計算及動態宣告。當再次使用這個屬性時，因為屬性已經存在，便會被直接取用。

這裡比較兩次讀取屬性所花的時間，第一次讀取時屬性還不存在，需先行計算並儲存。第二次讀取屬性時便直接讀取先前儲存的數值。由下面例子可以看到時間大幅的縮短。如果屬性沒有被用到，自然也不會有計算成本的開銷。

{% code lineNumbers="true" %}
```python
```
{% endcode %}