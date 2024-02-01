# 第一章	窗体

1. P18将listbox的Items.Add放在Load事件中

2. ShowDialog显示模态窗体，只有关闭窗体才执行后面代码

3. P21将窗体隐藏，如果后面还要用。用hide还是visible=false更好？？

4. MDI窗体不同于SDI，就是窗体间有父子和兄弟关系的。父窗体可以对子窗体层叠、平铺等。

   一般在父窗体的load事件中new子窗体，然后设置子窗体的父窗体

   ```C#
   Form2 childForm = new Form2();
   childForm.Mdiparent = this;
   childForm.Show();
   ```

   子窗体只能在父窗体范围内活动，不能超出范围。

* 指定层叠、水平平铺、垂直平铺等

```C#
this.LayoutMdi(MdiLayout.Cascade);	//层叠
this.LayoutMdi(MdiLayout.TileHorizontal);	//水平平铺
this.LayoutMdi(MdiLayout.TileVertical);	//垂直平铺
```

### MDI主窗口作为父窗体，非MDI窗体作为子窗体。子窗体之间的传值问题。

* 子窗体向子窗体传值——通过构造传值：new出来的子窗体要设置MdiParent

```C#
Form2 childForm = new Form2();
childForm.MdiParent = this.MdiParent;	//表示两个子窗体父窗体相同
childForm.Show();
this.Close();
```

* B子窗体设置属性。A子窗体new出来B子窗体后，通过B的属性直接赋值

  ```C#
  this.Hide();
  Form3 childFormB = new Form3();
  childFormB.val1=this.textBox1.Text;
  childFormB.val2=this.textBox2.Text;
  childFormB.MdiParent=this.MdiParent;
  childFormB.Show();
  ```

* 防止点击某一个按钮重复打开多个同一窗口

  ```c#
  
  ```


5. 添加menu

   选择menustrip控件添加，右键属性更改name和text。

   右键选择`编辑项`（或在属性的Items调出窗口修改）添加子选项，并编辑二级选项的name、text、shortcutkey。

   在二级选项上右键，选择`编辑DropDownItems`添加修改二级选项并修改name、text和shortcutkey。

6. 使用第三方皮肤

   https://www.cnblogs.com/shaozhuyong/articles/3125402.html

   自带设计的窗体都是很普通的，不够美观。使用第三方皮肤DotNetSkin.dll和IrisSkin2.dll可以得到较为美观的皮肤。皮肤文件.skin和.ssk。

## 7. 菜单栏、工具栏、状态栏、右键菜单

https://www.cnblogs.com/yieryi/p/4602765.html

菜单栏——menustrip

工具栏——toolstrip，添加各种控件且可设置图片

状态栏——statusstrip

鼠标右键菜单——contextmenustrip，右键点击`编辑项`添加和编辑。**要在窗体的contextMenuStrip属性上选择该控件，右键菜单才有效**

toolstripcontainer控件——使用不是很方便。 其实就是个工具栏容器，软件运行起来后，允许我们将工具栏拖放到我们需要的地方（四边的四个框框）。中间的框框用来放其他控件。删除用delete键。

# 第二章	控件

## 1. radiobutton

和CheckBox的区别是，一个分组内一次只能选择一个。

分组才能实现不能同时选择，一般用panel或groupbox。

panel——默认无边框，可设置AutoScroll有滑动条。设置BorderStyle，可有边框。

groupbox——有一个小标题，默认有框框。

## 2. tabControl——选项卡

### （1）属性

> MultiLine——选项卡是否多行
>
> Appearance——选项卡外观（Button外观）
>
> ShowToolTips——鼠标移动到选项卡时是否提示信息。若设置true，在选项卡的属性`ToolTipText`中修改提示信息内容
>
> **TabPages**——选项卡编辑器（可编辑选项卡每页。也可直接点击选项卡下的框框编辑每页）
>
> SelectedIndex——被选的选项卡的index，从0编号，默认为-1
>
> SelectedTab——当前选中的选项卡，未选中时为NULL。
>
> TabCount——选项卡总数

```C#
private void tabControl1_SelectedIndexChanged(object sender, EventArgs e)
{
	string msg = string.Empty;
	msg = "当前页面为：" + this.tabControl1.SelectedTab.Text + "，是第" + 			                     this.tabControl1.SelectedIndex.ToString() + "页" + "，共" + this.tabControl1.TabCount + "页";
	MessageBox.Show(msg);
}
```

### （2）事件



## 3. progressBar——进度条

### （1）属性

> Maximum——最大值
>
> Minimum——最小值
>
> Step——步长
>
> Value——当前值

### （2）方法

```C#
void Increment(int val)	//按照设定的val作为步长增加一次
```

```C#
void PerformStep()	//按照属性中的Step作为步长增加一次
```

```C#
while (this.progressBar1.Value != this.progressBar1.Maximum)
{
	this.progressBar1.Value+=this.progressBar1.Step;
}
```

## 4. ImageList——封装图片的组件

不能单独显示图片，作为控件，搭配其他控件使用，如toolbar，属性ImageList选择已有ImageList。

### （1）属性

> Images——图片集合。点击，弹出`图片集合编辑器`添加图片
>
> ImageSize——图片显示尺寸



## 5. ToolBar——工具栏（老版本控件了不建议用，用ToolStrip）

### （1）属性

> ImageList——选择
>
> Style：工具栏按钮的样式
>
> ShowToolTips——鼠标放在按钮上是否显示提示
>
> **Buttons**——打开`ToolBarButton集合编辑器`。添加按钮，编辑按钮属性

ToolBarButton按钮属性：

> ImageIndex——按钮选择的ImageList封装的图片的序号
>
> ToolTipsText——按钮提示内容

### （2）demo

```C#
private void Form_Load(object sender, EventArgs e){
    //new按钮
    ToolBarButton bt1 = new ToolBarButton();
    ToolBarButton bt2 = new ToolBarButton();
    ToolBarButton bt3 = new ToolBarButton();
    ToolBarButton bt4 = new ToolBarButton();
    ToolBarButton bt5 = new ToolBarButton();
    //按钮添加到ToolBar上
    ToolBar1.Buttons.Add(bt1);
    ToolBar1.Buttons.Add(bt2);
    ToolBar1.Buttons.Add(bt3);
    ToolBar1.Buttons.Add(bt4);
    ToolBar1.Buttons.Add(bt5);
    //ToolBar添加ImageList
    ToolBar1.ImageList=imageList1;
 	ToolBar1.ShowToolTips=true;
    //为按钮设置ImageList中图片
    bt1.ImageIndex=0;
    bt2.ImageIndex=1;
    bt3.ImageIndex=2;
    bt4.ImageIndex=3;
    bt5.ImageIndex=4;
    
    //按钮属性设置
    bt1.Text="button1";
    bt1.ToolTipText="button1 提示信息";
    bt2.Text="button1";
    bt2.ToolTipText="button2 提示信息";
    bt3.Text="button1";
    bt3.ToolTipText="button3 提示信息";
    bt4.Text="button1";
    bt4.ToolTipText="button4 提示信息";
    bt5.Text="button1";
    bt5.ToolTipText="button5 提示信息";
}
```

## 6. StatusStrip——状态栏

StatusStrip可添加进度条

```C#
while (this.toolStripProgressBar1.Value != this.toolStripProgressBar1.Maximum)
{
	this.toolStripProgressBar1.Value += this.toolStripProgressBar1.Step;
}
```

## 7. numericUpDown——微调框

### （1）属性

> Maximum——最大值
>
> Minimum——最小值
>
> Value——当前值
>
> TextAlign——数字在框中的位置
>
> UpDownAlign——微调按钮的位置
>
> Increment——增长的步长

## 8. DataTimePicker——调整时间

### （1）属性

> showcheckbox——是否显示复选框
>
> checked——复选框是否选中
>
> showupdown——false显示下拉日历。true为时间可调
>
> value——当前时间

## 9. openFileDialog和saveFileDialog——文件读写对话框

### （1）属性

> FileName——第一个对话框中显示的文件，或用户选择的最后一个文件
>
> Title——将显示在对话框标题栏中的字符串
>
> InitialDirectory——对话框的初始目录
>
> Filter——对话框中显示的文件筛选器，例如"C#文件|*.cs|所有文件|*.*"
>
> DefaultExt——默认的扩展名，当用户键入文件名时，如果未指定扩展名，将在文件名后添加此扩展名
>
> Multiselect——控制是否可以在该对话框中选择多个文件

### （2）事件

> FileOk——当用户对话框单击"打开"或"保存"按钮是发生

```C#
private void FileOpen_Click(object sender, EventArgs e)
 2         {
 3             OpenFileDialog openFile = new OpenFileDialog();//创建OpenFileDialog对象
 4 
 5             openFile.InitialDirectory = @"E:\";//打开初始目录
 6             openFile.Title = "选择打开文件";//窗体标题
 7             openFile.Filter = "txt files (*.txt)|*.txt|All files (*.*)|*.*";//过滤条件
 8             openFile.FilterIndex = 2;//获取第二个过滤条件开始的文件
 9             openFile.Multiselect = true;//是否多选
10 
11             if (openFile.ShowDialog() == DialogResult.OK)//页面弹出判断是否点击确定按钮
12             {
13                 //没勾选多选时
14                 //string filename = openFile.FileName;
15                 //string name=openFile.SafeFileName; 
16 
17                 //勾选多选时
18                 for (int i = 0; i < openFile.SafeFileNames.Length; i++)//获取文件名，拓展名
19                 {
20                     rictbo.Text += openFile.SafeFileNames[i] + "\r\n";
21                 }
22                 for (int i = 0; i < openFile.FileNames.Length; i++)//获取文件全部路径
23                 {
24                     rictbo.Text += openFile.FileNames[i] + "\r\n";
25                 }
26             }
27         }
```

```C#
public partial class FileDialogForm : Form
{
    public FileDialogForm()
    {
        InitializeComponent();
    }
    //打开文件
    private void button1_Click(object sender, EventArgs e)
    {
        DialogResult dr = openFileDialog1.ShowDialog();
        //获取所打开文件的文件名
        string filename = openFileDialog1.FileName;
        if(dr==System.Windows.Forms.DialogResult.OK && !string.IsNullOrEmpty(filename))
        {
            StreamReader sr = new StreamReader(filename);
            textBox1.Text = sr.ReadToEnd();
            sr.Close();
        }
    }
    //保存文件
    private void button2_Click(object sender, EventArgs e)
    {
        DialogResult dr = saveFileDialog1.ShowDialog();
        string filename = saveFileDialog1.FileName;
        if(dr==System.Windows.Forms.DialogResult.OK && !string.IsNullOrEmpty(filename))
        {
            StreamWriter sw = new StreamWriter(filename, true, Encoding.UTF8);
            sw.Write(textBox1.Text);
            sw.Close();
        }
    }
}
```

## 10. folderBrowserDialog——文件夹浏览对话框

### （1）打开对话框

```C#
FolderBrowserDialog folderDlg = new FolderBrowserDialog();
DialogResult drs = folderDlg.ShowDialog();
if (drs == DialogResult.OK){
    //code
}
else{
    //code
}
```

### （2）记忆上一次选择的路径（软件关闭失效）

```C#
DataTable dtGrid = (DataTable)this.gridControl_faultHistory.DataSource;
            string path = String.Empty;
            FolderBrowserDialog folderDlg = new FolderBrowserDialog();
            folderDlg.SelectedPath = defaultFolder;     //由上次打开的路径处打开
            DialogResult drs = folderDlg.ShowDialog();
            if (drs == DialogResult.OK)
            {
                defaultFolder = folderDlg.SelectedPath;		//更新默认路径为上次打开的路径
                path = defaultFolder + "\\faultsHistory";

                if (!Directory.Exists(path))
                {
                    Directory.CreateDirectory(path);
                }

                string excelFileName = path + "\\faultsHistory" + excelFileNameIndex++.ToString() + ".xlsx";
                FileStream filestream = new FileStream(excelFileName, FileMode.OpenOrCreate);

                XSSFWorkbook wk = new XSSFWorkbook();
                ISheet isheet = wk.CreateSheet("Sheet1");

                IRow row = null;
                ICell cell = null;
                //加表头
                row = isheet.CreateRow(0);
                cell = row.CreateCell(0);
                cell.SetCellValue("序号");
                cell = row.CreateCell(1);
                cell.SetCellValue("产线名称");
                cell = row.CreateCell(2);
                cell.SetCellValue("设备名称");
                cell = row.CreateCell(3);
                cell.SetCellValue("故障名称");
                cell = row.CreateCell(4);
                cell.SetCellValue("故障时间");

                for (int i = 1; i < dtGrid.Rows.Count + 1; i++)
                {
                    row = isheet.CreateRow(i);
                    for (int j = 0; j < 5; j++)
                    {
                        cell = row.CreateCell(j);
                        cell.SetCellValue(dtGrid.Rows[i - 1][j].ToString());
                    }

                }

                wk.Write(filestream);   //通过流filestream将表wk写入文件
                filestream.Close(); //关闭文件流filestream
                wk.Close(); //关闭Excel表对象wk
                MessageBox.Show("Excel导出");
            }
            else if(drs == DialogResult.Cancel)
            {
                folderDlg.Dispose();
            }
```

### （3）记忆上一次选择的路径（软件重开后仍然有效）

#### 注册表法

https://www.cnblogs.com/BensonHe/archive/2010/07/24/1784432.html

C#中的FolderBrowserDialog有个RootFolder属性，每次打开文件夹对话框默认指向的就是这个目录。但如果我们需要频繁打开同一个目录，则可以在ShowDialog()函数执行前，对SelectedPath属性赋值，这在程序退出前都有效，如果想要程序下次运行还是打开这个目录，则需要将打开的路径记录下来。可以使用两种方法：一、使用文件记录；二、使用注册表。使用文件记录有一个不好的地方，就是除了可执行程序exe外，还必须有一个配置文件，用户是可知的，这样非常的不好。使用注册表则没有这种烦恼。

```C#
DataTable dtGrid = (DataTable)this.gridControl_faultHistory.DataSource;
            string path = String.Empty;

            FolderBrowserDialog folderDlg = new FolderBrowserDialog();
            //注册表法记录上次打开路径（软件重开后仍然有效）
            try
            {

                RegistryKey testKey = Registry.CurrentUser.OpenSubKey("TestKey");
                if (testKey == null)
                {
                    testKey = Registry.CurrentUser.CreateSubKey("TestKey");
                    testKey.SetValue("OpenFolderDir", "");
                    testKey.Close();
                    Registry.CurrentUser.Close();
                }
                else
                {
                    defaultFolder = testKey.GetValue("OpenFolderDir").ToString();
                    testKey.Close();
                    Registry.CurrentUser.Close();
                }
            }
            catch (Exception e)
            {
            }

            if (defaultFolder != "")
            {
                //设置此次默认目录为上一次选中目录                  
                folderDlg.SelectedPath = defaultFolder;
            }

            DialogResult drs = folderDlg.ShowDialog();
            if (drs == DialogResult.OK)
            {
                if (defaultFolder != folderDlg.SelectedPath)
                {
                    defaultFolder = folderDlg.SelectedPath;
                    RegistryKey testKey = Registry.CurrentUser.OpenSubKey("TestKey", true);  //true表示可写，false表示只读
                    testKey.SetValue("OpenFolderDir", defaultFolder);
                    testKey.Close();
                    Registry.CurrentUser.Close();
                }

                path = defaultFolder + "\\faultsHistory";

                if (!Directory.Exists(path))
                {
                    Directory.CreateDirectory(path);
                }

                string excelFileName = path + "\\faultsHistory" + excelFileNameIndex++.ToString() + ".xlsx";
                FileStream filestream = new FileStream(excelFileName, FileMode.OpenOrCreate);

                XSSFWorkbook wk = new XSSFWorkbook();
                ISheet isheet = wk.CreateSheet("Sheet1");

                IRow row = null;
                ICell cell = null;
                //加表头
                row = isheet.CreateRow(0);
                cell = row.CreateCell(0);
                cell.SetCellValue("序号");
                cell = row.CreateCell(1);
                cell.SetCellValue("产线名称");
                cell = row.CreateCell(2);
                cell.SetCellValue("设备名称");
                cell = row.CreateCell(3);
                cell.SetCellValue("故障名称");
                cell = row.CreateCell(4);
                cell.SetCellValue("故障时间");

                for (int i = 1; i < dtGrid.Rows.Count + 1; i++)
                {
                    row = isheet.CreateRow(i);
                    for (int j = 0; j < 5; j++)
                    {
                        cell = row.CreateCell(j);
                        cell.SetCellValue(dtGrid.Rows[i - 1][j].ToString());
                    }

                }

                wk.Write(filestream);   //通过流filestream将表wk写入文件
                filestream.Close(); //关闭文件流filestream
                wk.Close(); //关闭Excel表对象wk
                MessageBox.Show("Excel导出");
            }
            else if(drs == DialogResult.Cancel)
            {
                folderDlg.Dispose();
            }
```

#### 读写文件法

https://blog.csdn.net/D_lunar/article/details/84991615

# 第三章	文件读写

https://blog.csdn.net/deni2000/article/details/102023993

计算机上的文件基本上分为2种，文本文件（ASCII文件）和二进制文件，图形和程序都属于二进制文件。

## 1. System.IO命名空间常用的类

> File——提供**文件**的打开、创建、删除、复制、移动的**静态**方法
>
> FileInfo——同File，提供的是**实例**方法（需先创建对象再使用）
>
> Directory——提供**文件夹**的创建、移动的**静态**方法
>
> DirectoryInfo——提供**实例**方法
>
> StreamWriter——以指定编码向字节流中写入字符
>
> StreamReader——以指定编码从字节流中读出字符
>
> FileStream——文件流，操作文件
>
> BinaryWriter——用指定编码以二进制形式将基元数据写入流
>
> BinaryReader——将基元数据以二进制读出

* File类的方法为静态方法，可直接使用。FileInfo需要创建实例关联文件，以实例调用方法。

* File性能低，若要多次操作文件，使用FileInfo。
* **FileStream、StreamWriter/StreamReader、BinaryWriter/BinaryReader使用完记得.Close()关闭流。**

## 2. FileStream——文件流

**以字节byte为单位操作数据。**

提供文件读写，必须要实例化对象后才可操作文件。

用FileStream关联文件，再看具体是文本文件还是二进制文件。选择用StreamWriter或StreamReader读写文本。或BinaryWriter或BinaryReader读写二进制文件。

### （1）构造

```C#
string path = "";
FileStream fs = new FileStream(path, FileMode.OpenorCreate, FileAccess.Write, FileShare fileshare, Int32 bufferSize);
```

**FileMode**——打开文件的方式

> OpenOrCreate——若已存在则打开，若不存在则创建
>
> CreateNew——创建新文件
>
> Create——文件不存在则创建，若已存在则覆盖
>
> Append——在现有文件后添加，与FileAccess.Write一起使用
>
> Truncate——覆盖文件

**FileAccess**——Read、Write、ReadWrite

**FileShare**——是否允许其他线程共享访问：None（拒绝共享）、Read、Write、ReadWrite（同时读写）、Delete

**bufferSize**——缓冲区大小

### （2）方法

```C# 
int fs.Read(byte[] buffer, int offset, int count);	//从流中将从offset起的count个字节读取到缓冲区字节数组buffer中
```

```C#
void fs.Write(byte[] buffer, int offset, int count);
```

```C# 
close();	//关闭文件流
Dispose();	//释放流使用的所有资源
```

```C#
CopyTo(Stream)	//从当前流中读取所有字节并将其写入目标流。具体使用查资料
```

## 3. StreamWriter/StreamReader

StreamWriter、StreamReader读写文本文件，**以字符char为单位操作数据。**

与FileStream相比，StreamWriter和StreamReader封装了更多的方法，前者更加的底层、多用于大文件的读写。

### （1）构造

```C#
FileStream fs = new FileStream(path, FileMode.OpenorCreate, FileAccess.ReadWrite);
StreamWriter sw = new StreamWriter(fs);
StreamReader sr = new StreamReader(fs);
```

### （2）方法

> * sr.ReadLine()——读取一行
>
>   仅仅需要读取文本的第一行。因为如果整个文本读取出来再获得第一行的话，比较占用内存。因为那个文本足足有几M大。因此用到了StreamReader的ReadLine()方法。
>
> * sr.ReadToEnd()——从开头读取到末尾
>
> * sr.Read()——读取流的下一个字符或一组字符
> * sr.Close()——关闭当前流、释放资源

> * sw.WriteLine()——写入一行，添加行结束符
> * sw.Write()——写入数据
> * sw.Close()——关闭流，释放资源

## 4.BinaryWriter/BinaryReader

BinaryWriter、BinaryReader读写二进制文件（**图片等文件**）

### （1）构造

构造必须要对FileStream对象上进行封装。

```C#
FileStream fs = new FileStream(path, FileMode.OpenorCreate, FileAccess.ReadWrite);
BinaryWriter bw = new BinaryWriter(fs);
//BianryWriter bw = new BinaryWriter(File.Open(path, FileMode.OpenorCreate, FileAccess.ReadWrite));
bw.Write(autoSaveTime);
bw.Close();
fs.Close();
```

### （2）读二进制文件

```C#
//从当前流中读取指定的字节数以写入字节数组中，并将当前位置前移相应的字节数
public virtual byte[] binaryReader.ReadBytes(int len);
```

```c#
FileStream fs = new FileStream(path, FileMode.OpenorCreate, FileAccess.Read);
BinaryReader binaryReader = new BinaryReader(fs);
byte[] fileBytes = binaryReader.ReadBytes((int)fs.Length)	//FileStream.Length获得文件大小字节数
```

### （3）写二进制文件

```C#
public virtual void Write(char[] chars, int index, int count);
public virtual void Write(string value);
public virtual void Write(float value);
public virtual void Write(ulong value);
public virtual void Write(long value);
public virtual void Write(uint value);
public virtual void Write(int value);
public virtual void Write(ushort value);
public virtual void Write(short value);
public virtual void Write(byte[] buffer, int index, int count);
public virtual void Write(double value);
public virtual void Write(char[] chars);
public virtual void Write(char ch);
public virtual void Write(byte[] buffer);
public virtual void Write(sbyte value);
public virtual void Write(byte value);
public virtual void Write(bool value);
public virtual void Write(decimal value);
```

```c#
byte[] fileBytes;
binaryweader.Write(fileBytes);	//将二进制流fileBytes写入文件
binaryweader.flush();	//清理“编写器”的缓冲区，将数据写入基础设备
binaryweader.Close();
```

## 

## 5. File类和FileInfo类

### （1）File

> bool Exists(string path)——判断指定路径的文件是否存在
>
> **FileStream** Open(string path, FileMode mode, FileAccess access, Fileshare fileshare)——打开指定文件，返回的FileStream提供对打开文件的访问
>
> **FileStream** Create(string path)——在指定路径创建文件/覆盖已有文件。返回的FileStream提供访问
>
> void Delete(string path)——删除指定文件。文件不存在不报异常
>
> void Copy(string sourcePath, string targetPath)——将源路径的文件拷贝到目标路径
>
> void Move(string sourcePath, string targetPath)——将源路径文件移动到目标路径
>
> **StreamWriter** CreateText(string path)——创建一个通过UTF-8写入的文件。返回StreamWriter
>
> **StreamReader** OpenText(string path)——打开UTF-8编码文件进行读取。返回StreamReader
>
> **SreamWriter** AppendText(string path)——通过StreamWriter将UTF-8文本添加到文件

```C#
string file = @"C:\1.txt";
bool flag = File.Exists(file);
```

* File.Open方法

  ```C#
  string path = "";
  FileStream fs;
  fs = File.Open(path, FileMode.OpenorCreate, FileAccess.Read, FileShare fileshare);
  ```

* File.Create方法

  ```C#
  string path = "";
  FileStream fs = File.Create(path, FileMode.Create, FileAccess.Write, FileShare fileshare);
  ```

### （2）FileInfo类

**一个实例关联一个文件**

```C#
string file = @"C:\1.txt";
FileInfo fileinfo = new FileInfo(file);
bool flag = fileinfo.Exists;
```

### （1）属性

> Name——文件名string
>
> Exists——文件是否存在bool
>
> Directory——获取目录的实例DirectoryInfo
>
> DirectoryName——目录的完整路径的字符串string
>
> length——文件大小（字节为单位）long
>
> CreationTime——创建时间DateTime
>
> public string Extension {get, }——获取文件后缀的字符串

### （2）方法

> **FileStream** Open(string path, FileMode mode, FileAccess access, Fileshare fileshare)
>
> **FileStream** Create()——创建文件
>
> void Delete()——删除文件
>
> **FileInfo** copyTo(string targetFileName)——拷贝
>
> **FileInfo** MoveTo(string targetFileName)——移动
>
> **StreamWriter** CreateText()——**创建**新文件，返回写入新文本文件的StreamWriter
>
> **StreamWriter** AppendText()——向当前实例关联的文件中创建一个**追加**文本的StreamWriter
>
> **StreamReader** OpenText()——创建从当前文件中以UTF-8**读取**字符的StreamReader

## 6. Directory类和DirectoryInfo类

### （1）Directory类

> bool Exists(string path)——目录是否存在
>
> **DirectoryInfo** CreateDirectory(string path)——创建目录
>
> void Delete(string path)——删除指定目录
>
> void Move(string sourceDirName, string targetDirName)——移动文件夹
>
> string GetCurrentDirectory()——获取当前目录
>
> **string[] GetFiles(string path)**——获取目录中所有文件名（包含路径）
>
> DateTime GetCreationTime(string path)——获取路径的创建时间
>
> DateTime GetLastAccessTime(string path)——获得上次访问指定文件/目录的时间
>
> DateTime GetLastWriteTime(string path)——获得上次写入指定文件/目录的时间

```C#
public void Main(){
    try{
        	DateTime dt = Directory.GetCreateTionTime(Environment.CurrentDirectory);
        	if(DateTime.Now.Subtract(dt).TotalDays>365){
            	Console.WriteLine("this directory is over a year old..");
        	}
    }
    catch(Exception ex){
        ex.toString();
    }
}
/**
*            DateTime endTime = new DateTime(2016,4,10);
*            var startTime=DateTime.Now;
*            startTime.Subtract(endTime);	//时间差
*/
```

```C#
//文件夹操作函数
//out表示引用传值，同ref。https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/out-parameter-modifier
public bool directoryOption(string dirPath, string targetDirPath, Int16 optionMethod, out string[] filesName){
    bool flag = false;
    filesName=null;
    if(Directory.Exists(dirPath)){
        try{
        		if(optionMethod==0){
                    //创建文件夹
                    Directory.Create(dirPath);
                    flag = true;
                }else if(optionMethod==1){
                    //删除文件夹
                    Directory.Delete(dirPath);
                    flag = true;
                }else if(optionMethod==2){
                    //移动文件夹
                    Directory.Move(dirPath,targetDirPath);
                    flag = true;
                }else if(optionMethod==3){
                    //获取文件夹下所有文件的文件名
                    filesName=Directory.GetFiles(dirPath);
                }
   		   }
    	catch(Exception ex){
        	ex.toString();
    	 }
    }else{
        Directory.Create(dirPath);	//不存在则创建
        flag = true;
    }
    return flag;
}
```

公开用于创建、移动和枚举目录和子目录的**实例方法**。

### （2）DirectoryInfo类

> CreationTime——获取/设置当前文件夹的创建时间
>
> CreationTimeUtc——获取/设置文件夹创建时间，为世界时格式
>
> Exists——文件夹是否存在
>
> public DirectoryInfo Parent {get, }——获取当前目录的父目录
>
> Name——返回目录名
>
> FullName——返回目录名完整路径
>
> Root——返回根目录

## 7. MemoryStream——读写内存流

MemoryStream为系统内存提供流式的读写操作。常作为其他流数据交换时的中间对象操作。

* MemoryStream类封装一个字节数组，即内存中一块连续区域（缓冲区Buffer），每个数组元素存储一个字节的数据。

  在构造实例时可以使用一个字节数组作为参数，但是数组的长度无法调整。使用默认无参数构造函数创建实例，可以使用Write方法写入，随着字节数据的写入，数组的大小自动调整

```C#
//构造函数
MemoryStream();	//buffer初始容量为0，但是可动态增长
MemoryStream(byte[]);	//使用指定字节数组初始化内存流，字节数组长度固定了无法调整
MemoryStream(byte[], Boolean);	//同上，多一个可设置CanWrite属性
```

* 在对MemoryStream类中数据流进行读取时，可以使用seek方法定位读取器的当前的位置，可以通过指定长度的数组一次性读取指定长度的数据。ReadByte方法每次读取一个字节，并将字节返回一个整数值。

  MemoryStream继承自Stream类。内存流的好处是指针可以晃来晃去，也就是支持CanSeek，Position,Seek()。任意读其中一段。

  SeekOrigin

  > Begin	指定流的开头
  >
  > Current	指定流的当前位置
  >
  > End	指定流的结尾

* MemoryStream中没有任何非托管资源，所以它的Dispose不调用也没关系。托管资源.Net会自动回收

### （1）属性

> CanWrite 　　　 已重写。获取一个值，该值指示当前流是否支持写入
>
> CanRead 　　　　已重写。获取一个值，该值指示当前流是否支持读取
>
> CanSeek 　　　　已重写。获取一个值，该值指示当前流是否支持查找
>
> Capacity 　　　　获取或设置分配给该流的字节数。 这个是分配的字节数
>
> Length 　　　　 已重写。获取用字节表示的流长度。这个是真正占用的字节数
>
> Position 　　　　 已重写。获取或设置流中的当前位置。

### （2）方法

> void Write(byte[] buffer, int offset, int count)	已重写。 使用从缓冲区读取的数据将字节块写入当前流。
>
> int ReadByte()	已重写。 从当前流中读取一个字节。
>
> long Seek(long offset, SeekOrigin loc) 	已重写。 将当前流中的位置设置为指定值。起点loc，偏移量offset

## 8. 开发中使用的code

https://blog.csdn.net/Yandning/article/details/126746332?app_version=5.7.3&csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%22126746332%22%2C%22source%22%3A%22unlogin%22%7D&utm_source=app

### （1）文件流FileStream直接读写字节数组

```C#
using System.IO;

void FileStreamRW(string filePathR, string filePathW){
    #region	读取文件字节到字节数组buffer
    byte[] buffer;
    using(FileStream fs = new FileStream(filePathR, FileMode.Open, FileAccess.Read)){
        buffer = new byte[fs.Length];		//FileStream以字节为单位进行操作
        fs.Read(buffer, 0, buffer.Length);	//从流fs中读取offset=0、count=buffer.Length的这块字节写入缓冲区buffer中
    }
    #endregion
        
    #region	将buffer中内容添加文字后写入文件
    using(FileStream fs = new FileStream(filePathW, FileMode.Create, FileAccess.Write)){
        buffer = Encoding.UTF8.GetBytes(Encoding.UTF8.GetString(buffer)+"\n欲穷千里目");	//在buffer的基础上添加文字
        fs.Write(buffer, 0, buffer.Length);		//将buffer中offset=0、count=buffer.Length的内容写到文件中
    }
    #endregion
}
```

### （2）文本流StreamWriter/StreamReader读写字符

```C#
using System.IO;

void StreamRW(){
    string text = "";
    #region 读取
    using (StreamReader reader = new StreamReader(filePath3))
    {
        text = reader.ReadToEnd();	//从头读到尾
        text = reader.ReadLine();	//读取一行
        Console.WriteLine(text);
    }
    #endregion

    #region 写入
    using (FileStream fs = new FileStream(filePath4, FileMode.Create))
    {
        using (StreamWriter writer = new StreamWriter(fs))
        {
            writer.Write(text+"\nThis is tuesday!");
        }
    }
    #endregion
}
```

### （3）二进制流BinaryWriter/BinaryReader读写

```C#
void BinaryDo()
{
    #region 读取
    byte[] buffer;
    //方式1
    //Stream类操作字符数据。字符数据易于使用
    using (Stream stream = File.Open(filePath2, FileMode.Open))
    {
        using (BinaryReader reader = new BinaryReader(stream))
        {
            buffer = reader.ReadBytes((int)stream.Length); //将流读入到字节数组中
        }
    }
    #endregion
    #region 写入
    using (Stream stream = File.Open(filePath3, FileMode.Create))
    {
        using (BinaryWriter writer = new BinaryWriter(stream))
        {
            writer.Write(Encoding.UTF8.GetBytes(Encoding.UTF8.GetString(buffer) + "\n更上一层楼"));
        }
    }
    #endregion
}
```

### （4）读写JSON

```C#
Install - Package Newtonsoft.Json	//首先安装依赖包
```

```json
config.json内容如下：

{
  "server": "1.1.1.1",
  "user": "sa",
  "passwd": "1"
}
```

```C#
void Get_Json()
{
    try
    {
        StreamReader reader = File.OpenText(filePath5);
        JsonTextReader jsonTextReader = new JsonTextReader(reader);
        JObject jsonObject = (JObject)JToken.ReadFrom(jsonTextReader);
        string server = jsonObject["server"].ToString(); //user ,passwd 类似
        reader.Close();
        Console.WriteLine(server);
    }
    catch (Exception ex)
    {
        Console.WriteLine("读取错误" + ex);
    }
}

void Set_Json(string server)
{
    try
    {
        StreamReader reader = File.OpenText(filePath5);
        JsonTextReader jsonTextReader = new JsonTextReader(reader);
        JObject jsonObject = (JObject)JToken.ReadFrom(jsonTextReader);
        jsonObject["server"] = server; 
        reader.Close();
        string output = JsonConvert.SerializeObject(jsonObject, Newtonsoft.Json.Formatting.Indented);
        File.WriteAllText(filePath5, output);
    }
    catch(Exception ex)
    {
        Console.WriteLine("写入错误"+ex);
    }
}

//Set_server("1.1.1.2");
//Get_server();
```



### （5）读写XML

```xml
创建两个.xml文件，内容如下：
不带ID的文件:
<?xml version="1.0" encoding="utf-8" ?>
<studentList>
	<student>
		<name>张三</name>
		<sex>男</sex>
		<old>20</old>
	</student>
	<student>
		<name>李四</name>
		<sex>女</sex>
		<old>21</old>
	</student>
</studentList>

```

```xml
<?xml version="1.0" encoding="utf-8" ?>
<studentList>
	<student Type="Bad" ISBN="541801">
		<name id="1">张三</name>
		<sex id="11">男</sex>
		<old id="111">20</old>
	</student>
	<student Type="Good" ISBN="541802">
		<name id="2">李四</name>
		<sex id="22">女</sex>
		<old id="222">21</old>
	</student>
</studentList>
```

```C#
void DataSet_ReadXml()
{
    //dataset读取简单xml文件
    DataSet ds = new DataSet();
    ds.ReadXml(filePath7);
    //读取第一条数据的name节点
    string name = ds.Tables[0].Rows[0]["name"].ToString();
    //输出：张三
    Console.WriteLine(name);
}

void DataSet_ReadXml_id()
{
    //dataset读取带属性的xml文件
    DataSet ds = new DataSet();
    ds.ReadXml(filePath8);
    //读取的节点名称
    string nodeName = "name";
    string name = "";
    name = ds.Tables[nodeName].Rows[0][nodeName + "_Text"].ToString();
    //读取节点的id属性
    string id = ds.Tables[nodeName].Rows[0]["id"].ToString();
    //输出：id:1,name:张三
    Console.WriteLine("id:{0},name:{1}", id, name);
}
//DataSet_ReadXml();
//DataSet_ReadXml_id();
```

### （6）读写EXCEL

```C#
NPOI方式

```

```C#
以ClosedXML的方式读写

    
//添加相应的NuGet包
//using ClosedXML.Excel;
void Excle_ClosedXml()
{
    var workbook = new XLWorkbook(filePath11);
    IXLWorksheet sheet = workbook.Worksheet(1);//这个库也是从1开始
    //设置第一行第一列值,更多方法请参考官方Demo
    sheet.Cell(1, 5).Value = "Excle_ClosedXml";//该方法也是从1开始，非0
    workbook.SaveAs(filePath12);
}
//Excle_ClosedXml();
//导出不支持覆盖，如果路径12的文件存在则报错？
```

```C#
以Spire.Xls的方式读写
    
    
//如果需要在WinForm中展示，那么Spire.Office可能是唯一选择。
//Nuget下载Free Spire.xls
void Excle_spire()
{
    Spire.Xls.Workbook workbook = new Spire.Xls.Workbook();
    workbook.LoadFromFile(filePath11);
    //处理Excel数据，更多请参考官方Demo
    Spire.Xls.Worksheet sheet = workbook.Worksheets[0];
    sheet.Range[1, 6].Text = "Excle_spire";//该方法也是从1开始，非0
    workbook.SaveToFile(filePath12);
}
//Excle_spire();
//覆盖式导出
```

```C#
以EPPlus的方式读写
    
    
//如果只是需要读写Excel，那么EPPlus非常方便而且符合使用习惯。
//using OfficeOpenXml;
void TestEPPlus()
{
    ExcelPackage.LicenseContext = LicenseContext.NonCommercial;//指明非商业应用；EPPlus通过LicenseContext来区分商业应用（Commercial）和非商业应用（NonCommercial）
    ExcelPackage package = new ExcelPackage(filePath12);//加载Excel工作簿
    ExcelWorksheet sheet1 = package.Workbook.Worksheets["Sheet1"];//读取工作簿中名为"Sheet1"的工作表

    sheet1.Cells[1, 1].Value = "A";//设置单元格内容
    sheet1.Cells[2, 2].Value = "B";
    sheet1.Cells[3, 3].Value = "C";
    //package.Save();//将更改保存到原文件
    package.SaveAs(filePath12);//将更改保存到新的文件，类似于另存为
}
//TestEPPlus();
```



### （7）读写CSV

```C#
DataTable CSV2DataTable(string fileName)
{
    try
    {
        DataTable dt = new DataTable();
        FileStream fs = new FileStream(fileName, FileMode.Open, FileAccess.Read);
        StreamReader sr = new StreamReader(fs, Encoding.Default);
        //记录每次读取的一行记录
        string strLine = "";
        //记录每行记录中的各字段内容
        string[] aryLine;
        //标示列数
        int columnCount = 0;
        //标示是否是读取的第一行
        bool IsFirst = true;
        //逐行读取CSV中的数据
        while ((strLine = sr.ReadLine()) != null)
        {
            aryLine = strLine.Replace('"', ' ').Replace(" ", "").TrimEnd(',').Split(',');
            if (IsFirst == true)
            {
                IsFirst = false;
                columnCount = aryLine.Length;
                //创建列
                for (int i = 0; i < columnCount; i++)
                {
                    DataColumn dc = new DataColumn(aryLine[i]);
                    try
                    {
                        dt.Columns.Add(dc);
                    }
                    catch (Exception ex)
                    {
                        return null;
                    }
                }
            }
            else
            {
                DataRow dr = dt.NewRow();
                for (int j = 0; j < columnCount; j++)
                {
                    dr[j] = aryLine[j];
                }
                dt.Rows.Add(dr);
            }
        }
        sr.Close();
        fs.Close();
        return dt;
    }
    catch (Exception)
    {
        return null;
    }
}
//仅能读取简单的CSV
//DataTable dt = CSV2DataTable(filePath9);
//for (int i = 0; dt.Rows.Count<i; i++)
//{
//    Console.Write("");
//}

```









```C#
string path = @"";

/*
1.用FileStream关联文件，再依文件类型使用相应的文本流或二进制流封装文件流进行读写
*/
FileStream fs1 = new FileStream(filepathTarget, FileMode.OpenorCreate ,FileAccess.ReadWrite);
BinaryWrite bw = new BinaryWrite(fs1);
FileStream fs2 = File.Open(filepathSource, FileMode.OpenorCreate ,FileAccess.ReadWrite);
BinaryReader br = new BinaryReader(fs2);
byte[] fileBytes = br.ReadBytes((int)fs2.Length);	//从filepathSource中读取二进制数据
bw.Write(fileBytes);	//将二进制数据写入filepathTarget

//使用FileInfo/File，若是文本文件可直接使用CreateText或OpenText，返回StreamWriter或StreamReader进行读写
string content="hello world!";
FileInfo fi = new FileInfo(filepath, FileMode.Append ,FileAccess.ReadWrite);	//文件已存在
//StreamReader sw = fi.OpenText();	//OpenText只能读取
StreamWriter sw = fi.AppendText();
sw.Write(content);

//若是二进制文件就饶了一个远路，再返回FileStream，再用Binary流封装进行读写

```

## 











































