# 如何在命令列將.brd轉成.siw

在置放.brd(此處以Galileo\_G87173\_204.brd為例)的目錄底下，建立批次檔convert.bat，內容如下：

{% code title="convert.bat" lineNumbers="true" %}
```batch
path=%path%;C:\Program Files\AnsysEM\AnsysEM20.2\Win64
anstranslator.exe Galileo_G87173_204.brd
siwave_ng Galileo_G87173_204.aedb SaveSiw.exec
pause
```
{% endcode %}

此外，在目錄底下建立SaveSiw.exec檔案，當中只有一行：

{% code lineNumbers="true" %}
```
SaveSiw
```
{% endcode %}

滑鼠雙擊convert.bat，幾秒鐘之後生成Galileo\_G87173\_204.siw。開啟Galileo\_G87173\_204.siw如下圖：

<figure><img src="../.gitbook/assets/image (2) (3).png" alt=""><figcaption></figcaption></figure>
