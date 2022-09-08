# 如何處理大stp檔案

使用者提供了一個400MB的複雜機構stp檔，需要找出當中某些部件輸出到HFSS當中做模擬。正常狀況機構工程師應該只輸出實體物件到stp檔當中，但是這個stp檔並不是。該檔案當中包含了極多的輔助線以及輔助面，要在HFSS當中載入全部物件再從中篩選出所要的實體部件極為耗時(十幾個小時)。為了解決這個問題，我安裝了FreeCAD，當中提供了Python API。我寫了腳本先讀取stp檔，當中用函數輸出所有的實體部件(共600多個)並將其匯出到新的stp當中。最後載入stp到HFSS當中做操作即可。這時候要再設定就簡單多了。

從腳本讀取stp檔的實體物件到生成新的stp檔到最後匯入stp到HFSS，完成所有工作僅需20分鐘。環境為Python3.6版，在Anaconda當中執行。

{% code lineNumbers="true" %}
```python
import sys
sys.path.append('D:/Program Files/FreeCAD 0.18/bin')
import FreeCAD
import Part #載入Part模組

shape = Part.Shape()
shape.read('D:/demo/design.stp') #讀取stp檔

model = Part.makeCompound(shape.Solids) #將solid部件轉成shape
model.exportStep('d:/demo12/final.stp') #將shape輸出stp檔
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
