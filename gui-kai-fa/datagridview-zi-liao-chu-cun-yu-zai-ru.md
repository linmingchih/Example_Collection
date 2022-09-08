---
description: 按鈕將資料保存到Json檔案當中，下次執行程式自動載入。
---

# DataGridView資料儲存與載入

{% code lineNumbers="true" %}
```python
def Form1_Load(self, sender, e):
    try:
        with open('./data.json') as f:
            data = json.load(f)
        AddWarningMessage(str(data))
        for i in data:
            self.dataGridView1.Rows.Add(*i)
    except:
        return None


def button1_Click(self, sender, e):
    data = []
    try:
        for i in range(len(self.dataGridView1.Rows)):
            data.append([])
            for j in range(len(self.dataGridView1.Rows[i].Cells)):
                x = self.dataGridView1.Rows[i].Cells[j].Value
                if j == 3:
                    data[i].append(True if x else False)
                    continue
                if not string.IsNullOrEmpty(x):
                    data[i].append(x)
                else:
                    data[i].append("")
    except:
        raise
    with open('./data.json', 'w') as f:
        json.dump(data, f)
```
{% endcode %}
