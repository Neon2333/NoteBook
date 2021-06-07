# winform笔记补充

http://c.biancheng.net/csharp/winform/

winform窗体应用程序是事件驱动的。鼠标或键盘发出事件，通过窗体和控件的属性和响应相关事件完成业务逻辑。

属性设置一般写在Designer.cs中

## 用户控件UserControl和自定义控件CustomControl

https://blog.csdn.net/bluecard2008/article/details/103922589

* UserControl(用户控件)，也就是**复合控件**。继承自UserControl  主要用于开发Container控件。  
   (Container控件：可以添加其他Controls控件, .net  自带很多Container  控件, 其实Form就是一个Container)   
* CustomControl(自定义控件)继承自Control主要用于开发windows控件的最基本的类  象Text,Button都是。

* 用户控件使用步骤：

  新建`Windows窗体控件库`，然后在Release文件夹找到.dll文件。

  用户控件的运行同项目不同，是在用户控件测试容器中运行的。

  工具箱处右键，`添加选项卡`可新建控件分组。`添加项`将.dll添加到.NET Framework组件中，就可以使用自定义控件了。

  

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
> StartPosition：窗口启动时的位置
>
> Test：窗体标题
>
> TopMost：是否在最上层
>
> WindowState：窗体启动时是最大化/最小化/普通

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

> | void Show()               | 显示窗体                 |
> | ------------------------- | ------------------------ |
> | **void Hide()**           | 隐藏窗体                 |
> | DialogResult ShowDialog() | 以对话框模式显示窗体     |
> | void CenterToParent()     | 使窗体在父窗体边界内居中 |
> | void CenterToScreen()     | 使窗体在当前屏幕上居中   |
> | void Activate()           | 激活窗体并给予它焦点     |
> | void Close()              | 关闭窗体                 |

### （1）ShowDialog()和Show()的区别？

Show显示的窗口可自由切换到其他窗口，Show后的代码立刻执行。

ShowDialog()显示的窗口是模态窗口，不能切换到其他窗口，ShowDialog()后的代码必须要等关闭了当前的窗体才会执行，被阻塞在ShowDialog

### （2）怎样将Hide的窗体重新显示？

https://www.cnblogs.com/hexiaobao/p/7692790.html

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

  ```
   private void Form3_FormClosed(object sender, FormClosedEventArgs e)
          {
              this.Dispose();
          }
  ```

  （2）在Form3的关闭事件中将DialogResult设置为OK,在调用者里面判断后释放

  ```
  //这是Form3的事件 
  private void Form3_FormClosed(object sender, FormClosedEventArgs e)
          {
              this.DialogResult = DialogResult.OK;
          }
  ```

  ```
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

  如果值为 Normal，则图片显不在控件的左上角；

  如果值为 Stretchimage，则图片在图片控件中被拉伸或收缩，适合控件的大小；

  如果值为AutoSize，自动调整控件大小适应图片；

  如果值为 Centerimage，则控件>图片时图片居中显示，控件<图片时图片外围被裁剪；

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
* 停止Stop