# License Increment簡化

{% code lineNumbers="true" %}
```python
# -*- coding: utf-8 -*-
import streamlit as st

st.header("License Increment輸出工具")
st.markdown("**從自己的license檔案取得與客戶license相同的Increments建立新的license。注意:數量無法改變!**")
large_license = st.file_uploader("上傳自己的ansyslmd.lic")
small_license = st.file_uploader("上傳客戶的ansyslmd.lic")

if large_license and small_license:
    header = []
    license_items = {}

    text = large_license.getvalue().decode("utf-8").splitlines()

    for line in text:
        if line.startswith('INCREMENT'):
            feature = line.split()[1]
            license_items[feature] = [line]
        else:
            try:
                license_items[feature].append(line)
            except:
                header.append(line)

    text = small_license.getvalue().decode("utf-8").splitlines()

    extracted_features = []
    for line in text:
        if line.startswith('INCREMENT'):
            feature = line.split()[1]
            extracted_features.append(feature)

    text = '\n'.join(header) + '\n'
    for feature in extracted_features:
        text += '\n'.join(license_items[feature]) + '\n'
    st.subheader('{} Increments 輸出:'.format(len(extracted_features)))
    st.write(',\n'.join(extracted_features))

    st.download_button('下載檔案', file_name='new_ansyslmd.lic', data=text)
```
{% endcode %}
