# 如何將圖片嵌到程式碼當中

當我們要在GUI上面放上一些圖片檔，要另外提供.png檔實在是一件麻煩的事。如果可以將圖片放在Python檔裡面就可以只要提供一個檔案即可，這可以透過Base64機制達成。首先將PNG檔轉成Base64字串，很多的網站都有提供這個功能。再將字串插入到Python程式當中。等到在AEDT執行時將字串還原成圖片即可。

{% code lineNumbers="true" %}
```python
import base64

base64_img = '''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
AAAAAAAAAAAAAAAAAAAAAAAH//2Q=='''

base64_img_bytes = base64_img.encode('utf-8')
with open('d:/demo/ANSYS_Logo.png', 'wb') as file_to_save:
    decoded_image_data = base64.b64decode(base64_img_bytes)
    file_to_save.write(decoded_image_data)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>
