---
description: >-
  如果要上傳snp, brd, aedtz, gds, zip,
  png等檔案到遠端伺服器用自動化程式加以處理，以下程式碼可以產生檔案上傳按鈕，選擇檔案即可上傳到伺服器特定路徑存檔。另外也可以選擇副檔名篩選出檔案類型，並選擇檔案下載。
---

# 上傳檔案到遠端streamlit網頁伺服器

{% code lineNumbers="true" %}
```python
import streamlit as st
from os import listdir
from os.path import isfile, join

folder_path = 'D:/Downloads'

st.header('上傳檔案')
with st.form(key='my_form', clear_on_submit=True):
    files = st.file_uploader("上傳檔案", accept_multiple_files=True, )

    if st.form_submit_button(label='Submit'):
        for file in files:
            with open(join(folder_path, file.name), 'wb') as f:
                f.write(file.getbuffer())

st.header('下載檔案')
password = st.text_input('Password', type="password")
if password == '1234':
    ext = []
    files = listdir(folder_path)
    for f in files:
        if isfile(join(folder_path, f)):
            ext.append(f.split('.')[-1])

    ext = list(set(ext))
    x = st.selectbox("extensions", ext, 0)
    filename = st.selectbox('file', [i for i in files if i.endswith(x)])

    with open(join(folder_path, filename), 'rb') as f:
        data = f.read()
    st.download_button('download', data, filename)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
