# 匯出Q3D選項設定

### 重點

* 要從SIwave匯出模型到Q3D，選項可以寫在options.config當中，檔案內部填寫設定參數

```
LAUNCH_Q3D 1 
Q3D_PIN_GROUP_SRC_SNKS 1
Q3D_MERGE_SINKS 1
Q3D_VERSION 2023.1
```

輸出程式碼

{% code lineNumbers="true" %}
```python
oDoc.ScrSetOptionsFor3DModelExport ("options.config")
oDoc.ScrExport3DModel ("Q3D","E:\SIwaveScriptCommands\Export3DModel\MergeSinks.aedt")
```
{% endcode %}

