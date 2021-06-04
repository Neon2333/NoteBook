#  窗体/控件

http://c.biancheng.net/csharp/winform/

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

  ```C#
  public delegate void MyEventHandler(object sender, System.EventArgs e);	//定义委托类型
  public void MyEventFunc(object sender, System.EventArgs e){}	//定义回调函数
  private event MyEventHandler myevent;	//定义事件对象
  myevent += new MyEventHandler(MyEventFunc);	//将回调函数绑定事件对象
  //事件触发函数
  protected void OnMyEvent(System.EventArgs e){
      if(myevent != null)
  myevent(this, e);	//调用事件	
  }	
  ```

* 手动添加回调

  ```c#
  public delegate void dlg(Object sender, EventArgs e);
  public event dlg testButton.Click;
  public Form1{	
  	InitializaComponent();
  	testButton.Click += new EventHandler(callbackFunc);	//新建EventHandler，将回调函数传给它
  }
  public void callbackFunc(Object sender, EventArgs e){
  	MessageBox.Show("");
  }
  public void OnTest(System.Eventargs e){
      testButton.Click(this, e);	//this表明OnTest是调用事件testButton.Click的sender，传递给事件参数e
  }
  //调用事件的函数是事件发出者，所以用this传到sender
  ```

  大多数情况下都是自动添加回调的，少数情况需要手动添加回调。

* form项目中控件button自动生成的回调

  ```C#
  this.button1.Click += new System.EventHandler(this.button1_Click);//Click绑定回调函数
  private void button1_Click(object sender, EventArgs e)
          {
  
          }
  //this.button1.Click是一个public event EventHandler Click;
  //EventHandler是一个public delegate void EventHandler(object sender, EventArgs e);
  //给button1添加事件后，按下button1会调用this.button1.Click(this,e)
  ```

* 时间显示demo

  ```c#
  //在Button事件页添加Click的回调函数OnButtonClicked
  private void OnButtonClicked(object sender, EventHandler e){
  	string timeStr = DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss");
  	this.timeField.Text=timeStr;	//timeField为label的名称
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

### （2）创建一个输入数据时验证输入数据必须是数字

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

