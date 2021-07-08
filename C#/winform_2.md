# winform笔记补充

http://c.biancheng.net/csharp/winform/

微软的.NET开发框架图形用户界面的一部分，该组件通过将**现有的Windows API（Win32 API）封装**为托管代码提供了对Windows本地（native）组件的访问方式

winform窗体应用程序是事件驱动的。鼠标或键盘发出事件，通过窗体和控件的属性和响应相关事件完成业务逻辑。

<<<<<<< HEAD
属性设置一般写在Designer.cs中

## 用户控件UserControl和自定义控件CustomControl

https://blog.csdn.net/bluecard2008/article/details/103922589

* UserControl(用户控件)，也就是**复合控件**。继承自UserControl  主要用于开发Container控件。  
   (Container控件：可以添加其他Controls控件, .net  自带很多Container  控件, 其实Form就是一个Container)   
   
* CustomControl(自定义控件)继承自Control主要用于开发windows控件的最基本的类  象Text,Button都是。

* 创建用户控件在当前项目使用

   https://www.cnblogs.com/dawasai/p/4443899.html

   生成以后，就会在工具箱中看到创建的用户控件，可添加到窗体上使用。

* 用户控件dll使用步骤：

  新建`Windows窗体控件库`，然后在Release文件夹找到.dll文件。

  用户控件的运行同项目不同，是在用户控件测试容器中运行的。

  工具箱处右键，`添加选项卡`可新建控件分组。`添加项`将.dll添加到.NET Framework组件中，就可以使用自定义控件了。

  

=======
**属性设置一般写在Designer.cs中。一般不修改designer.cs，可打开看，看属性的赋值。然后在构造里写自己需要的属性设置。**

## *注意点收集*

### （1）按钮自动弹起

用MouseDown和MouseUp

 ```C#
 public partial class Form1 : Form
    {
        //private bool m_btnState1 = false;//按钮按下状态
        //private bool m_btnState2 = false;//按钮按下状态
        private const string MSGOK = "确定按钮被点击";
        private const string MSGCANCEL = "取消按钮被点击";

        public Form1()
        {
            InitializeComponent();
            //this.radioButton1.Checked = false;
            //this.radioButton2.Checked = false;
            //this.m_btnState1 = this.radioButton1.Checked;
            //this.m_btnState2 = this.radioButton1.Checked;

        }
        public Form1(string str)
        {
            InitializeComponent();

            
            this.textBox_content.Text = str;
            this.textBox_content.Font = new System.Drawing.Font("微软雅黑", 12F, System.Drawing.FontStyle.Bold);
            this.textBox_content.ForeColor = System.Drawing.Color.Yellow;
            this.textBox_content.TextAlign = System.Windows.Forms.HorizontalAlignment.Center;

        }
    
        ////毫秒延时函数
        //public static void Delay(int milliSecond)
        //{
        //    int start = Environment.TickCount;
        //    while (Math.Abs(Environment.TickCount - start) < milliSecond)
        //    {
        //        Application.DoEvents();
        //    }
        //}

        //事件
        public delegate void myDlg(string str);
        public myDlg eventClickOk;
        public myDlg eventClickCancel;


        //    //按钮按下不会弹起，再按下弹起
        //private void radioButton1_CheckedChanged(object sender, EventArgs e)
        //{
        //    eventClickOk(sender, e);
        //    if (this.m_btnState1 == false)
        //    {
        //        this.radioButton1.Checked = true;
        //        this.m_btnState1 = true;
        //        radioButton1.Image = global::demo1Form.Properties.Resources.OK;
        //    }
        //    else
        //    {
        //        this.radioButton1.Checked = true;
        //        this.m_btnState1 = true;

        //    }
        //}

        ////按钮按下自动弹起
        //private void radioButton1_Click(object sender, EventArgs e)
        //{
        //    this.radioButton1.BackgroundImage = global::demo1Form.Properties.Resources.down;
        //    //Delay(500);
        //    eventClickOk(Form1.MSGOK);
        //    this.radioButton1.BackgroundImage = global::demo1Form.Properties.Resources.up;
        //    //Delay(500);
        //    this.Dispose();
        //}

        //private void radioButton2_Click(object sender, EventArgs e)
        //{
        //    this.radioButton2.BackgroundImage = global::demo1Form.Properties.Resources.down;
        //    //Delay(500);
        //    eventClickOk(Form1.MSGCANCEL);
        //    this.radioButton2.BackgroundImage = global::demo1Form.Properties.Resources.up;
        //    //Delay(500);
        //    this.Dispose();

        //    //this.Invalidate();  //重绘
        //}
        
        //窗体重绘时调用
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            Graphics gp = this.CreateGraphics();
            //gp.DrawLine(new Pen(Color.Red, 5), new Point(this.Location.X, this.Location.Y), new Point(this.Location.X + 500, this.Location.Y));
            gp.DrawLine(new Pen(Color.Red, 4), new Point(0, 0), new Point(0, 350));
            gp.DrawLine(new Pen(Color.Red, 4), new Point(0, 0), new Point(500, 0));
        }

        /**
        Load事件是在窗体首次绘制之前调用的，所以调用Load时窗体还未绘制，所以无法显示。绘制图形还是用paint事件。
        */
        ////窗体加载时调用
        //private void Form1_Load(object sender, EventArgs e)
        //{
        //    //Graphics gp = this.CreateGraphics();
        //    ////gp.DrawLine(new Pen(Color.Red, 5), new Point(this.Location.X, this.Location.Y), new Point(this.Location.X + 500, this.Location.Y));
        //    //gp.DrawLine(new Pen(Color.Red, 4), new Point(0, 0), new Point(0, 350));
        //    //gp.DrawLine(new Pen(Color.Red, 4), new Point(0, 0), new Point(500, 0));
        //}

        //按钮的按下、弹起，用MouseDown和MouseUp
        //不要用Click事件+延时模拟
        private void radioButton1_MouseDown(object sender, MouseEventArgs e)
        {
            this.radioButton1.BackgroundImage = global::demo1Form.Properties.Resources.down;
            eventClickOk(Form1.MSGOK);
        }

        private void radioButton1_MouseUp(object sender, MouseEventArgs e)
        {
            this.radioButton1.BackgroundImage = global::demo1Form.Properties.Resources.up;
            this.Dispose();
        }

        private void radioButton2_MouseDown(object sender, MouseEventArgs e)
        {
            this.radioButton2.BackgroundImage = global::demo1Form.Properties.Resources.down;
            eventClickOk(Form1.MSGCANCEL);
        }

        private void radioButton2_MouseUp(object sender, MouseEventArgs e)
        {
            this.radioButton2.BackgroundImage = global::demo1Form.Properties.Resources.up;
            this.Dispose();
        }
 ```

### （2）导出form为dll

https://blog.csdn.net/TimeFault/article/details/50206145

### （3）按钮白边

用label作为按钮？

### （4）窗体加载时绘图——放在构造中？放Load事件中？放Paint事件中？为什么GDI绘图放在Load事件中不显示？

* 希望窗体加载时绘制图形，放在构造和Load中时，加载时都是空白。

  这和windows的机理有关。在windows系统中所有的窗口，子窗口，控件等都是属于一种叫做"窗口"的对象。这个对象会接收很多消息，比如鼠标消息，绘画消息，定时消息等，对不同的消息的不同处理就形成了你所用的按钮，文本框等等各种控件。 但是"窗口"有个最基本的消息就是Paint消息，当有其他窗口层叠在它之上然后移开，或者它的大小改变，或它的显示模式改变都会自动触发这个消息，调用内部函数重新绘制"窗口"。 如果你没在paint消息里绘制，只是直接用draw绘制的东西，在用另一个窗口遮挡然后移开后，会变回原来的图案(一般就是空白)。

*  Form_Load是窗体初始化时发生， Form_ Paint是当移动窗体、改变窗体大小，最大化，最小化，窗体被其他窗体遮住后再次显示，等等时都会发生。

* 今天无意将一段绘图代码 写在form_load事件了，结果不能显示绘图。（代码：Graphics g = this.CreateGraphics();Pen pen = new Pen(Color.Red, 10);Rectangle r = new Rectangle(70, 20, 100, 60);*g.DrawEllipse(pen, r);。*

  **经过查询得知：Form_Load事件是在窗体首次绘制前发生的。也就是说，在Form_Load过程中，这个Form里所有需要在屏幕上呈现的东西都还没开始绘制，所以你在Load中去绘制东西是看不到的。在Windows系统中，窗体的Load事件执行完毕后，系统才开始绘制窗体并显示在屏幕上。所以只有当Form_Load方法执行完后，我们才能看见窗体。当Form_Paint方法执行完后才显示出我们想要绘制的完整的图形。并且窗体Repaint后，窗体的Graphics就被清空了。每次刷新的时候都需要绘制，在窗体Load也会刷新。**

* demo——用Paint实现渐变色

  https://blog.csdn.net/weixin_33998125/article/details/93802524

### （5）Invalidate()

* Invalidate()是System.Windows.Forms命名空间下Control类的众多重载方法之一，调用还方法使控件的整个画面无效并导致重绘控件，是一个void类型的函数，这里的this表示该类是Control类或继承了Control类的实例 
* Invalidate()函数的作用是使整个窗口客户区无效，窗口客户无效即需要重绘，这时Window系统会发送一WM_PAINT消息放在应用程序的消息队列中，WM_PAINT消息的优先级很低，所以不会立即重绘。
  如果需要立即重绘，那么就使用UpdateWindow( )函数，该函数可使WM_PAINT被直接发送到目标窗口，从而导致窗口立即重绘。 
* Invalidate()是通知区域无效,至于什么时候重画还要等消息排队
  Update()是通知立刻重画,不用等消息排队! 
*  Invalidate()是System.Windows.Forms.Form的一个成员，**它把客户窗口区域标记为无效，因此在需要重新绘制时，它可以确保引发Paint事件**。Invalidate()有两个重载方法：可以给它传送一个矩形，指定(使用页面坐标)需要重新绘制哪个窗口区域，如果**不提供任何参数，它就把整个客户区域标记为无效**。 
### （6）路径中反斜杠不要写2个，用@

https://blog.csdn.net/u012719076/article/details/108235452

路径中使用/ 和 \都可以，不过Windows下两种都可以，而unix中只能用/

而且由于在ASCII中字符 \ 是转义字符，所以要表示 \ 则要多加一个，所以是 \; 而 / 则不需多添加。

对于路径加@
正斜杠/ 加@ 没有作用
反斜杠\ 加@ 会使 一个\ 变成 两个\

```C#
string strPath = @"D:\\OSA\\BK\\20200825-095420-bk.csv";
```



### （7）/// \<summary> XML注释

https://www.cnblogs.com/mq0036/p/3544264.html

```xml
 /// <summary>
/// 增加方法
/// </summary> 
```

 是XML注释
xml注释都在三个向前的斜线之后(///)，两条斜线表示是一个注释，编译器将忽略后面的内容，三条斜线告诉编译器，后面是XML注释，需要适当地处理。

>>>>>>> ac62793 (2021/6/9,winform笔记增加了注意点)

### （8）char转数字

```C#
int i = 9;
//char c = (char)i;
//Console.WriteLine(c);
char c = (char)(i+'0');//9+48 => '0':48
Console.WriteLine(c);
```



## 1. 窗体属性

> Name：窗体名
>
> AcceptButton：选择按钮，在窗体下按下enter相当于单击该按钮
>
> CancelButton：同上，只是对应按下Esc
>
> AllowDrop：控件是否接受用户拖到控件上的数据
>
> AutoScroll：设置窗体是否自动显示滑动条，当控件超出窗体的“工作区”时
>
> AutoScrollMargin：拖动滑动条到头时，控件距离窗体的边界的距离。设置为0时，滑动条拖到头控件与窗体边缘重合
>
> AutoScrollMinSize：设置窗体的工作区大小。若大于窗体的Size，就会直接出现滑动条，而无需控件超出范围
>
> AutoSize：设置窗口会自动调整大小，控件不会超出范围也不需要滑动条了
>
> AutoSizeMode：GrowOnly——只把不够的地方grow，多出来的不shrink。GrowAndShrink——不够的地方grow，多出来的shrink，展现出来可容纳控件的最小的窗体
>
> Cursor：光标进入控件范围内，显示的光标
>
> Enabled：控件使能
>
> Font：窗体内所有文字的字体/大小等
>
> ForeColor：窗体内所有文字的颜色
>
> Icon：图标
>
> Location：左上角点相对其容器左上角点的坐标
>
> MaximizeBox：右上角是否显示可最大化按钮
>
> MaximizeSize：最大化时窗口的尺寸
>
> Opacity：透明度
>
> **StartPosition**：窗口启动时的位置（this.StartPosition = FormStartPosition.Manual）
>
> **FormBorderStyle**：窗体的边框和标题栏的的外观
>
> Test：窗体标题
>
> TopMost：是否在最上层
>
> WindowState：窗体启动时是最大化/最小化/普通

* 窗体初始位置

  https://www.cnblogs.com/xinjian/archive/2011/09/21/2184306.html

  https://www.cnblogs.com/roboot/p/7088070.html

### （1）*添加资源文件*

* 项目资源

  添加到全局的properties的Resourse.resx中

  添加资源文件（背景图片jpg/图标png）到Resource.resx

  项目名右键选择`属性`，再选择`资源`，点击`添加资源`右侧箭头，选择已有文件。

  直接点的话，生成bmp画图，自己画。

  **保存后，资源文件放到到项目文件夹下的Resource文件夹下**

  （在Image属性那里，通过添加项目资源文件，是一样的）

  ```C#
  this.pictureBox1.Image = global::WindowsFormsApplication1.Properties.Resources.img01;
  ```

* 窗体资源/本地资源

  在Image属性那里，通过本地资源添加，图片将保存在Form.resx中

  ```C#
  //使用方法
  this.pictureBox1.Image = ((System.Drawing.Image)(resources.GetObject("pictureBox1.Image")));
  
  ///由ComponentResourceManager进行管理
  ComponentResourceManager resources = new ComponentResourceManager(typeof(Form1));this.pictureBox1.Image = ((Image)(resources.GetObject("Image")));
  ```

  导入“本地资源”的图片，**会在form文件下面的form.resx文件里面，不可以在多个form界面引用**，不可以在里面修改图片的名称；而导入“项目资源文件”的图片，会保存在Properties文件夹下面的Resources.resx文件夹里面，可以在多个form界面引用，可以在里面修改图片的名称。
  注意：Resources.rexs文件里面的资源可以改名字，有时会自动映射到已运用的资源，有时需要手动修改。

* 本地资源，对应的是专属于某窗体使用的资源。

  项目资源文件，则是大家都可以使用的资源。

  **不论是本地资源，还是项目资源，添加后，都会传入到Resources文件夹下，所以，注意命名不要相同。**

* 向项目资源和本地资源添加图片的区别

  待完善

### （2）启动程序

窗体程序启动时，只能显示一个窗体。在Program.cs中设置。

```C#
Application.Run(new Form1());
```



## 2. 窗体事件

> | Load             | 窗体加载事件，在运行窗体时即可执行该事件 |
> | ---------------- | ---------------------------------------- |
> | MouseClick       | 鼠标单击事件                             |
> | MouseDoubleClick | 鼠标双击事件                             |
> | MouseMove        | 鼠标移动事件                             |
> | KeyDown          | 键盘按下事件                             |
> | KeyUp            | 键盘释放事件                             |
> | FormClosing      | 窗体关闭事件，关闭窗体时发生             |
> | FormClosed       | 窗体关闭事件，关闭窗体后发生             |



## 3. 窗体方法

> | void Show()               | 显示窗体                       |
> | ------------------------- | ------------------------------ |
> | **void Hide()**           | 隐藏窗体                       |
> | DialogResult ShowDialog() | 以对话框模式显示窗体，模态窗体 |
> | void CenterToParent()     | 使窗体在父窗体边界内居中       |
> | void CenterToScreen()     | 使窗体在当前屏幕上居中         |
> | void Activate()           | 激活窗体并给予它焦点           |
> | void Close()              | 关闭窗体                       |

### （1）ShowDialog()和Show()的区别？

Show显示的窗口可自由切换到其他窗口，Show后的代码立刻执行。

ShowDialog()显示的窗口是模态窗口，不能切换到其他窗口，ShowDialog()后的代码必须要等关闭了当前的窗体才会执行，被阻塞在ShowDialog

### （2）怎样将Hide的窗体重新显示？

https://www.cnblogs.com/hexiaobao/p/7692790.html

**Hide和Visible=false的区别是什么？**

> 1、hide和.visible=false同是隐藏，hide之后是show,窗体当即可见，而visible的窗体即使＝true了，可能还被什么窗体挡住。
>
> 2、从语言方面来说有区别，HIDE是方法，VISIBLE是属性。
> 不过从达到效果来说没有什么区别，都一样~

```C#
//主窗口:
public partial class frmMain : Form
{
	public static frmMain f0 = null; //用来引用主窗口
	public frmMain()
	{
		InitializeComponent();
		f0 = this; //赋值
	}
	private void button1_Click(object sender, EventArgs e)
	{
		new frmCx().Show();
		this.Hide();
	}
//子窗口:
public partial class frmCx : Form
{
	public frmCx()
	{
		InitializeComponent();
	}
	private void frmCx_FormClosing(object sender, FormClosingEventArgs e)
	{
		frmMain.f0.Show(); //显示主窗口
	}
}
```

### （3）怎样关闭已hide的窗体？



## 4. *窗体的资源释放*

https://www.cnblogs.com/yieryi/p/4616610.html

https://blog.csdn.net/po_xiao_/article/details/49758823

* 子窗体所属的父窗体关闭的话，子窗体的资源也会被释放

* 慎用Hide()，注意隐藏的如果是主窗体，那么子窗体关闭后，主窗体资源是否释放了。

  Show()显示的普通窗体，ShowDialog()显示模态窗体。

  ShowDialog()后的代码要等模态窗体关闭后才会执行。而前者在Show后的代码会立刻执行。

* 窗体的及时关闭和销毁对软件的效率、对计算机的资源占用都非常重要。

  在以下两种情况下调用 Close 不会释放窗体：(1) 窗体是多文档界面 (MDI) 应用程序的一部分且是不可见的；(2) 您是使用 ShowDialog 显示的该窗体。在这些情况下，需要手动调用 Dispose 来将窗体的所有控件都标记为需要进行垃圾回收。所以当窗体显示为模态窗体时，单击“关闭”按钮会隐藏窗体，并将DialogResult属性设置为DialogResult.Cancel 

  如果不需要此模态窗口，请立即对其进行销毁，销毁代码可以用以下两种方式写

  （1）在Form3的关闭事件里

  ```C#
   private void Form3_FormClosed(object sender, FormClosedEventArgs e)
          {
              this.Dispose();
          }
  ```

  （2）在Form3的关闭事件中将DialogResult设置为OK,在调用者里面判断后释放

  ```C#
  //这是Form3的事件 
  private void Form3_FormClosed(object sender, FormClosedEventArgs e)
          {
              this.DialogResult = DialogResult.OK;
          }
  ```

  ```C#
   //这是Form2中调用private void button2_Click(object sender, EventArgs e)
          {
              Form3 fr3 = new Form3();
              if(fr3.ShowDialog()==DialogResult.OK)
              {
                  fr3.Dispose();
              }   
          }
  ```

* 窗体关闭

  this.Close()/this.Dispose()——只关闭当前窗口，若关闭的不是主窗体或存在后台线程，都是无法干净的退出程序的。

  System.Environment.Exit(0)——这是最彻底的退出方式，不管什么线程都被强制退出，把程序结束的很干净。

* Dispose和Close的区别？

  Close()会自动调用Dispose()方法，但是如果窗体是模态的，则不会调用。

  所以ShowDialog的时候，要用Dispose()，Show()用Close()

* 

## 6. *窗体传参*

https://www.cnblogs.com/yieryi/p/4616688.html

### （1）重载构造函数——主传子

将要接受参数的子窗体的构造函数重载，主窗体在new子窗体时将参数通过构造传入。

### （2）委托传递——子传主

在主窗体类中实现函数getValue

```C#
public void getValue(typename val){
    
}
```

在子窗体中声明委托类型、声明委托对象

```C#
public delegate void dlgHandler(typename val);
dlgHandler dlg;
//将参数传给dlg

```

在主窗体类中new子窗体时，设置dlg的绑定函数为getValue

```C#
FormSon form = new FormSon();
form.dlg=getValue;
```

子窗体的传参事件

```C#
button_click(object sender, EventArgs e){
    dlg(v);	//子窗体中要传的参数是v
}
```



## 7. MessageBox

### （1）属性

```C#
DialogResult Show(string text, string caption, MessageBoxButtons buttons, MessageBoxIcon icon);
```

text：显示的文本

caption：消息框的标题

buttons：显示的按钮，枚举类型

> * MessageBoxButtons.OK
>
> - OKCancel：在消息框中显示“确定”和“取消”按钮。
> - AbortRetryIgnore：在消息框中显示“中止” “重试”和“忽略”按钮。
> - YesNoCancel：在消息框中显示“是” “否”和“取消”按钮。
> - YesNo：在消息框中显示“是”和“否”按钮。
> - RetryCancel：在消息框中显示“重试”和“取消”按钮。

icon：图标，枚举类型

> - None：在消息框中不显示任何图标。
> - Error：在消息框中显示由一个红色背景的圆圈及其中的白色X组成 的图标。
> - Question：在消息框中显示由圆圈和其中的一个问号组成的图标。
> - Warning：在消息框中显示由一个黄色背景的三角形及其中的一个感叹号组成的图标。
> - Information：在消息框中显示由一个圆圈及其中的小写字母 i 组成的图标。

按下按钮后，会返回一个DialogResult类型变量，枚举类型

> - None：消息框没有返回值，表明有消息框继续运行。
> - OK：消息框的返回值是 0K （通常从标签为“确定”的按钮发送）。
> - Cancel：消息框的返回值是 Cancel （通常从标签为“取消”的按钮发送）。
> - Abort：消息框的返回值是 Abort （通常从标签为“中止”的按钮发送）。
> - Retry：消息框的返回值是 Retry （通常从标签为“重试”的按钮发送）。
> - Ignore：消息框的返回值是 Ignore （通常从标签为“忽略“的按钮发送）。
> - Yes：消息框的返回值是 Yes （通常从标签为“是“的按钮发送）。
> - No：消息框的返回值是 No （通常从标签为“否“的按钮发送）。

### （2）demo

```C#
//创建一个窗体，单击该窗体弹出一个消息框提示“是否打开新窗口”，如果单击“是”按钮，则打开新窗口，如果单击“否”按钮，则关闭当前窗体
 private void Form1_Click(object sender, EventArgs e)
        {
            DialogResult flag = MessageBox.Show("是否打开新窗口", "message:", MessageBoxButtons.YesNoCancel, MessageBoxIcon.Question);
            if (flag == DialogResult.Yes)
            {
                Form2 form2 = new Form2();
                form2.Show();
            }else if (flag == DialogResult.No)
            {
                this.Hide();
            }
        }
```

## 8. Label/LinkLabel——显示

### （1）属性

> | Name      | 标签对象的名称，区别不同标签唯一标志                         |
> | --------- | ------------------------------------------------------------ |
> | Text      | 标签对象上显示的文本                                         |
> | Font      | 标签中显示文本的样式                                         |
> | ForeColor | 标签中显示文本的颜色                                         |
> | BackColor | 标签的背景颜色                                               |
> | Image     | 标签中显示的图片                                             |
> | AutoSize  | 标签的大小是否根据内容自动调整，True 为自动调整，False 为用户自定义大小 |
> | Size      | 指定标签控件的大小                                           |
> | Visible   | 标签是否可见，True 为可见，False 为不可见                    |
>
> TextAlign：文字在label中的位置是居中/靠左/靠右

### （2）事件

超链接标签主要应用的事件是鼠标单击事件 

### （3）demo

```C#
//创建一个窗体，在窗体上放置两个普通标签控件 (Label)，分别显示“早上好！”和“GoodMorning！”。在窗体上通过单击超链接标签 (LinkLabel) 交换这两个标签上显示的信息。
 private void linkLabel1_LinkClicked(object sender, LinkLabelLinkClickedEventArgs e)
        {
            string temp = string.Empty;
            temp = label1.Text;
            label1.Text = label2.Text;
            label2.Text = temp;
        }
```

## 9. TextBox——输入

### （1） 属性

> | Text         | 文本框对象中显示的文本                                       |
> | ------------ | ------------------------------------------------------------ |
> | MaxLength    | 在文本框中最多输入的文本的字符个数                           |
> | WordWrap     | 文本框中的文本是否自动换行，如果是 True，则自动换行，如果是 False，则不能自动换行 |
> | PasswordChar | 将文本框中出现的字符使用指定的字符替换，通常会使用“*”字符    |
> | Multiline    | 指定文本框是否为多行文本框，如果为 True，则为多行文本框，如果 为 False，则为单行文本框 |
> | ReadOnly     | 指定文本框中的文本是否可以更改，如果为 True，则不能更改，即只读文本框，如果为 False，则允许更改文本框中的文本 |
> | Lines        | 指定文本框中文本的行数                                       |
> | ScrollBars   | 指定文本框中是否有滚动条，如果为 True，则有滚动条,如果为 False， 则没有滚动条 |
>
> TestAlign：文本在框中位置

### （2）事件

 文本框控件最常使用的事件是文本改变事件 (TextChange)，即在文本框控件中的内容改变时触发该事件。 

### （3）demo

```C#
/**登录窗体仅包括用户名和密码，将登录窗体命名为 LoginForm。
单击“登录”超链接标签，对文本框中输入的用户名和密码进行判断，如果用户名和密码的输入值分别为 xiaoming 和 123456，则弹出消息框提示“登录成功！”，否则提示“登录失败！”*/
if("wk" == textBox1.Text && "123" == textBox2.Text){
	MessageBox.Show("connected!!","connect state",MessageBoxButtons.OK,MessageBoxIcon.Information);
}else{
	MessageBox.Show("not connected!!", "connect state", MessageBoxButtons.OK, MessageBoxIcon.Information);
          }
```

## 10. Button

### （1）属性

常用的是

Button.Text和外观设置相关的

### （2）事件

单击事件

### （3）demo

```C#
/**
实现一个简单的用户注册功能，并将提交的注册信息显示在新窗体的文本框中。
本例的用户注册界面中仅包括用户名和密码，通过单击“注册”按钮跳转到新窗体中并显示注册的用户名和密码，实现该功能分别使用 RegForm 窗体和 MainForm 窗体。
*/

//Form1.cs
 public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            bool flag = true;
            if (string.IsNullOrEmpty(textBox1.Text))
            {
                MessageBox.Show("用户名不能为空");
                flag = false;
            }else if (string.IsNullOrEmpty(textBox2.Text))
            {
                MessageBox.Show("密码不能为空");
                flag = false;

            }
            else if (!textBox2.Text.Equals(textBox3.Text))
            {
                MessageBox.Show("两次密码不一致");
                flag = false;

            }
            if (flag == true)
            {
                Form2 form2 = new Form2(textBox1.Text, textBox2.Text);
                form2.ShowDialog();
                //System.Environment.Exit(0);
            }
        }
//Form2.cs
public Form2(string name, string pwd)
        {
            InitializeComponent();
            this.label1.Text = "用户名为：   " + name;
            this.label3.Text = "密码为；    " + pwd;
        }
```



## 11. RadioButton——单选

### （1）属性

Checked——是否被选中

### （2）事件

### （3）demo

子窗口将参数用委托对象封装，主窗口将委托对象绑定getValue函数。

```C#
/**
根据题目要求，子窗口显示：用户权限包括“普通用户”“年卡用户”“VIP 用户”，因此需要 3 个单选按钮，按下确定，将字符串传到主窗体并显示
*/

//主窗体Form2
  public void getVal(string str)
        {
            this.label1.Text = str;
        }

        public Form2()
        {
            InitializeComponent();
            Form1 form1 = new Form1();
            form1.Show();
            form1.d1 = getVal;
        }

//子窗体Form1
public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        public delegate void dlg(string str);
        public dlg d1;

        private void button1_Click(object sender, EventArgs e)
        {
            string str = string.Empty;
            if (radioButton1.Checked)
            {
                str = radioButton1.Text;
            }else if (radioButton2.Checked)
            {
                str = radioButton2.Text;
            }else if (radioButton3.Checked)
            {
                str = radioButton3.Text;
            }
            d1(str);
            
        }
    }
```



## 12. CheckBox——复选框

一个CheckBox是一个复选按钮

### （1）属性

- Name：表示这个组件的名称；
- Text：表示这个组件的标题；
- Checked：表示这个组件是否已经选中。

### （2）事件

CheckedChanged 

### （3）demo

```C#
 public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            string msg = string.Empty;
            //或者用多个if判断，不能用ifelse否则其他复选框的逻辑就不会执行
            foreach (Control c in Controls)
            {
                if (c is CheckBox)
                {
                    if (((CheckBox)c).Checked)
                    {
                        msg += ((CheckBox)c).Text + " ";
                    }
                }
            }
            Form2 form2 = new Form2(msg);
            form2.Show();
        }
    }

 public partial class Form2 : Form
    {
        public Form2()
        {
            InitializeComponent();
        }
        public Form2(string msg)
        {
            InitializeComponent();
            this.label1.Text = "您的爱好是： " + msg;
        }
    }
```



## 13. CheckListBox——复选框列表

与CheckBox不同的是，多了一个框将复选按钮框起来作为一个整体，添加Item用add。并且可以高亮选中的项目。

https://www.cnblogs.com/janeaiai/p/4913927.html

### （1）属性

* CheckListBox.Items

  CheckListBox.Items[i]

  CheckListBox.CheckedItems

  CheckListBox.CheckedItems[i]

* 增加项

  ```C#
  //可放在窗体构造中
  checkedListBox1.Items.Add("散步");
  checkedListBox1.Items.Add("coding");
  checkedListBox1.Items.Add("游戏");
  ```

  或者直接在属性中的Items添加字符串，每行为一项

* 获取勾选项

  CheckItems[i]

  

* 判断第i项是否被勾选

  ```C#
  if（checkedListBox1.GetItemChecked(i)）
  {
      return true;
  } 
  else
  {
        return false; 
  }
  ```

  

* 设置第i项勾选状态

  ```C#
  checkedListBox1.SetItemChecked(i, true); //true改为false为没有选中。
  ```

  

* **遍历所有勾选项**

  ```C#
  for (int i = 0; i < checkedListBox1.Items.Count; i++)
  {
      if (checkedListBox1.GetItemChecked(i))
      {  
  		MessageBox.Show(checkedListBox1.GetItemText(checkedListBox1.Items[i]));
      }
  }
  ```

  ## *Items的类型是什么？*

  是ObjectCollection。Object是“上帝基类”。

  所以Items.Add填充的是什么变量类型，最后获取Items[i]时就是什么类型。

  ```C#
  //这里，Add()填充的是"散步"等string，所以容器中的元素也是string
  foreach (string outstr in checkedListBox1.CheckedItems)
  {
       MessageBox.Show(outstr);
  }
  ```

* 用一个CheckBox设置全选/全不选

  ```C#
  添加一个名为select_all的checkbox控件，由其控制checkedListBox是全选还是全不选。
  private void select_all_CheckedChanged(object sender, EventArgs e)
  {
       if(select_all.Checked)
  	{
       	for (int j = 0; j < checkedListBox1.Items.Count; j++)	//遍历所有项
           	checkedListBox1.SetItemChecked(j, true);
  	}
  	else
  	{
  		for (int j =0; j < checkedListBox1.Items.Count; j++)
       		checkedListBox1.SetItemChecked(j, false);
  	}
  }
  ```

  

* 获取蓝色选中项

  Selectedltem

### （2）事件



### （3）demo

```C#
public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            checkedListBox1.Items.Add("散步");
            checkedListBox1.Items.Add("游戏");
            checkedListBox1.Items.Add("coding");
            checkedListBox1.Items.Add("睡觉");
        }

        private void button1_Click(object sender, EventArgs e)
        {
            string msg = string.Empty;
            foreach(string str in checkedListBox1.CheckedItems)
            {
                msg += str + "  ";
            }
            MessageBox.Show("你选择了：  " + msg);
        }
    }
```



## 14. ListBox——列表显示，也有复选功能

### （1）属性

* MultiColumn——是否支持多行

* SelectionMode——None表示不能选中，One表示只能选中一行，MultiSimple表示选中多行

* Items——列表框中项的集合

  Items.Add()——添加项

  Items.Insert()——指定位置添加项

  Items.Remove()——移除项

* 选中项

  SelectedItems——选中项集合

  SelectedItem——当前选中项

  SelectedIndex——当前选中项索引，从0开始

### （2）事件



### （3）demo

```C#
 public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            listBox1.Items.Add("睡觉");
            listBox1.Items.Add("游戏");
            listBox1.Items.Add("coding");
            listBox1.Items.Add("散步");
        }

        private void button1_Click(object sender, EventArgs e)
        {
            string msg = string.Empty;
            foreach (string str in listBox1.SelectedItems)
            {
                msg += str + " ";
            }
            MessageBox.Show(msg);
        }
    }
```



## 15. ComboBox——下拉列表

提供已有选项供用户选择，避免用户输入非法值

### （1）属性

**组合框=文本框+列表框**

* DropDownStyle

  获取或设置组合框的外观，如果值为 Simple，**同时显示文本框和列表框，选中的项会放在文本框中，并且文本框可以编辑**

  如果值为 DropDown，则**只显示文本框，通过鼠标或键盘的单击事件展开文本框，选中的项会放在文本框中，并且文本框可以编辑**。默认情况下为 DropDown

  如果值为 DropDownList，显示效果与 DropDown 值一样，**选中的项会放在文本框中，但文本框不可编辑**。

* Items——列表框中项的集合

  Items.Add()——添加项

  Items.Remove()——移除项

  Items.Contains()——项中是否包含特定项

* SelectedItem——被选中的项（Item要变为string，需ToString）

  SelectedIndex——被选中的序号

* ComboBox.Text——获取当前在文本框中的文本

### （2）事件

**组合框**中的选项改变事件 SelectedlndexChanged

### （3）demo

```C#
  public Form1()
        {
            InitializeComponent();
            comboBox1.Items.Add("散步");
            comboBox1.Items.Add("coding");
            comboBox1.Items.Add("睡觉");
        }

        private void button1_Click(object sender, EventArgs e)
        {
            string msg = string.Empty;
            //msg = comboBox1.Text;
            //msg = comboBox1.Items[comboBox1.SelectedIndex].ToString();
            msg = comboBox1.SelectedItem.ToString();
            MessageBox.Show(msg);
        }
```

## 16. PictureBox——显示图片

### （1）属性

* Image——获取或设置图片控件中显示的图片

* ImageLocation——获取或设置图片控件中显示图片的路径

* **SizeMode**——获取或设置图片控件中图片显示的大小和位置

  如果值为 Normal，则从图片的左上角显示在控件中；

  如果值为 Stretchimage，则图片在图片控件中被拉伸或收缩，适合控件的大小；

  如果值为AutoSize，自动调整控件大小适应图片；

  如果值为 Centerimage，则控件>图片时图片居中显示，控件<图片时图片外围被裁剪显示图片中心；

  如果值为 Zoom，则图片会自动按原比例缩放至符合图片控件的大小

* 设置图片路径

  可用添加资源的方式

  ```C#
  this.pictureBox1.Image = global::WindowsFormsApplication6.Properties.Resources.img01;
  ```

  也可用添加本地图片路径的方式

  ```C#
  pictureBox.Image = Image.FromFile(string path);
  pictureBox.ImageLocation = 	path;
  ```

### （2）事件



### （3）demo

```C#
public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            //pictureBox1.ImageLocation = "C:\\Users\\25224\\Desktop\\img02.jpg";
            //pictureBox1.Image = Image.FromFile("C:\\Users\\25224\\Desktop\\img02.jpg");
            //pictureBox1.SizeMode = PictureBoxSizeMode.Zoom;
            this.pictureBox1.Image = global::WindowsFormsApp1.Properties.Resources.img01;
        }
    }
```



## 17. Timer——计时器

它并不直接显示在窗体上，而是与其他控件连用，表示每隔一段时间执行一次 Tick 事件。

### （1）属性

* 定时器控件中常用的属性是 Interval，用于设置时间间隔，以毫秒为单位

* 启动Start
<<<<<<< HEAD
* 停止Stop

## 18. 导出form为dll

https://blog.csdn.net/TimeFault/article/details/50206145

## 19. 创建用户控件

* 控件项目路径不能有#等非Unicode字符，否则`用户控件测试容器`无法启动
* https://blog.csdn.net/Codeeror/article/details/80380294
* 生成控件时注意目标平台（any CPU）和目标框架
* 
=======

* 停止Stop

  

## 18. 创建用户控件及使用

* 控件项目路径不能有#等非Unicode字符，否则`用户控件测试容器`无法启动
* https://blog.csdn.net/Codeeror/article/details/80380294
* 生成控件时注意目标平台（any CPU）和目标框架

### （1）用户控件UserControl和自定义控件CustomControl

https://blog.csdn.net/bluecard2008/article/details/103922589

* UserControl(用户控件)，也就是**复合控件**。继承自UserControl  主要用于开发Container控件。  
  (Container控件：可以添加其他Controls控件, .net  自带很多Container  控件, 其实Form就是一个Container)   

* CustomControl(自定义控件)继承自Control主要用于开发windows控件的最基本的类  象Text,Button都是。

* 用户控件使用步骤：

  新建`Windows窗体控件库`，然后在Release文件夹找到.dll文件。

  用户控件的运行同项目不同，是在用户控件测试容器中运行的。

  工具箱处右键，`添加选项卡`可新建控件分组。`添加项`将.dll添加到.NET Framework组件中，就可以使用自定义控件了。
>>>>>>> ac62793 (2021/6/9,winform笔记增加了注意点)
