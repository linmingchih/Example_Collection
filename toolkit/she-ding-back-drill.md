# 設定Back Drill

![](<../.gitbook/assets/image (2) (2).png>)

<details>

<summary>AEDT_3D_Layout_Back_Drill_Toolkit</summary>

{% code lineNumbers="true" %}
```python
# coding=utf-8
import os, re, sys, clr, json, math, logging
from distutils.dir_util import copy_tree

os.chdir(os.path.dirname(__file__))
logging.basicConfig(filename='gui.log', filemode='w', encoding='utf-8', level=logging.DEBUG)
clr.AddReference('System.Drawing')
clr.AddReference('System.Windows.Forms')

from System import Drawing, Array, ComponentModel, Diagnostics, IO, Collections
from System.Windows import Forms
from System.Drawing import Color
import System.Object as object
import System.String as string
from System.Windows.Forms import DialogResult, OpenFileDialog ,SaveFileDialog, FolderBrowserDialog
#----------------------------------------------------------------------------
import clr

clr.AddReference('Ansys.Ansoft.Edb')
clr.AddReference('Ansys.Ansoft.SimSetupData')
import Ansys.Ansoft.Edb as edb
import ScriptEnv

oDesktop.ClearMessages("", "", 2)
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oProject = oDesktop.GetActiveProject()
oDesign = oProject.GetActiveDesign()
oEditor = oDesign.GetActiveEditor()


info = {}
for via in oEditor.FindObjects('type', 'via'):
    net_name = oEditor.GetPropertyValue('BaseElementTab', via, 'Net')
    if net_name not in info:
        info[net_name] = []
    info[net_name].append(via)
    

aedb_path = os.path.join(oProject.GetPath(), oProject.GetName() + '.aedb') 
cell_name = oDesign.GetName() if ';' not in oDesign.GetName() else oDesign.GetName().split(';')[1]

AddWarningMessage(aedb_path)
AddWarningMessage(cell_name)

def convert(x):
    x = x.strip()
    scale = {'mil': 0.0000254, 'mm':0.001}
    for n, i in enumerate(x):
        if i.isalpha():
            return float(x[:n])*scale[x[n:]]
            
def convert_mil(x):
    return str(x/0.0000254) + 'mil'

        
def get_back_drill_layer(_from, _length):
    info = []
    for i in oEditor.GetStackupLayerNames():
        x = oEditor.GetLayerInfo(i)
        layer_type = x[0].split(':')[1].strip()
        thickness = convert(x[-2].split(':')[1])
        elevation = convert(x[-1].split(':')[1])
        info.append([i, layer_type, thickness, elevation])
    
    total_thickness = info[0][2]+ info[0][3]
    AddWarningMessage(str(total_thickness))
    
    if _from == 'Top':
        length = total_thickness-convert(_length)
    else:
        length = convert(_length)
    AddWarningMessage(str(_length))
    for layer_name, layer_type, thickness, elevation in info:
        if elevation <= length < (elevation + thickness):
            up_thickness = (elevation + thickness) - length
            low_thickness = length - elevation
            return layer_type, layer_name, convert_mil(up_thickness), convert_mil(low_thickness)
    return None


def new_aedb(layer_name, up_height, low_height):
    n=1
    while True:
        new_aedb_path = aedb_path.replace('.aedb', '_{}.aedb'.format(n))
        if not os.path.isdir(new_aedb_path):
            break
        n+=1
    copy_tree(aedb_path, new_aedb_path)
            
    middle_layer_name = layer_name + '_1'
    low_layer_name = layer_name + '_2'
    
    DB = edb.Database.Open(new_aedb_path, False)

    cells = [i for i in DB.CircuitCells]
    for cell in cells:
        if cell.GetName() == cell_name:
            break
    
    layout = cell.GetLayout()
    raw_layercollection = layout.GetLayerCollection()
    new_layercollection = edb.Cell.LayerCollection(raw_layercollection)
    
    all_layers = raw_layercollection.Layers(edb.Cell.LayerTypeSet.AllLayerSet)
    
    for layer in all_layers:
        if layer.GetName() == layer_name: 
            layer_material = layer.GetMaterial()
            layer2 = edb.Cell.StackupLayer(middle_layer_name, edb.Cell.LayerType.SignalLayer, edb.Utility.Value('0'), edb.Utility.Value(0), "COPPER")    
            layer3 = edb.Cell.StackupLayer(low_layer_name, edb.Cell.LayerType.DielectricLayer, edb.Utility.Value(low_height), edb.Utility.Value(0), layer_material)
            new_layercollection.AddLayerBelow(layer2, layer_name)
            new_layercollection.AddLayerBelow(layer3, middle_layer_name)

    
    empty_layercollection = edb.Cell.LayerCollection()
    for layer in new_layercollection.Layers(edb.Cell.LayerTypeSet.AllLayerSet):
        if layer.GetName() == layer_name:
            layer=layer.Clone()
            layer.SetThickness(edb.Utility.Value(up_height))
            
            empty_layercollection.AddLayerBottom(layer)
        else:
            empty_layercollection.AddLayerBottom(layer)            
            
    layout.SetLayerCollection(empty_layercollection)

    padstack_instances = [i for i in layout.PadstackInstances]
    padstack_def = {}
    for i in padstack_instances:
        if i.GetPadstackDef().GetName() not in padstack_def:
            padstack_def[i.GetPadstackDef().GetName()] = i.GetPadstackDef()

    dimension = Collections.Generic.List[edb.Utility.Value]()
    dimension.Add(edb.Utility.Value(0))

    for i, d in padstack_def.items():
        new_data = edb.Definition.PadstackDefData(d.GetData())
        
        new_data.SetPadParameters('Default', 
                                  edb.Definition.PadType.RegularPad,
                                  edb.Definition.PadGeometryType.NoGeometry,
                                  dimension,
                                  edb.Utility.Value(0),
                                  edb.Utility.Value(0),
                                  edb.Utility.Value(0))
        new_data.SetPadParameters('Default', 
                                  edb.Definition.PadType.AntiPad,
                                  edb.Definition.PadGeometryType.NoGeometry,
                                  dimension,
                                  edb.Utility.Value(0),
                                  edb.Utility.Value(0),
                                  edb.Utility.Value(0))        

        d.SetData(new_data)
    DB.Save()
    DB.Close()
    return new_aedb_path



#----------------------------------------------------------------------------
class MyForm(Forms.Form):
    def __init__(self):
        self.groupBox1 = Forms.GroupBox()
        self.nets_lb = Forms.ListBox()
        self.groupBox2 = Forms.GroupBox()
        self.vias_cl = Forms.CheckedListBox()
        self.groupBox3 = Forms.GroupBox()
        self.Insert_bt = Forms.Button()
        self.unit_lb = Forms.Label()
        self.length_tb = Forms.TextBox()
        self.bottom_rb = Forms.RadioButton()
        self.top_rb = Forms.RadioButton()
        self.apply_bt = Forms.Button()
        self.groupBox4 = Forms.GroupBox()
        self.find_tb = Forms.TextBox()
        self.groupBox1.SuspendLayout()
        self.groupBox2.SuspendLayout()
        self.groupBox3.SuspendLayout()
        self.groupBox4.SuspendLayout()
        self.SuspendLayout()
        # groupBox1
        self.groupBox1.Anchor = ((((Forms.AnchorStyles.Top | Forms.AnchorStyles.Bottom)| Forms.AnchorStyles.Left)))
        self.groupBox1.Controls.Add(self.nets_lb)
        self.groupBox1.Location = Drawing.Point(12, 155)
        self.groupBox1.Name = "groupBox1"
        self.groupBox1.Size = Drawing.Size(250, 437)
        self.groupBox1.TabIndex = 0
        self.groupBox1.TabStop = False
        self.groupBox1.Text = "Nets"
        # nets_lb
        self.nets_lb.Dock = Forms.DockStyle.Fill
        self.nets_lb.FormattingEnabled = True
        self.nets_lb.ItemHeight = 19

        #self.nets_lb.SelectedIndex = 0
        self.nets_lb.Location = Drawing.Point(3, 23)
        self.nets_lb.Name = "nets_lb"
        self.nets_lb.ScrollAlwaysVisible = True
        self.nets_lb.SelectionMode = Forms.SelectionMode.MultiExtended
        self.nets_lb.Size = Drawing.Size(244, 411)
        self.nets_lb.Sorted = True
        self.nets_lb.TabIndex = 0
        self.nets_lb.SelectedIndexChanged += self.nets_lb_SelectedIndexChanged

        # groupBox2
        self.groupBox2.Controls.Add(self.vias_cl)
        self.groupBox2.Location = Drawing.Point(270, 155)
        self.groupBox2.Name = "groupBox2"
        self.groupBox2.Size = Drawing.Size(250, 387)
        self.groupBox2.TabIndex = 1
        self.groupBox2.TabStop = False
        self.groupBox2.Text = "Vias"
        # vias_cl
        self.vias_cl.CheckOnClick = True
        self.vias_cl.Dock = Forms.DockStyle.Fill
        self.vias_cl.FormattingEnabled = True

        #self.vias_cl.SelectedIndex = 0
        self.vias_cl.Location = Drawing.Point(3, 23)
        self.vias_cl.Name = "vias_cl"
        self.vias_cl.ScrollAlwaysVisible = True
        self.vias_cl.Size = Drawing.Size(244, 361)
        self.vias_cl.Sorted = True
        self.vias_cl.TabIndex = 0
        self.vias_cl.SelectedIndexChanged += self.vias_cl_SelectedIndexChanged

        # groupBox3
        self.groupBox3.Controls.Add(self.Insert_bt)
        self.groupBox3.Controls.Add(self.unit_lb)
        self.groupBox3.Controls.Add(self.length_tb)
        self.groupBox3.Controls.Add(self.bottom_rb)
        self.groupBox3.Controls.Add(self.top_rb)
        self.groupBox3.Location = Drawing.Point(9, 9)
        self.groupBox3.Name = "groupBox3"
        self.groupBox3.Size = Drawing.Size(511, 67)
        self.groupBox3.TabIndex = 4
        self.groupBox3.TabStop = False
        self.groupBox3.Text = "Insert Layer for Back Drill"
        # Insert_bt
        self.Insert_bt.Location = Drawing.Point(373, 15)
        self.Insert_bt.Name = "Insert_bt"
        self.Insert_bt.Size = Drawing.Size(130, 44)
        self.Insert_bt.TabIndex = 5
        self.Insert_bt.Text = "Insert"
        self.Insert_bt.UseVisualStyleBackColor = True
        self.Insert_bt.Click += self.Insert_bt_Click

        # unit_lb
        self.unit_lb.AutoSize = True
        self.unit_lb.Location = Drawing.Point(295, 28)
        self.unit_lb.Name = "unit_lb"
        self.unit_lb.Size = Drawing.Size(31, 19)
        self.unit_lb.TabIndex = 4
        self.unit_lb.Text = ""
        # length_tb
        self.length_tb.Location = Drawing.Point(231, 25)
        self.length_tb.Name = "length_tb"
        self.length_tb.Size = Drawing.Size(58, 27)
        self.length_tb.TabIndex = 2
        self.length_tb.Text = "0mil"
        self.length_tb.TextAlign = Forms.HorizontalAlignment.Right
        self.length_tb.TextChanged += self.length_tb_TextChanged

        # bottom_rb
        self.bottom_rb.AutoSize = True
        self.bottom_rb.Checked = True
        self.bottom_rb.Location = Drawing.Point(106, 26)
        self.bottom_rb.Name = "bottom_rb"
        self.bottom_rb.Size = Drawing.Size(119, 23)
        self.bottom_rb.TabIndex = 1
        self.bottom_rb.TabStop = True
        self.bottom_rb.Text = "from Bottom"
        self.bottom_rb.UseVisualStyleBackColor = True
        self.bottom_rb.CheckedChanged += self.bottom_rb_CheckedChanged

        # top_rb
        self.top_rb.AutoSize = True
        self.top_rb.Location = Drawing.Point(6, 26)
        self.top_rb.Name = "top_rb"
        self.top_rb.Size = Drawing.Size(94, 23)
        self.top_rb.TabIndex = 0
        self.top_rb.TabStop = True
        self.top_rb.Text = "from Top"
        self.top_rb.UseVisualStyleBackColor = True
        self.top_rb.CheckedChanged += self.top_rb_CheckedChanged

        # apply_bt
        self.apply_bt.Location = Drawing.Point(382, 548)
        self.apply_bt.Name = "apply_bt"
        self.apply_bt.Size = Drawing.Size(130, 44)
        self.apply_bt.TabIndex = 2
        self.apply_bt.Text = "Apply"
        self.apply_bt.UseVisualStyleBackColor = True
        self.apply_bt.Click += self.apply_bt_Click

        # groupBox4
        self.groupBox4.Controls.Add(self.find_tb)
        self.groupBox4.Location = Drawing.Point(9, 82)
        self.groupBox4.Name = "groupBox4"
        self.groupBox4.Size = Drawing.Size(250, 67)
        self.groupBox4.TabIndex = 1
        self.groupBox4.TabStop = False
        self.groupBox4.Text = "Find Net"
        # find_tb
        self.find_tb.Dock = Forms.DockStyle.Fill
        self.find_tb.Location = Drawing.Point(3, 23)
        self.find_tb.Name = "find_tb"
        self.find_tb.Size = Drawing.Size(244, 27)
        self.find_tb.TabIndex = 0
        self.find_tb.TextChanged += self.find_tb_TextChanged

        # Form1
        self.AutoScaleDimensions = Drawing.SizeF(9, 19)
        self.AutoScaleMode = Forms.AutoScaleMode.Font
        self.ClientSize = Drawing.Size(532, 604)
        self.Controls.Add(self.groupBox4)
        self.Controls.Add(self.groupBox3)
        self.Controls.Add(self.apply_bt)
        self.Controls.Add(self.groupBox2)
        self.Controls.Add(self.groupBox1)
        self.FormBorderStyle = Forms.FormBorderStyle.FixedSingle
        self.MaximizeBox = False
        self.MinimizeBox = False
        self.Name = "Form1"
        self.Text = "AEDT 3 Layout Back Drill Toolkit"
        self.TopMost = True
        self.FormClosed += self.Form1_FormClosed

        self.Load += self.Form1_Load

        self.groupBox1.ResumeLayout(False)
        self.groupBox2.ResumeLayout(False)
        self.groupBox3.ResumeLayout(False)
        self.groupBox3.PerformLayout()
        self.groupBox4.ResumeLayout(False)
        self.groupBox4.PerformLayout()
        self.ResumeLayout(False)

    def bottom_rb_CheckedChanged(self, sender, e):
        self.calculate()

    def length_tb_TextChanged(self, sender, e):
        self.calculate()

    def nets_lb_SelectedIndexChanged(self, sender, e):
        self.vias_cl.Items.Clear()
        
        selected_nets = [i for i in sender.SelectedItems]
        for net in selected_nets:
            for via in info[net]:
                self.vias_cl.Items.Add(via)

    def find_tb_TextChanged(self, sender, e):
        self.nets_lb.Items.Clear()
        for net in info:
            m = re.search(sender.Text, net)
            if m:
                self.nets_lb.Items.Add(net)

    def top_rb_CheckedChanged(self, sender, e):
        self.calculate()

    def Form1_FormClosed(self, sender, e):
        pass

    def vias_cl_SelectedIndexChanged(self, sender, e):
        AddWarningMessage(str([i for i in self.vias_cl.CheckedItems]))

    def Form1_Load(self, sender, e):
        self.groupBox1.Enabled = False
        self.groupBox2.Enabled = False
        self.groupBox4.Enabled = False
        self.apply_bt.Enabled = False  
        for net in info:
            self.nets_lb.Items.Add(net)
            

    def apply_bt_Click(self, sender, e):
        oProject = oDesktop.GetActiveProject()
        oDesign = oProject.GetActiveDesign()
        oEditor = oDesign.GetActiveEditor()
        
        if self.direction == 'Top':
            prop = 'Start Layer'
        else:
            prop = 'Stop Layer'
            
        for vianame in self.vias_cl.CheckedItems:
            oEditor.ChangeProperty(
                [
                    "NAME:AllTabs",
                    [
                        "NAME:BaseElementTab",
                        [
                            "NAME:PropServers", 
                            vianame
                        ],
                        [
                            "NAME:ChangedProps",
                            [
                                "NAME:{}".format(prop),
                                "Value:="		, self.target_layer
                            ]
                        ]
                    ]
                ])
            AddWarningMessage('change {}!'.format(vianame))
    
    def calculate(self):
        oDesktop.ClearMessages("", "", 2)
        if self.top_rb.Checked == True:
            self.direction = 'Top'
        else:
            self.direction = 'Bottom'
        try:
            length = self.length_tb.Text
            self.data = get_back_drill_layer(self.direction, length)
            AddWarningMessage(str(self.data))
        except:
            AddWarningMessage('Invalid')
    
    
    def Insert_bt_Click(self, sender, e):
        oProject.Close()
        if self.data:
            layer_type, layer_name, up_thickness, low_thickness = self.data
            self.target_layer = layer_name+'_1'
            if layer_type == 'dielectric':
                new_aedb_path = new_aedb(layer_name=layer_name, up_height=up_thickness, low_height=low_thickness)
                oTool = oDesktop.GetTool("ImportExport")
                oTool.ImportEDB(os.path.join(new_aedb_path, "edb.def"))
                self.groupBox3.Enabled = False
                self.groupBox1.Enabled = True
                self.groupBox2.Enabled = True
                self.groupBox4.Enabled = True
                self.apply_bt.Enabled = True
                

if __name__ == '__main__':
    try:
        form = MyForm()
        form.ShowDialog()
        form = MyForm()
        form.Dispose()
        #form.Show()
        #oDesktop.PauseScript()
    except:
        logging.exception('ERROR!')

```
{% endcode %}

</details>
