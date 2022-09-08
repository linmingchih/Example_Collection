---
description: >-
  延續上一篇介紹的內容，本範例套用Streamlit模組，不到10行程式碼(st.XXX)便可以建立RC電路模擬GUI。使用者輸入R,
  C，按下執行模擬鍵，一秒便可以顯示S21分布圖。也可以從遠端設備登入網頁執行模擬工作。如圖二。
---

# 以Streamlit建立網頁GUI背景呼叫Nexxim.exe執行RC電路模擬

{% code lineNumbers="true" %}
```python
# -*- coding: utf-8 -*-
import os
import streamlit as st
import random
import matplotlib.pyplot as plt
from datetime import datetime



text = '''
.option PARHIER='local'
.option max_messages=1

* begin toplevel circuit

R1 Port1 Port2 {}
C2 0 Port2 {}
RPort1 Port1 0 PORTNUM=1 RZ=50 IZ=0 
.PORT Port1 0 1 RPort1 
RPort2 Port2 0 PORTNUM=2 RZ=50 IZ=0 
.PORT Port2 0 2 RPort2 

* end toplevel circuit
.LNA
+ LIN 10000 0 10000000000
+ FLAG='LNA'

.end'''


def readCSV(csv_name):
    with open(csv_name) as f:
        text = f.readlines()

    freq = []
    S11, S12, S21, S22 = [], [], [], []
    for line in text[1:]:
        _, f, s11_re, s11_im, s12_re, s12_im, s21_re, s21_im, s22_re, s22_im = line.split(',')
        freq.append(float(f))
        S11.append(complex(float(s11_re), float(s11_im)))
        S12.append(complex(float(s12_re), float(s12_im)))
        S21.append(complex(float(s21_re), float(s21_im)))
        S22.append(complex(float(s22_re), float(s22_im)))
    return freq, S21


st.header('RC電路S參數模擬')
R = st.number_input('電阻值(Ohm)')
C = st.number_input('電容值(pF)') * 1e-12
z = st.button('執行模擬')

n = datetime.now().timestamp()
if z:
    with open('c:/demo/rc{}.cir'.format(n), 'w') as f:
        f.write(text.format(R, C))

    os.environ['path'] = 'C:\Program Files\AnsysEM\AnsysEM21.1\Win64'
    os.system('nexxim c:/demo/rc{}.cir'.format(n))
    os.system('sdf2csv c:/demo/rc{}.cir.sdf'.format(n))
    with open('c:/demo/rc{}.cir.sdf.csv'.format(n)) as f:
        text = f.readlines()

    f, S21 = readCSV('c:/demo/rc{}.cir.sdf.csv'.format(n))
    plt.figure(figsize=(4, 3))
    plt.plot(f, [abs(i) for i in S21])
    plt.grid()
    plt.xlabel('freq(Hz)')
    plt.ylabel('mag')
    plt.xlim(0, 1e10)
    plt.ylim(0, 1)
    st.pyplot(plt)
    print('Finished ID: {}'.format(n))
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>
