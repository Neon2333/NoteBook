# 一. 窗体/控件

* isMdiContainer设置父窗口
* WPF基于DirectＸ，WinForm基于GDI，前者图形效果更加丰富
* 控件的属性栏与工具箱栏在视图中。
* MdiParent设置父窗体
* 在窗体属性设置AcceptButton属性可以选择按钮，以Enter等价于按下按钮。
  CancelButton属性等价于按下ESC相当于按下按钮。
* 窗体的可见与否：hide()/show()，控件：visible()
* Form.cs主要写业务逻辑，FormDesigner.cs是由图形设计器自动生成的，主要存放控件的属性和Form的初始化，一般不要改动。
* 在GUI程序中控制台不起作用，Console.WriteLine()不起作用。
* Size是整个窗口的大小，ClientSize是仅窗口客户区的大小。

## 标签Label

* 显示文本 label.Text="";
* 可见与否 label.Visible=true;

## Button

* Name是Button对象名。
* Text设置内容：Text="";
* Location设置位置：Location=new Point(x,y);
* Size设置大小：Size=new Size(height,width);
* 单击事件：单击按钮自动生成单击函数button1_Click(object sender, EventArgs s)
* 窗体设置AcceptButton和CancelButton，分别使用Enter或Esc可以取代点击按钮。

## 事件回调

* Button的Click事件，直接输入函数名/回车即可生成相应函数。（双击按钮会自动生成button_click事件回调函数，但是这样的函数命名不是很好，但是也可以在事件栏中修改函数名）

* 手动添加回调

  ```c#
  public Form1{	
  	InitializaComponent();
  	testButton.Click += new EventHandler(this.OnTest);	//新建EventHandler，将回调函数传给它
  }
  public void OnTest(Object sender, EventArgs e){
  	MessageBox.Show("");
  }
  
  ```

  大多数情况下都是自动添加回调的，少数情况需要手动添加回调。

* 时间显示demo

  ```c#
  //在Button事件页添加Click的回调函数OnButtonClicked
  private void OnButtonClicked(object sender, EventHandler e){
  	string timeStr = DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss");
  	this.timeFiled.Text=timeStr;	//timeField为label的名称
  }
  ```

## 控件布局

* 拖动时，会显示相应的线条对齐。自动生成Location和Size代码。问题：控件大小不会随着窗口大小改变而改变，无法自适应窗口大小。

* 自适应窗口大小：

  ```c#
  //override父类的OnLayout方法，手动设置长度/宽度等
  
  ```

* 默认布局器自带Anchor/Dock

* Anchor布局属性：锚定。将控件设置在窗口的某个固定位置，或设定控件的拖拉拉伸效果，设定与四条边框的距离固定（当窗口大小变化时)．

  锚定上边缘／水平拉伸：Anchor=Top　Left　Right

  拉伸：Anchor=Left　Right Top  Bottom

  锚定于上边缘、水平居中：水平居中，Anchor=Top

  居中：水平居中、垂直居中、Anchor=None

* Dock ：停靠。停靠在一侧或中央。上Top下Bottom左Left右Right中Fill无None。

  

## 布局器LayoutEngine

* Form或Panel都自带布局器，窗口大小改变时有窗口的布局器调整布局。

* 自定义布局器

  ```c#
  //自定义面板
  class SimpleLayoutPanel : Panel{
  	//布局器
      private LayoutEngine layoutEngine = new SimpleLayoutPanel{
          public override LayoutEngine LayoutEngine{
              get{
                  return layoutEngine;
              }
          }
      }	
  }
  //自定义布局器，不要求会写
  class SimpleLayoutEngine : LayoutEngine{
      public override bool Layout(object container,LayoutEventArgs layoutEventArgs){
          //...
      }
  }
  ```

* 自定义控件添加

  1.工具->选项->Windows窗体设计器->常规->自动填充工具箱设为True。

  2.添加自定义的控件。

  3.F7生成解决方案。

  4.重新打开Forms.cs，在工具箱可以看到自定义的控件。

* 流式布局FlowLayoutPanel

  当改变窗体大小时，若一行不够显示时，会将控件放到下一行显示。

* Padding：控件的间距。
* 当控件的层次叠加在一起的时候，选中的时候，用右键选择选中哪个。
* 表格形式布局TableLayoutPanel

## 文本框TextBox

* 属性

  Name变量名

  Text文本内容

  Font字体

  Multiline切换单行模式/多行模式

  UseSystemPasswordChar密码输入

  ReadOnly只读

* 事件

  KeyPress，如：输入完内容按下回车

  ```c#
  //双击产生回调函数
  //sender是事件发出者，e是事件信息
  private void nameBox_KeyPress(object sender, KeyPressEventArgs e){
  	char ch = e.KeyChar;
  	if(ch == '\r'){
  		string str = nameBox.Text;
  		MessageBox.Show("你输入了："+str);
  	}
  }
  ```

## 复选框checkBox

* 属性

  checkstate：选中状态checked，未选中Unchecked。

  Text，文本内容

* 事件

  Click

  CheckStateChanged，属性->杂项

   ```C#
  private void checkBox1_Click(object sender, EventArgs e)
          {
              if (checkBox1.Checked == true)
              {
                  textBox1.UseSystemPasswordChar = true;
              }
              else
              {
                  textBox1.UseSystemPasswordChar = false;
              }
          }
          
   ```

  ```c#
          private void checkBox1_CheckedChanged(object sender, EventArgs e)
          {
              bool isChecked = checkBox1.Checked;
              if(isChecked == true)
              {
                  textBox1.PasswordChar = '*';
              }
          }
  
  ```

## 单选按钮RadioButton

* 属性

  checked

  CheckChanged

## 数值选择NumericUpDown

* 属性

  数值value

  DecimalPlaces数值显示小数位数

## 下拉列表ComboBox

* 属性

  Items，添加数据：1.在设计器里直接编辑Items，点击添加；2.在构造方法中添加：comboBox.Items.Add("red");

  ComboBoxs中的数据可以使任意类型，可以是string或自定义类型。如：

  ```c#
  comboBox1.Items.Add(new Student("haha"));
  ```

* 获取选中的项

  SelectedItem：选中项的值，null表示未选中

  SelectedIndex：选中项的索引，-1表示未选中

  ```C#
  int index = comboBox1.SelectedIndex;
  string sel = (string) comboBox1.SelectedItem;
  Console.WriteLine("选中了："+sel);
  ```

* 事件

  SelectedIndexChanged：

## 列表框ListBox

* 展示选项，让用户单选/多选

  显示时，若是自定义类型，需要override ToString()方法。

  ```c#
  //自定义类型为Student
  class Student{
  	//...
  	public override string ToString(){
  		return string.Format("{{0}} {1}",this.id,this.name);
  	}
  }
  ```

* 属性

  Items，添加:

  ```c#
  listBox1.Items.Add(new Student("haha"));
  ```

  SelectedMode:单选/多选

  SelectedIndexChanged:选中项目改变

  SelectedItems:获取单选选中的项目

  SelectedIndices：获取多选选中的项目

  ```c#
  foreach(object o in listBox1.SelectedItems){
  	Student stu = (Student)o;
  	Console.WriteLine("选中了："+stu.name);
  }
  ```

  SelectedIndex：获取选中项目的索引
  
  删除内容ListBox.Items.Remove()；

## TabControl

* 向选项卡TabControl控件添加选项卡：TabControl.TabPages.Add()。
* 向选项卡TabPage中添加控件：TabPage.Control.Add()；
* 移除：TabPage.Control.Remove()。

##  imageList

* 在imageList中添加图片：imageList.image.Add(image.FromFile("path.bmp"))，
* 删除：imageLIst.image.RemoveAt()。
* 图片以index标识，imageList.image[index]。

## 图片

* 图片类

  Image抽象类，图像统称

  Bitmap具体类，位图/像素图

  ```c#
  Bitmap img = new Bitmap("c:\\example\\123.jpg");
  int w = img.width;
  int h = img.height;
  ```

* 图片框——用图片框显示图片

  BackColor设置背景颜色

  Dock设置停靠

  Image设置图片资源

  SizeMode设置图片的显示方式：Zoom/CenterImage

  ```c#
  //加载bitmap图片
  Bitmap img = new Bitmap("c:\\example\\123.jpg");	//windows路径是反斜杠
  int w = img.width;
  int h = img.height;
  
  //设置pictureBox显示
  pictureBox1.Image=img;
  pictureBox1.SizeMode=PictureBoxSizeMode.Zoom;·
  
  ```

 * 图片来源

   1. 本地：”c:\\\example\\\123.jpg“

   2. 资源文件：双击Resource.resx进入资源编辑器->添加资源。使用：

   Bitmap photo = Properties.Ressources.Img_Name;

   得到一个Bitmap对象photo，将其赋值给图片框pictureBox。

   ```c#
   Bitmap photo = Properties.Ressources.Img_Name;
   pictureBox1.Image=photo;
   
   ```

   程序打包时资源数据打包至EXE程序中，EXE的体积显著增加。此时删除资源的源文件仍然可以正常运行，资源文件就是将源文件打包到资源文件中。

   3. 网络文件：URL 

## 复合控件

* 将标准控件组合起来形成新的控件，有属性/事件可以直接使用。

* 创建添加复合控件

  项目右键添加用户控件

* Windows窗体设计器->常规->自动填充工具箱true

* F7生成解决方案

* 重新打开Form.cs查看工具箱，可以查看到自定义的控件。

  

## listView带图标的listBox

listView同listBox，添加项为listView.Items.Add()，不同的是前者可以添加图标。

* 添加：listView.Items.Add(ListViewItem item);  //添加的元素的类型是ListViewItem
* 清除：listView.Items.Clear();
* 删除：RemoveAt();
* 添加图标：listView.Items[index].imageIndex=0。
* 选中某项：listView.Items[index].Selected()。

## treeView树形标签

* 添加父节点：TreeNode tn = treeView.Nodes.Add("");
* 删除节点：treeView.Nodes.Remove(TreeNode );
* 建立新节点：TreeNode Ntn = new TreeNode("");
* 将新节点添加到父节点：tn.Nodes.Add(Ntn);
* 被选中的节点：treeView.SelectedNode
* 为节点设置图标：将TreeView的ImageList属性设置为已有的ImageList：treeView.ImageList=imageList1;设置节点正常情况下与选中情况下的图标：treeView.ImageIndex=0;treeView.SelectedImageIndex=1;

## Timer控件控制时间间隔

* Timer控件，属性interval设置计时间隔ms，Enabled设置定时开启与关闭，计时完毕后引发Tick事件。

## progressBar显示进度条

* 设置Minimum和Maximum，value值表示当前完成情况，step表示value的增长步值，PerformStep启动value的增长。　　　　　　　

  

## GDI绘图

* Graphics对象，GDI绘图表面

  Graphics g = this.CreateGraphics();

  修改已存在图像：Bitmap mbit = new Bitmap(@"path.bmp");

  Graphics g = Graphics.FromImage(mbit);

* 线条Pen

  Pen pen = new Pen(Color.Blue, 2);	//设置颜色和线条宽度

* 填充SolidBrush

  Brush b = new SolidBrush(Color.Red);

* 直线DrawLine

  public void DrawLine(Pen p,int x1,int y1,int x2,int y2);

  ```c#
  Graphics g = this.CreateGraphics();
  Pen p = new Pen(Color.Black,3);
  g.DrawLine(p,150,30,150,100);
  ```

* 矩形DrawRectangle

  public void DrawRectangle(Pen p,int x,int y, int width, int height);

  (x,y)为矩形左上角点的坐标。

  ```c#
  Graphics g = this.CreateGraphics();
  Pen p = new Pen(Color.Black,3);
  g.DrawRectangle(p,10,10,150,100);	//Pen绘制矩形
  ```

  ```c#
  Rectangle myRectangle = new Rectangle(70,20,100,60);	//创建矩形对象
  ```

* 椭圆

  public void DrawEllipse(Pen p,int x, int y, int width, int height);

  椭圆由矩形边框决定，(x,y)是边框左上角点，width和height分别是边框宽度。

* 圆弧

  public void DrawArc(Pen pen,Rectangle rect, float startAngle, float sweepAngle);

  rect矩形定义圆弧的边界。

  startAngle和sweepAngle：

* 扇形

  public void DrawPie(Pen p, float x, float y, float width, float height,float startAngle , float sweepAngle);

* 多边形

  public void DrawPloygon(Pen p, Point[] points);

  Point[]数组确定多边形的顶点。

## 例题

 ### （1）创建一个渐变显示的窗体

```c#
 //在Form1构造函数中设定为透明
 this.Opacity = 0.0D;    //设置透明
//计时器计时，变量opacity自增赋值给this.Opacity。不能大于1.
namespace demo5
{
    public partial class Form1 : Form
    {
        //透明度 
        double opacity = 0.0D;
        public Form1()
        {
            InitializeComponent();
        }

        private void timer1_Tick(object sender, EventArgs e)
        {
            
            if (opacity <= 1.0)
            {
                opacity += 0.05D;
                this.Opacity = opacity;
            }
        }
    }
}
//标题栏文字右对齐
//设置RightToLeft属性为YES
```

### （2）创建一个输入数据时验证输入数据必须时数字

```c#
private void button1_clicked(object sender, EventArgs e)
        {
            bool flag = true;
            string str = textBox1.Text;
            int len = str.Length;
            for(int i=0;i< len; i++)
            {
                char ch = str[i];
                if (ch > '9' || ch < '0')
                {
                    flag = false;
                    break;
                }
            }
            if (flag == false)
            {
                textBox1.Text = "";
                MessageBox.Show("please enter numbers..");
            }
            else
            {
                MessageBox.Show(textBox1.Text);
            }
        }
```

### （3）程序demo,将文本框中输入的“书名目录”添加到图书目录列表中，若有重复，弹出提示信息。

```c#
  bool isContainInListView(ListViewItem item, ListView lv,string str)
        {
            bool flag = false;
            foreach (ListViewItem items in lv.Items)
            {
                if (str == items.Text)
                {
                    flag = true;
                    break;
                }
            }
            return flag;
        }

        private void button1_Clicked_AddBooks(object sender, EventArgs e)
        {
            string bookName;
            bookName = textBox1.Text;
            ListViewItem item1 = new ListViewItem(bookName);  //ListViewItem构造函数
            if (isContainInListView(item1, listView1, bookName)==false)
            {
                listView1.Items.Add(bookName);
                textBox1.Text = "";
            }
            else
            {
                MessageBox.Show("重复输入");
            }

        }
```

Q：这里自己写了一个遍历判断是否存在重复item。不知道什么原因ListView.Items.Contains(ListViewItem item)不能正确判断是否存在已有元素。

---

# 二. 连接MySQL

## 1.dataGridView控件

https://www.cnblogs.com/monkeyZhong/p/4530795.html

https://www.cnblogs.com/my---world/p/12044302.html

http://c.biancheng.net/view/3040.html

http://www.360doc.com/content/14/0331/16/16325679_365222145.shtml

### 1.1 属性

* DataGridView.ReadOnly=true;	//不允许在表上编辑

* DataGridView.SelectionMode = DataGridViewSelectionMode.FullRowSelect;	//选中模式，整行选中  

* DataGridViewSelectionMode枚举类型

* DataGridView.AllowUserToAddRows = false 	//不允许用户自行添加一行

  设置为true时，表最后会空出一行，用户可以自行添加数据

* DataGridView.BackgroundColor=Color.White;    //设置表格背景颜色

* **DataGridView.Columns[9].Visible=false;     //设置第10列隐藏**

* DataGridView.Rows[9].Visible=false;     //设置第10行隐藏

* **dataGridView.Column.Count=num;	//事先设置列数，否则设置HeadText会越界**

* dataGridView.Column.Width;     //列宽度

### 1.2 删除行/列

（1）删除列

```C#
//删除列标题为Column1的列
DataGridView1.Columns.Remove("Column1");
//删除第一列 
DataGridView1.Columns.RemoveAt(0);

```

（2）删除行

在清除DataGridview的数据时遇到个问题。我想要清空DataGridview的数据，用了DataGridview.Rows.Clear()，这时就出错了，提示“不能清除此列表”。于是搜索了下，**用数据源绑定的DataGridView不能用Rows.Clear()清除**，手动添加的是能够用clear()的。所以将datasource设置为null就可以清空数据，但是这不是我要的效果，这样会将DataGridView的列也删掉。想要保持原有的列用如下代码就可以了，就是重新绑定一个没有数据的datatable。

```C#
//删除所有行，方法1
DataTable dt = (DataTable)dataGridView1.DataSource;
dt.Rows.Clear();         
dataGridView1.DataSource = dt;
//方法2，删除所有行
while (this.dgv1.RowCount != 0)
{
   this.dgv1.Rows.RemoveAt(0); //每次删除一行数据
}

//删除指定行
//删除第一行 
DataGridView1.Rows.RemoveAt(0); 

//删除选中行
foreach (DataGridViewRow r in DataGridView1.SelectedRows)
{
    if (!r.IsNewRow)
    {
        DataGridView1.Rows.Remove(r);
    }
}
```

（3）重新显示数据前清空dgv

```c#
 //清空。不用for判断因为RowCount在变
    while (this.dgv1.RowCount != 0)
     {
        this.dgv1.Rows.RemoveAt(0); //每次删除一行数据
     }
```

```C#
//如果使用
for (int i = 0; i < dgv.Rows.Count; i++)
{
if (dgv.Rows[i].Cells[8].Value.ToString() == “离线”)
{
dgv.Rows.Remove(dgv.Rows[i]);
}
else
{
	continue;
}
}
//删除当前行后 整个datagridview的所有行的索引会发生变化 i++后会跳过下一条数据 会造成删除本不应该删除的数据，应该在 dgv.Rows.Remove(dgv.Rows[i]); 后面加上i--使循环检索回到跳过检索的数据当中进行检索，满足条件就删除完整代码如下：
for (int i = 0; i < dgv.Rows.Count; i++)
{
if (dgv.Rows[i].Cells[8].Value.ToString() == “离线”)
{
dgv.Rows.Remove(dgv.Rows[i]);
i--;
}
else
{
continue;
}
}
```

### 1.3 修改列标题

 通过设置 DataGridView 控件的 DataSource 属性即可绑定 DataGridView 控件，但绑定后的 DataGridView 控件中的标题是数据表中的列名。如果需要更改 DataGridView 控件的列标题 ，用HeadText修改。

```mysql
DataGridView.Columns[0].HeadText="每一列名";
DataGridBiew.Columns[0].Width = 70;
```

### 1.4 绑定数据

（1）数据源绑定的方式

**绑定数据源后，列标题已经有了！！只不过是表中的列名**

```c#
//全部显示,绑定表
DataTable dt = new DataTable();
DataGridView.DataSource = dt；	//数据源指向表，就可以显示表中数据了
//绑定库
DataSet ds = new DataSet(); 
   da.Fill(ds); 
   this.dataGridView1.DataSource = ds.Tables[0]; 
}
```

**不能向没有列的 DataGridView 控件添加行。必须首先添加列。**

DataGridView 有个属性可以设置自动产生 列 AutoGenerateColumns=true ,而且默认值是为true 的， 你这种情况， 估计是没有绑定数据源就先向控件添加行了，这就相当于没有生成列就添加行了，这样操作是不允许的。所有产生异常，有两种解决办法，一种就是你先绑定数据源、然后再添加行。另外一种就是先手动给控件添加列，那就怎么都不会出错了。不过如果你的数据源是表格或者、对象集合的话，直接在表里面添加空行、添加对象，这样更容易些。

先对DataTable/DataSet进行操作，然后在绑定数据源。 

```c#

DataTable dt = new DataTable();//建立个数据表

dt.Columns.Add(new DataColumn("id", typeof(int)));//在表中添加int类型的列

dt.Columns.Add(new DataColumn("Name", typeof(string)));//在表中添加string类型的Name列

DataRow dr;//行

for (int i = 0; i < 3; i++)
{
      dr = dt.NewRow();	//新建一个行
      dr["id"] = i;	//给行的列赋值
      dr["Name"] = "Name" + i;
      dt.Rows.Add(dr);//在表里添加此行
}
dataGridView1.DataSource =dt;	//将表绑定为dgv1的数据源
```

（2）手动添加方式

**在手动添加行之前必须要先添加列**

```c#
int index = this.dataGridView.Rows.Add();	//增加一行，并返回行号。增加行前要先添加列
this.dataGridView.Rows[index].Cells[0].Value="1";	//列的序号访问
this.dataGridView.Rows[index].Cells["name"].Value="lihua";	//用字段访问
this.dataGridView.Rows[index].Cells[2].Value="28";
```

### 1.5 对调某两列

```mysql
//手动设置表头名，并将datatable另一列绑定该列

//交换第一列的id和第二列的name
DataGridView.Columns[0].HeadText="name";
DataGridView.Columns[1].HeadText="id";
DataGridView.Columns[0].DataPropertyName=DataTable.Column[1].ToString;	//将表中1列数据绑定到显示的第0列
DataGridView.Columns["id"].DataPropertyName=DataTable.Column[0].ToString;	//将表第0列数据绑定到显示的第1列。DataTable.Column[0].ToString是这一列的表头不是列中的数据。
```

DataPropertyName绑定数据源用的，DataGridView.Columns["id"].DataPropertyName="xx",将表中"xx"这一列与表中"id"这列绑定。

### 1.6 设置列宽度

```C#
this.dataGridView1.Columns[i].Width = 150;
```

**设置自适应列宽**

https://blog.csdn.net/kasama1953/article/details/51656495

```C#
    /// <summary>
    /// 使DataGridView的列自适应宽度
    /// </summary>
    /// <param name="dgViewFiles"></param>
    private void AutoSizeColumn(DataGridView dgViewFiles)
    {
        int width = 0;
        //使列自使用宽度
        //对于DataGridView的每一个列都调整
        for (int i = 0; i < dgViewFiles.Columns.Count; i++)
        {
            //将每一列都调整为自动适应模式
            dgViewFiles.AutoResizeColumn(i, DataGridViewAutoSizeColumnMode.AllCells);
            //记录整个DataGridView的宽度
            width += dgViewFiles.Columns[i].Width;
        }
        //判断调整后的宽度与原来设定的宽度的关系，如果是调整后的宽度大于原来设定的宽度，
        //则将DataGridView的列自动调整模式设置为显示的列即可，
        //如果是小于原来设定的宽度，将模式改为填充。
        if (width > dgViewFiles.Size.Width)
        {
            dgViewFiles.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.DisplayedCells;
        }
        else
        {
            dgViewFiles.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill;
        }
        //冻结某列 从左开始 0，1，2
        dgViewFiles.Columns[1].Frozen = true;
    }
```



## 2. MySQL相关

### （1）用到的头文件：都给它包含进去再说

```mysql

```

https://www.cnblogs.com/gaokaitai/p/10633858.html

https://www.cnblogs.com/zwj1276952588/p/10499789.html

https://blog.csdn.net/liyazhen2011/article/details/82895946

https://zhuanlan.zhihu.com/p/68905542

https://blog.csdn.net/yelin042/article/details/90479330

https://www.cnblogs.com/andy9468/p/12841413.html

### （2）一些函数

* MySqlConnection.Open()

* MySqlConnection.State

* MySqlCommand.CommandTimeout

* MySqlCommand.ExecuteNonQuery()

  执行一个INSERT/DELETE/UPDATE命令后，cmd.ExecuteNonQuery()相当于刷新操作，执行后增/删/改指令起作用。该函数返回受到指令操作影响的行数。SELECT方法不适合该函数。用于确定操作是否成功，若受影响的行数=0则不成功，若>0则成功。

* ExecuteScalar()
* ExecuteReader()
* 类MySqlDataAdapter
* 类MySqlCommandBuilder

### （3）MySQLParameter

参数化查询（Parameterized Query ）是指在设计与数据库链接并访问数据时，在需要填入数值或数据的地方，使用参数 (Parameter) 来给值，这个方法目前已被视为最有效可预防SQL注入攻击 (SQL Injection) 的攻击手法的防御方式。下面将重点总结下Parameter构建的几种常用方法。

   说起参数化查询当然最主要的就是如何构造所谓的参数：比如，我们登陆时需要密码和用户名，一般我们会这样写sql语句，Select * from Login where username= @Username and password = @Password，为了防止sql注入，我们该如何构建@Username和@Password两个参数呢，下面提供六种（其实大部分原理都是一样，只不过代码表现形式不一样，以此仅作对比，方便使用）构建参数的方法，根据不同的情况选用合适的方法即可：

```mysql
static void Main(string[] args)
{       
    string connectionString = "Server='localhost';Database='test';UId='root';Password='123456'";
    MySqlConnection mycon = new MySqlConnection(connectionString);
    mycon.Open();
 
    string str = "insert into person(id,name) values(@id,@name)";
    MySqlCommand cmd = new MySqlCommand(str, mycon);
    cmd.CommandType = CommandType.Text;
            
    MySqlParameter p1 = new MySqlParameter("@id", MySqlDbType.Int32);
    p1.Value = 1;
    MySqlParameter p2 = new MySqlParameter("@name", MySqlDbType.VarChar);
    p2.Value = "Dave";
          
    cmd.Parameters.Add(p1);
    cmd.Parameters.Add(p2);
 
    if (cmd.ExecuteNonQuery() > 0)
    {
        Console.WriteLine("数据插入成功！");
    }
    Console.ReadLine();
    mycon.Close();
}
```

## 3.流程

### （1）创建连接对象

```mysql
//命名空间 System.Data.MySqlClient.MySqlConnection
using MySql.Data;
using MySql.Data.MySqlClient;

string connectString="dataBase=,server=,userId=,password=";	//存储指定连接时的数据库、服务器、用户名等
MySqlConnection sqlcon = new MySqlConnection(connectString);	//创建连接对象
sqlcon.open();	//连接打开
```

### （2）创建命令对象/增删改

 CommandType是SqlCommand对象的一个属性，用于指定执行动作的形式，它告诉.net接下来执行的是一个文本(text)、存储过程(StoredProcedure)还是表名称(TableDirect)。而CommandType是一个枚举类型。有三个值：text、StoredProcedure、TableDirect用于表示SqlCommand对象CommandType的执行形式。 cmd.CommandType = CommandType.Text; 是告诉cmd执行的sql是你赋给CommandText的值里写出的sql语句，如果是存储过程的话，cmd.CommandType = CommandType.StoredProcedure;然后CommandText='存储过程的名字'，这就是告诉cmd执行的是存储过程，存储过程的名字就是CommandText的值。 

**当指令不确定时用变量替代变换内容，再给变量赋值**

```mysql
string cmdString="insert into person(id,name) values(@id,@name)";//在指令中定义变量id和name，以@表示
//写法1
MySqlCommand cmd = new MySqlCommand(cmdString,sqlcon);//绑定连接对象和指令
cmd.CommandType = CommandType.Text;
MySqlParameter p1 = new MySqlParameter("@id", MySqlDbType.Int32);//将变量id封装为p1
p1.Value = 1;//给@id赋值
MySqlParameter p2 = new MySqlParameter("@name", MySqlDbType.VarChar);
p2.Value = "Dave";          
cmd.Parameters.Add(p1);
cmd.Parameters.Add(p2);
//写法2
MySqlCommand cmd = new MySqlCommand();
cmd.Connection=sqlcon;
cmd.CommandType = CommandType.Text;	//告诉cmd按照用户给CommandText赋值的指令来操作
cmd.CommandText=cmdString;	//给CommandText赋值指令
cmd.Parameters.Add("@id",MySqlDbType.Int32);
cmd.Parameters.Add("@name",MySqlDbType.Varchar);
cmd.Parameters["@id"].Value=1;
cmd.Parameters["@name"].Value="Dave";
    
```

### （3）查询读取数据

* 用MySqlDataReader对象

单向读取：只能依次读取下条数据。只读：数据无法修改。DataSet可修改。

提供Read()方法读取一行，返回值为真时表明读到。

```mysql
MySqlDataReader reader=cmd.ExecuteReader();	//返回一个MySQLDataReader对象
int index=0;
while(reader.Read()){
	this.dataGridView.Rows[index].Cells[0].Value=reader.GetString("id");
	this.dataGridView.Rows[index].Cells[1].Value=reader.GetString("name");
	this.dataGridView.Rows[index].Cells[2].Value=reader.GetString("age");
}
```

* **用DataSet/DataTable读取数据（常用）**

DataSet与DataReader以“流”随时和服务器保持联系不同，类似于一个本地的小数据库，速度略慢但可忽略不计，连接池负担小。

https://blog.csdn.net/yelin042/article/details/90479330

**DataTable赋值、取值**

DataTable.Rows.Count

DataTable.Columns.Count

```mysql
//已有表dt

//赋值
//可通过索引访问，也可通过字段名访问
dt.Rows[0][1]="李四"	//第一个索引表示第1行，第二个索引表示第2个字段
dt.Rows[0]["name"]="李四"	//通过字段名"name"访问

//取值
string name = dt.Rows[0][1].ToString();
```











