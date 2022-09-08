---
description: main.py為主頁面，透過main.py可以連接到不同App。
---

# 一次啟動多個App

{% code title="run.bat" lineNumbers="true" %}
```batch
set PATH=C:\Users\mlin\AppData\Local\Programs\Python\Python38\Scripts

start streamlit run .\main.py --server.port 8050
start streamlit run .\case1.py --server.port 8051
start streamlit run .\case2.py --server.port 8052
start streamlit run .\case3.py --server.port 8053
```
{% endcode %}

{% code title="main.py" lineNumbers="true" %}
```python
import os
import streamlit as st
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
s.connect(("8.8.8.8", 80))
ip = s.getsockname()[0]
s.close()

st.title('main')
st.markdown(f'[case1](http://{ip}:8051)')
st.markdown(f'[case2](http://{ip}:8052)')
st.markdown(f'[case3](http://{ip}:8053)')
```
{% endcode %}
