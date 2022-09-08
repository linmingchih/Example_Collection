# 如何將tech檔案轉成控制檔

在手動匯入GDSII檔案的時候，我們是輸入.tech檔案將層數編號轉成一般的名稱，如圖一。但是在錄製腳本的時候，無法設定.tech檔案。這時候我們可以利用控制檔(.xml)來取代，程式碼如下：

{% code lineNumbers="true" %}
```python
oTool = oDesktop.GetTool("ImportExport")
oTool.ImportGDSII(gds_path, aedb_path, control_path, "")
```
{% endcode %}

以下函數可以將.tech檔轉成.xml檔：

{% code lineNumbers="true" %}
```python
def techToControlFile(tech_path, unit='mm'):
    result = []
    with open(tech_path) as f:
        for line in f:
            try:
                layerID, layer_name, color, elevation, layer_height = line.split()
                x = '      <Layer Color="#ff0000" GDSIIVia="false" Name="{}" TargetLayer="{}" Thickness="{}" Type="conductor"/>'.format(
                    layerID, layer_name, layer_height)
                result.append(x)
            except:
                pass

    frame = '''<?xml version="1.0" encoding="UTF-8" standalone="no" ?>
    <c:Control xmlns:c="http://www.ansys.com/control" schemaVersion="1.0">

      <Stackup schemaVersion="1.0">
        <Layers LengthUnit="{}">
{}    
    </Layers>
  </Stackup>

  <ImportOptions Flatten="true" GDSIIConvertPolygonToCircles="false" ImportDummyNet="true"/>

</c:Control>'''

    return frame.format(unit, '\n'.join(result))
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>
