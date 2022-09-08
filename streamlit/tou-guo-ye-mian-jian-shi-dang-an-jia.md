# 透過頁面監視檔案夾

{% code lineNumbers="true" %}
```python
import streamlit as st
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler
from streamlit_autorefresh import st_autorefresh

count = st_autorefresh(interval=1000)

class MyHandler(FileSystemEventHandler):
    def on_created(self, event):
        with open('state.log', 'a') as f:
            f.writelines("on_created: {}\n".format(event.src_path)) 

    def on_deleted(self, event):
        with open('state.log', 'a') as f:
            f.writelines("on_deleted: {}\n".format(event.src_path))

if 'event_handler' not in st.session_state:
    st.session_state.event_handler = MyHandler()
    st.session_state.observer = Observer()
    st.session_state.observer.schedule(st.session_state.event_handler, path='d:/demo', recursive=True)
    st.session_state.observer.start()

try:
    with open('state.log') as f:
        text = f.readlines()
        st.text(''.join(text))
except:
    pass
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>
