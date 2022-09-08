# 如何避免執行ValidateDesign()之後跳出錯誤視窗？

在執行HFSS模擬之前，可以按下Validation Check來檢查是否有模擬設定錯誤產生。同樣的，自動化程式可以透過oDesign.ValidateDesign('xxx.log')的函式呼叫將檢查得到的相關訊息輸出到log檔，接著便可以到log檔當中去撈資料。

但此時會出現一個問題，便是在進行接下來的3D編輯工作時會跳出錯誤視窗(如下圖)，這視窗需要手動才能關閉，導致程式卡住。解決方法是在oDesign.ValidateDesign()之後加入oProject.Save()，這個指令會將Buffer清空。之後在操作3D編輯工作時，錯誤視窗便不會跳出來中斷程式的執行了。

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>
