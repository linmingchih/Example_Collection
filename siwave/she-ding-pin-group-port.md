# 設定Pin Group Port

{% code lineNumbers="true" %}
```python
from win32com import client

oApp = client.Dispatch("SIwave.Application.2022.1")
oApp.RestoreWindow()
oDoc = oApp.GetActiveProject()

# ScrPlaceCircuitElement(string givenElementName, 
#                        string givenPartName, 
#                        int circuitElementType, 
#                        int posTermConnectionType, 
#                        string posTermParam1, 
#                        string posTermParam2, 
#                        string posTermParam3, 
#                        int refTermConnectionType, 
#                        string refTermParam1, 
#                        string refTermParam2, 
#                        string refTermParam3, 
#                        double capVal, 
#                        double indVal, 
#                        double resVal, 
#                        double refZRe, 
#                        double mag, 
#                        double phase) -> short

oDoc.ScrPlaceCircuitElement('ABC', 
                            'DEF', 
                            3, 
                            1,
                            'FCHIP', 
                            'FCHIP', 
                            'FCHIP_VDD_15_Group', 
                            1, 
                            'FCHIP', 
                            'FCHIP', 
                            'FCHIP_VSS_Group', 
                            0, 
                            0, 
                            0, 
                            50, 
                            0, 
                            0)
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>
