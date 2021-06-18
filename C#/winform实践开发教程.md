# 第一章

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



## 第三章	文件读写

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

## 2. File类

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

```C#
string file = @"C:\1.txt";
bool flag = File.Exists(file);
```

## 3. FileInfo类

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
> **StreamWriter** CreateText()——创建新文件，返回写入新文本文件的StreamWriter
>
> **StreamWriter** AppendText()——向当前实例关联的文件中创建一个追加文本的StreamWriter
>
> **StreamReader** OpenText()——创建从当前文件中以UTF-8读取字符的StreamReader

## 4. Directory类

### （1）属性

> 

### （2）方法

> bool Exists(string path)——目录是否存在
>
> **DirectoryInfo** CreateDirectory(string path)——创建目录
>
> void Delete(string path)——删除指定目录
>
> void Move(string sourceDirName, string destDirName)——移动文件夹
>
> string GetCurrentDirectory()——获取当前目录
>
> string[] GetFiles(string path)——获取目录中所有文件名（包含路径）
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
*           DateTime endTime = new DateTime(2016,4,10);
            var startTime=DateTime.Now;
            startTime.Subtract(endTime);	//时间差
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

## 5. FileStream类

提供文件读写，必须要实例化对象后才可操作文件。

用FileStream关联文件，再看具体是文本文件还是二进制文件。选择用StreamWriter或StreamReader读写文本。或BinaryWriter或BinaryReader读写二进制文件。

### （1）打开或创建

* 构造

  ```C#
  string path = "";
  FileStream fs = new FileStream(path, FileMode.OpenorCreate, FileAccess.Write, FileShare fileshare);
  StreamWriter sw = new StreamWriter(fs, Encoding.Default);
  ```

  FileMode——打开文件的方式

  > Append——在现有文件后添加，与FileAccess.Write一起使用
  >
  > Create——文件不存在则创建，若已存在则覆盖
  >
  > OpenOrCreate——若已存在则打开，若不存在则创建
  >
  > CreateNew——创建新文件

  FileAccess——Read、Write、ReadWrite

  FileShare——是否允许其他线程共享访问（具体看书）

* File.Open方法

  ```C#
  string path = "";
  FileStream fs;
  fs = File.Open(path, FileMode.OpenorCreate, FileAccess.Write, FileShare fileshare);
  ```

* File.Create方法

  ```C#
  string path = "";
  FileStream fs = File.Create(path);
  ```

### （2）读写文本文件

```C#
//打开现有文本文件
string path = @".txt";
FileStream openFileStream = new FileStream(path, FileMode.Open, FileAccess.Read);
StreamReader sr = new StreamReader(openFileStream, Encoding.Default);
richTextBox.Text = sr.ReadToEnd();
sr.Close();
openFileStream.Close();
//创建文本文件
string path = @".txt";
FileStream openFileStream = new FileStream(path, FileMode.Create, FileAccess.ReadWrite);
StreamWriter sw = new StreamReader(openFileStream, Encoding.Default);
sw.write(richTextBox.Text);
```

## 6. 读写二进制文件——读写图片等文件

BinaryWriter

BinaryReader

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
byte[] fileBytes = binaryReader.ReadBytes((int)fs.Length)
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
binaryReader.Write(fileBytes);	//将二进制流fileBytes写入文件
binaryReader.flush();
binaryReader.Close();
```

## 8. 小结

```C#
string path = @"";
//用FileStream关联文件，再依文件类型使用相应流封装文件流进行读写

//使用FileInfo，若是文本文件可直接使用CreateText或OpenText，返回StreamWriter或StreamReader进行读写

//若是二进制文件就饶了一个远路，再返回FileStream，再用Binary流封装进行读写

```

## 9. 读写内存流

