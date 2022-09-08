# HFSS Djordjevic Sarkar材料模型頻率變化公式字串輸出

{% code lineNumbers="true" %}
```python
from math import tan, pi, sqrt, atan, log, exp

'''
3.85309+0.0265244*ln((2.53303e+22+Freq*Freq)/(6.10915e+09+Freq*Freq))
1e-12+2.95123e-12*Freq*(atan(Freq/78161.1)-atan(Freq/1.59155e+11))
'''


def Djordjevic_Sarkar(dk=4, df=0.02, f0=10e9):
    epson0 = 8.8541878128e-12
    epson1 = dk
    delta1 = df
    f1 = f0
    fb = 159.15494e9
    sigma_dc = 1e-12

    K = (epson1 * delta1 - sigma_dc / (2 * pi * f1 * epson0)) / atan(fb / f1)
    epson_inf = epson1 - K * log(sqrt(fb ** 2 + f1 ** 2) / (f1))

    delta_epson = 10 * delta1 * epson_inf
    fa = fb / exp(delta_epson / K)

    formula1 = '{:.5f}+{:.7f}*ln(({:.5e}+Freq*Freq)/({:.5e}+Freq*Freq))'.format(epson_inf, K / 2, fb ** 2, fa ** 2)
    formula2 = '{}+{:.5e}*Freq*(atan(Freq/{:.5e})-atan(Freq/{:.5e}))'.format(sigma_dc, 2 * pi * epson0 * K, fa, fb)

    return formula1, formula2


dk, df = Djordjevic_Sarkar()
print(dk)
print(df)
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>
