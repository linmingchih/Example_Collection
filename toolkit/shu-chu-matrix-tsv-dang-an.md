---
description: >-
  Output Q3D Matrix to .tsv, including all variations, 6 quantities, all reduced
  matrix and all frequencies. Run the script in AEDT.
---

# 輸出Matrix TSV檔案

<figure><img src="../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

<details>

<summary>outputQ3DMatrix.py</summary>

{% code lineNumbers="true" %}
```python
import os

# from win32com import client
# oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
# oDesktop = oApp.GetAppDesktop()
# oDesktop.RestoreWindow()

oDesktop.ClearMessages("", "", 2)
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
x = oProject.GetPath()


#%%

csv_path = os.path.join(oProject.GetPath(), 'matrix_{}_{}.tsv'.format(oProject.GetName(), oDesign.GetName()))

oModule = oDesign.GetModule('AnalysisSetup')
setups = oModule.GetSetups() 
sweeps = oModule.GetSweeps(setups[0])
if sweeps:
    solution = setups[0] + ' : ' + sweeps[0]
else:
    solution = setups[0] + ' :  LastAdaptive'

#%%
categories = ['DCR Matrix', 'ACR Matrix', 'DCL Matrix', 'ACL Matrix', 'C Matrix', 'G Matrix']

matrix_list = oDesign.GetChildObject('Reduce Matrix').GetChildNames()
AddWarningMessage(str(matrix_list))

oModule = oDesign.GetModule('Solutions')
variations = oModule.GetAvailableVariations(solution)

#%%

result = {}
for matrix in matrix_list:
    quantities = []
    
    oModule = oDesign.GetModule('ReportSetup')
    for category in categories:
        q = oModule.GetAllQuantities("Matrix", "Data Table", solution, matrix, category)
        quantities+=q

    oModule = oDesign.GetModule("ReportSetup")

    for v in variations:
        for q in quantities:
            data = []
            va = []
            title = []
            for i in v.split():
                x, y = i.split('=')
                data.append(x+':=')
                y = y.replace("'", '')
                data.append([y])
                
                va.append(y)
                title.append(x)
            arr = oModule.GetSolutionDataPerVariation('Matrix',
                                                      solution, 
                                                      ['Context:=', matrix], 
                                                      ['Freq:=', ['All'],] + data, 
                                                      [q])
            
            result[tuple(va+[q, matrix])] = (arr[0].GetRealDataValues(q), arr[0].GetDataUnits(q)) 
            #freq_unit = arr[0].GetSweepUnits('Freq')
            freq_unit = 'Hz'
            freq = arr[0].GetSweepValues('Freq')
        
#%%
with open(csv_path, 'w') as f:
    f.writelines('\t'.join(title + ['Quantity', 'Matrix'] + [str(i)+freq_unit for i in freq]) + '\n')
    for key, value in result.items():
        line = '\t'.join(list(key)+ [str(i) for i in value[0]]) + '\n'
        f.writelines(line)
        

AddWarningMessage('{} is saved!'.format(csv_path))
```
{% endcode %}

</details>
