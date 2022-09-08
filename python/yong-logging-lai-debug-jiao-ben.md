# 用logging來Debug腳本

AddWarningMessage雖然可以直接輸出抓取的訊息，但是message視窗小字也小。如果資料量大要再做處理也不容易。用logging模組可以解決這個問題。印到log檔案的訊息可以在文字編輯器或Spyder當中打開檢視。一邊看代碼，一邊看輸出結果。複雜的資料只要做簡單的處理便可以在變數表單打開或是畫成圖表方便檢視，另外要比較代碼修改前後資料結構改變也很容易，因為logging模組預設輸出資料會附加到log檔當中，而非覆蓋。使用logging很簡單，只要一開始設定好logging如下：

{% code lineNumbers="true" %}
```python
import logging
FORMAT = '%(asctime)s %(levelname)s: %(message)s'
logging.basicConfig(filename='simulation.log', encoding='utf-8', filemode='w', level=logging.DEBUG, format=FORMAT)
之後用logging.info()就可以將資料丟到log檔案中即可。

if __name__ == '__main__':
    oDesktop.ClearMessages("", "", 2)
    logging.info('-'*80+'\n')
    logging.info(getLayerElevation())
    logging.info(getComponentElevation())
    logging.info(getComponentElevation())
    logging.info(getPinInfo())
    logging.info(getListViewInfo())
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption><p>在Spyder當中一個頁面寫script，一個頁面檢查輸出結果</p></figcaption></figure>
