# 手動排序項目

{% code lineNumbers="true" %}
```python
import time
import streamlit as st


def submit_up(n):
    if n > 0:
        x = st.session_state.waiting_list[n]
        y = st.session_state.waiting_list[n - 1]
        st.session_state.waiting_list[n - 1] = x
        st.session_state.waiting_list[n] = y


def submit_down(n):
    if n < len(st.session_state.waiting_list) - 1:
        x = st.session_state.waiting_list[n]
        y = st.session_state.waiting_list[n + 1]
        st.session_state.waiting_list[n + 1] = x
        st.session_state.waiting_list[n] = y


def submit_top(n):
    st.session_state.waiting_list = [st.session_state.waiting_list.pop(n)] + st.session_state.waiting_list


def submit_bottom(n):
    st.session_state.waiting_list = st.session_state.waiting_list + [st.session_state.waiting_list.pop(n)]


def delete(n):
    st.session_state.waiting_list.pop(n)


if 'waiting_list' not in st.session_state:
    st.session_state.waiting_list = ['A1', 'B2', 'C3', 'D4']

for n, i in enumerate(st.session_state.waiting_list):
    with st.expander(i):
        c1, c2, c3, c4, c5, c6, c7, c8, c9, c10 = st.columns(10)
        with c1:
            st.button('▲', key=f'move up_{i}', on_click=submit_up, args=(n,))
        with c2:
            st.button('▼', key=f'move down_{i}', on_click=submit_down, args=(n,))
        with c3:
            st.button('Top', key=f'move up_{i}', on_click=submit_top, args=(n,))
        with c4:
            st.button('Bottom', key=f'move down_{i}', on_click=submit_bottom, args=(n,))
        with c8:
            st.button('Delete', key=f'delete_{i}', on_click=delete, args=(n,))
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>
