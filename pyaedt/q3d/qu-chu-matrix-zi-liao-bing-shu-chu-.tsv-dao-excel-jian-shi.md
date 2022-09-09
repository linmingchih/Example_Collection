---
description: >-
  可一次取出所有design variations, 6個物理量(DCR, DCL, ACR, ACL, C, G)，Original及所有Reduced
  Matrix的所有sources組合隨頻率變化的數值。
---

# 取出Matrix資料並輸出.tsv到EXCEL檢視

{% code lineNumbers="true" %}
```python
from pyaedt import Q3d
from itertools import product


class matrix:
    def __init__(self):
        q3d = Q3d()
        self.design_name = q3d.design_name

        categories = ['DCR Matrix', 'ACR Matrix', 'DCL Matrix', 'ACL Matrix', 'C Matrix', 'G Matrix']
        solution = q3d.nominal_sweep
        matrix_list = q3d.odesign.GetChildObject('Reduce Matrix').GetChildNames()

        variations = q3d.available_variations.variations()
        self.variations = [i.replace(':=', '') for i in variations[0][::2]]
        variations_value = []

        for i in variations:
            x = []
            for j in i[1::2]:
                x.append(j[0])
            variations_value.append(x)

        self.result = {}
        for matrix in matrix_list:
            quantities = []

            for category in categories:
                quantities += q3d.post.available_report_quantities(report_category="Matrix",
                                                                   display_type="Data Table",
                                                                   solution=solution,
                                                                   quantities_category=category,
                                                                   context=matrix)

            for v, va in zip(variations, variations_value):
                for q in quantities:
                    data = q3d.post.get_solution_data_per_variation(soltype="Matrix",
                                                                    setup_sweep_name=solution,
                                                                    ctxt=matrix,
                                                                    sweeps=["Freq:=", ["All"]] + v,
                                                                    expression=q
                                                                    )
                    self.result[tuple(va + [q, matrix])] = (data.data_real(), data.units_data[q])
                    self.freq = data.primary_sweep_values
                    self.freq_unit = data.units_sweeps['Freq']

        q3d.release_desktop(False, False)

    def export_csv(self, csv_path):
        with open(csv_path, 'w') as f:
            f.writelines('\t'.join(
                self.variations + ['Quantity', 'Matrix'] + [str(i) + self.freq_unit for i in self.freq] + ['Unit\n']))
            for key, value in self.result.items():
                line = '\t'.join(list(key) + [str(i) for i in value[0]] + [value[1]]) + '\n'
                f.writelines(line)


m1 = matrix()
m1.export_csv('d:/demo/matrix.tsv')
```
{% endcode %}
