# 利用desktopproxy中斷模擬

我們可以用指令執行aedt模擬，比如說

{% code lineNumbers="true" %}
```batch
set path=C:\Program Files\AnsysEM\v221\Win64;%path%
start ansysedt.exe -ng -BatchSolve .\package.aedt
```
{% endcode %}

上述案例執行時間4分17秒。&#x20;

接下來我們用ansysedt指令執行，2分鐘後以desktopproxy -abort中斷模擬。

{% code lineNumbers="true" %}
```batch
set path=C:\Program Files\AnsysEM\v221\Win64;%path%
start ansysedt.exe -ng -BatchSolve .\package.aedt
PING localhost -n 120 >NUL
desktopproxy -abort .\package.aedt
```
{% endcode %}

中斷之後，再重啟模擬到模擬結束共2分22秒

{% code lineNumbers="true" %}
```batch
set path=C:\Program Files\AnsysEM\v221\Win64;%path%
start ansysedt.exe -ng -BatchSolve .\package.aedt
```
{% endcode %}

總共花了4分22秒，比一次完成執行僅僅多了約5秒。代表中斷之後重啟模擬是延續之前中斷之前的狀態，而非重新開始。
