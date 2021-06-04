# winform笔记补充

http://c.biancheng.net/csharp/winform/

winform窗体应用程序是事件驱动的。鼠标或键盘发出事件，通过窗体和控件的属性和响应相关事件完成业务逻辑。

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

* 添加资源文件（背景图片jpg/图标png）到Resource.resx

  双击Resource.resx，点击`添加资源`右侧箭头，选择已有文件。

  直接点的话，生成bmp画图，自己画。

  保存后，资源文件添加到项目文件夹下的Resource文件夹下。

* 窗体程序启动时，只能显示一个窗体。在Program.cs中设置。

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

* ShowDialog()和Show()的区别？

  Show显示的窗口可自由切换到其他窗口，Show后的代码立刻执行。

  ShowDialog()显示的窗口是模态窗口，不能切换到其他窗口，ShowDialog()后的代码必须要等关闭了当前的窗体才会执行，被阻塞在ShowDialog

* 怎样将Hide的窗体重新显示？

  ```C#
  主窗口:
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
  子窗口:
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

  

## 4. MessageBox

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

## 5. Label/LinkLabel

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

## 6. TextBox

### (1)属性

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

```C#
/**登录窗体仅包括用户名和密码，将登录窗体命名为 LoginForm。
单击“登录”超链接标签，对文本框中输入的用户名和密码进行判断，如果用户名和密码的输入值分别为 xiaoming 和 123456，则弹出消息框提示“登录成功！”，否则提示“登录失败！”*/
if("wk" == textBox1.Text && "123" == textBox2.Text)
            {
                MessageBox.Show("connected!!","connect state",MessageBoxButtons.OK,MessageBoxIcon.Information);
            }
            else
            {
                MessageBox.Show("not connected!!", "connect state", MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
```













