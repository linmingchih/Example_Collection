# 用ansysedt.exe讀取網表做電路模擬

之前介紹過用nexxim.exe讀取網表作電路模擬，輸出.sdf並將.sdf轉成.csv。缺點是只能支援線性模擬及暫態模擬。且線性模擬只能輸出S參數，不能輸出Y/Z參數。除此之外，必須自行編寫程式來輸出Touchstone格式，非常不方便。本篇做法可以處理AEDT的各類電路模擬，並且以.csv輸出任意AEDT本身支援的模擬資料，如頻譜，眼圖等等。

1\. 建立AEDT專案test.aedt，加入一個設計CircuitNetlist1，當中只有一行: .INCLUDE "D:\demo\test.cir"，如下圖。並在專案當中加入"Z Parameter Table 1"的表格報告。完成之後關閉aedt。

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption><p>(圖一)test.aedt專案包含CircuitNetlist1設計</p></figcaption></figure>

2\. 文字檔test.cir當中包含完整的網表及模擬設定，例如:

{% code title="test.cir" lineNumbers="true" %}
```python
C1 Port1 Port2 1e-3
RPort1 Port1 0 PORTNUM=1 RZ=50 IZ=0 
.PORT Port1 0 1 RPort1 
RPort2 Port2 0 PORTNUM=2 RZ=50 IZ=0 
.PORT Port2 0 2 RPort2 

* end toplevel circuit
.LNA
+ LIN 101 0 1000000000
+ FLAG='LNA'
```
{% endcode %}

3\. 建立.py檔，執行模擬及.csv輸出，如下

{% code title="test.py" lineNumbers="true" %}
```python
oProject = oDesktop.OpenProject("d:/demo/test.aedt")
oDesign = oProject.SetActiveDesign("CircuitNetlist1")
oDesign.AnalyzeAll()
oModule = oDesign.GetModule("ReportSetup")

oModule.ExportToFile("Z Parameter Table 1", "D:/demo/Z Parameter Table 1.csv", False)
```
{% endcode %}

4\. 執行ansysedt命令輸出.csv檔

{% code title="test.bat" lineNumbers="true" %}
```batch
SET PATH=C:\Program Files\AnsysEM\AnsysEM21.1\Win64
ansysedt.exe -features=beta -ng -waitforlicense -RunScriptAndExit .\test.py
```
{% endcode %}

自動化程式可以透過修改test.cir當中的網表內容並執行ansysedt命令來取得模擬結果.csv檔來製作報告。
