# 資料結構對應之表格



{% code lineNumbers="true" %}
```python
import pandas as pd
import streamlit as st
from streamlit_autorefresh import st_autorefresh
import json
count = st_autorefresh(interval=1000)

x = {'name':['a','b','c'], 'Year':[4,5,6]}
st.table(pd.DataFrame(x))

y = [{'name':'a', 'Year':4}, {'name':'b', 'Year':5}, {'name':'c', 'Year':6}]
st.table(pd.DataFrame(y))

z = {'John':{'name':'a', 'Year':'4'}, 'Mark':{'name':'b', 'Year':'5'}, 'Eddy':{'name':'c', 'Year':'6'}}
st.table(pd.DataFrame(z))

w = {'John':{'name':'a', 'Year':4}, 'Mark':{'name':'b', 'Year':5}, 'Eddy':{'name':'c', 'Year':6}}
st.table(pd.DataFrame(w).T)
st.dataframe(pd.DataFrame(w).T)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>
