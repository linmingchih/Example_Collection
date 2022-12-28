---
description: 需安裝Selenium模組
---

# 將網頁輸出成PDF



{% code lineNumbers="true" %}
```python
from selenium import webdriver
import base64

chrome_options = webdriver.ChromeOptions()
settings = {
       "recentDestinations": [{
            "id": "Save as PDF",
            "origin": "local",
            "account": "",
        }],
        "selectedDestinationId": "Save as PDF",
        "version": 2
    }

driver = webdriver.Chrome(chrome_options=chrome_options)
driver.get("https://google.com")
pdf_data = driver.execute_cdp_cmd("Page.printToPDF", settings)

with open('d:/demo/Google1.pdf', 'wb') as file:
    file.write(base64.b64decode(pdf_data['data']))

driver.quit()
```
{% endcode %}
