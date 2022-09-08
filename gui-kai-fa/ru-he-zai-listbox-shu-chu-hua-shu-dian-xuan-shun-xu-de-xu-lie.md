---
description: ListBox預設只能輸出由上而下輸出所選序列，跟滑鼠點選順序無關。如果要輸出滑鼠點選順序的序列，可參考以下程式碼。
---

# 如何在ListBox輸出滑鼠點選順序的序列

{% code lineNumbers="true" %}
```python
# coding=utf-8
import os, re, sys, clr, json, math, logging

os.chdir(os.path.dirname(__file__))
logging.basicConfig(filename='gui.log', filemode='w', encoding='utf-8', level=logging.DEBUG)
clr.AddReference('System.Drawing')
clr.AddReference('System.Windows.Forms')

from System import Drawing, Array, ComponentModel, Diagnostics, IO
from System.Windows import Forms
import System.Object as object
import System.String as string
from System.Windows.Forms import DialogResult, OpenFileDialog, SaveFileDialog, FolderBrowserDialog

# ----------------------------------------------------------------------------
try:
    image_path = './ANSYS.png'
    oDesktop.ClearMessages("", "", 2)
    AddWarningMessage('Welcome')
except:
    pass


# ----------------------------------------------------------------------------
class MyForm(Forms.Form):
    def __init__(self):
        self.listBox1 = Forms.ListBox()
        self.textBox1 = Forms.TextBox()
        self.SuspendLayout()
        # listBox1
        self.listBox1.FormattingEnabled = True
        self.listBox1.ItemHeight = 19
        self.listBox1.Items.Add("a")
        self.listBox1.Items.Add("b")
        self.listBox1.Items.Add("c")
        self.listBox1.Items.Add("d")
        self.listBox1.Items.Add("e")
        self.listBox1.Items.Add("f")
        self.listBox1.Items.Add("g")
        self.listBox1.Items.Add("h")
        self.listBox1.Items.Add("i")
        # self.listBox1.SelectedIndex = 0
        self.listBox1.Location = Drawing.Point(12, 12)
        self.listBox1.Name = "listBox1"
        self.listBox1.SelectionMode = Forms.SelectionMode.MultiExtended
        self.listBox1.Size = Drawing.Size(331, 422)
        self.listBox1.TabIndex = 0
        self.listBox1.SelectedIndexChanged += self.listBox1_SelectedIndexChanged

        # textBox1
        self.textBox1.Location = Drawing.Point(388, 12)
        self.textBox1.Multiline = True
        self.textBox1.Name = "textBox1"
        self.textBox1.Size = Drawing.Size(400, 426)
        self.textBox1.TabIndex = 1
        # Form1
        self.AutoScaleDimensions = Drawing.SizeF(9, 19)
        self.AutoScaleMode = Forms.AutoScaleMode.Font
        self.ClientSize = Drawing.Size(800, 450)
        self.Controls.Add(self.textBox1)
        self.Controls.Add(self.listBox1)
        self.Name = "Form1"
        self.Text = "Form1"
        self.ResumeLayout(False)
        self.PerformLayout()
        self.selected = []

    def listBox1_SelectedIndexChanged(self, sender, e):

        sel = [i for i in sender.SelectedItems]
        if not set(self.selected).issubset(set(sel)):
            self.selected = []

        for i in self.selected:
            sel.remove(i)
        self.selected += list(sel)
        self.textBox1.Text = str(self.selected)


if __name__ == '__main__':
    try:
        form = MyForm()
        form.ShowDialog()
        form = MyForm()
        form.Dispose()
        # form.Show()
        # oDesktop.PauseScript()
    except:
        logging.exception('ERROR!')
```
{% endcode %}

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>
