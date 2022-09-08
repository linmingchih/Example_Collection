# EDB

AEDT將PCB或封裝的複雜結構記錄在aedb當中。各位可以留意從3D layout匯入PCB或封裝的時候，除了.aedt外，都會多出一個同名的.aedb目錄，目錄當中會有一個edb.def檔案，所有設計的資訊都儲存在其中。AEDT有一組專用的函式庫專門用來建立、更新、讀取、刪除資料庫edb.def當中所存放的零件、佈線或材料等訊息。這一組EDB專用函式庫的最大好處是不需要在設計開啟的狀況底下便可以針對版圖進行編修，速度相當快。缺點是不能透過錄製建立，而且找不到太多的範例。



### 如何取得3D Layout的database？

在開發3D Layout的自動化程式，某些時候我們需要取得Layout的一些訊息，偏偏函式庫不提供對應的函數，這時候就只能透過EDB的函式庫來抓取。但是如何才能從AEDT函式連結到專案對應到的EDB呢？這時候就可以透過下面指令來達成：

{% code lineNumbers="true" %}
```python
DB = edb.Database.Attach(int(oProject.GetEDBHandle()))
```
{% endcode %}

當中GetEDBHandle()可以取得現在開啟當中的Project對應到的資料庫編號，此時用edb.Database.Attach()輸入編號便可以返回資料庫物件做資料的抓取了。
