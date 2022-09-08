# 輸出Report所有維度的獨立變數

{% code lineNumbers="true" %}
```python
from win32com import client

oApp = client.Dispatch("Ansoft.ElectronicsDesktop.2022.2")
oDesktop = oApp.GetAppDesktop()
oDesktop.RestoreWindow()
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()

oReportSetup = oDesign.GetModule("ReportSetup")
oSolutions = oDesign.GetModule("Solutions")

report_solution = {}
solutions = []
display = {}
context = {}
category = {}
quantities = {}
variations = {}
solve_range = {}

for report_type in oReportSetup.GetAvailableReportTypes():
    display[report_type] = oReportSetup.GetAvailableDisplayTypes(report_type)
    report_solution[report_type] = oReportSetup.GetAvailableSolutions(report_type)
    for s in report_solution[report_type]:
        if s not in solutions:
            solutions.append(s)

for _report_type, _display in display.items():
    for d in _display:
        for _solution in report_solution[_report_type]:
            ctxt = oReportSetup.GetSolutionContexts(_report_type, d, _solution)
            context[(_report_type, d, _solution)] = ctxt
            for _ctxt in ctxt:
                cate = oReportSetup.GetAllCategories(_report_type, d, _solution, _ctxt)
                category[(_report_type, d, _solution, _ctxt)] = cate
                for _cate in cate:
                    q = oReportSetup.GetAllQuantities(_report_type, d, _solution, _ctxt, _cate)
                    quantities[(_report_type, d, _solution, _ctxt, _cate)] = q

for sol in solutions:
    solve_range[sol] = oSolutions.GetSolveRangeInfo(sol)
    variations[sol] = oSolutions.GetAvailableVariations(sol)
```
{% endcode %}

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>
