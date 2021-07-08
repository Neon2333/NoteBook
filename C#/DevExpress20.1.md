

# 问题总结

* TextEdit的高级模式找不到`UseAdvancedMode`属性
* LabelControl.UseMnemonic属性设置后，无法使用ALT+助记键选中相应label



# 博客汇总

https://zhuanlan.zhihu.com/p/34037924

# 一、安装

## 1. 汉化

https://www.devexpresscn.com/post/1081.html

将汉化包zh-Hans放到项目文件bin/Debug中，在Program.cs的Main函数中添加一句代码

```C#
//汉化代码
System.Threading.Thread.CurrentThread.CurrentUICulture = new System.Globalization.CultureInfo("zh-Hans");
System.Threading.Thread.CurrentThread.CurrentCulture = new System.Globalization.CultureInfo("zh-Hans");

```

# 二、属性优先级



# 三、窗体

## 1. XtraForm

*  XtraForm.FormBorderEffect设置边框被选中时发光或是阴影。ActiveGlowColor设定选中时边框的发光颜色，InactiveGlowColor设定未选中时边框的发光颜色。

* Opacity设定窗体透明度

* 

  

## 2. Ribbon Form

### 1. 问题

* 修改标题的[RibbonControl.ApplicationCaption](https://docs.devexpress.com/WindowsForms/DevExpress.XtraBars.Ribbon.RibbonControl.ApplicationCaption)和[RibbonControl.ApplicationDocumentCaption 两个属性不存在

* 增加边框宽度的`WindowsFormsSettings.FormThickBorder`的WindowsFormsSettings找不到是什么？
* Quick Access Toolbar 找不到在哪
* StatusBar找不到
* ribbon display mode selector找不到

### 2. 注意

* 2个标题字体可以分别使用`defaultBarAndDockingController1.Controller.AppearancesRibbon.FormCaption.ForeColor`和`defaultBarAndDockingController1.Controller.AppearancesRibbon.FormCaptionForeColor2`来设置，前者在AppearanceRibbon.FormCaption中，一个在AppearanceRibbon中。

* add page/add page group/add item


### 3. 页面组成

backstageViewControl——在RibbonForm右上角三角形处`add backstage view`

backstageViewClientControl——backstageViewControl右侧的空白部分 

ribbon——RibbonForm上方的菜单栏和图标快捷方式





## 3. Tabbed Form

怎么添加？？

## 4. Fluent Design Form

### 1. 属性

> enableAcrylicAccent——鼠标在左侧element移动时有光效
>
> 

# 4. 官方Demo

[官方Demo](dxdemo://Win/XtraEditors/MainDemo/SvgImageBox)

# 5. 常用控件

## 1. XtraEditors.TextEdit——文本框（TextBox）

### （1）属性

* EditValue——编辑文本。可选类型。

  Text只能输入文本

* Properties.Options下（[DevExpress.XtraEditors.Repository](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.Repository)文本编辑控件都有的属性）

  * [RepositoryItemTextEdit.CharacterCasing](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.Repository.RepositoryItemTextEdit.CharacterCasing)——EditValue的大小写

  * [RepositoryItemTextEdit.MaxLength](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.Repository.RepositoryItemTextEdit.MaxLength)——指定用户输入时可以输入的最多字符数

  * [RepositoryItem.NullText](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.Repository.RepositoryItem.NullText)——设置editor为空时默认显示的文本

  * [RepositoryItemTextEdit.NullValuePrompt](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.Repository.RepositoryItemTextEdit.NullValuePrompt)——设置editor为空时默认显示的文本

    NullText和NullValuePrompt两者区别：

  * ShowNullValuePrompt——显示NullText的情形
  * UseSystemPasswordChar——是否使用密码形式

* 不在Options中，直接使用代码指定

  * [TextEdit.SelectionStart](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.TextEdit.SelectionStart)——设置或获取被选择的字符的信息

    ```C#
    public int SelectionStart { get; set; }
    ```

  * TextEdit.SelectionLength——设置或获取被选择字符的长度

  * TextEdit.SelectedText——设置或获取被选择的文本

    ```C#
     private void textEdit1_MouseClick(object sender, MouseEventArgs e)
            {
                //string str = textEdit1.SelectedText;
                //MessageBox.Show(str);
                textEdit1.SelectionStart = 1;
                textEdit1.SelectionLength = 3;
            }
    ```

### （2）方法

* TextEdit.Copy——将被选中的文本拷贝到剪切板

  ```C# 
  public void Copy();
  ```

* TextEdit.Cut——剪切
* TextEdit.Paste——黏贴

### （3）高级模式（从V20.2开始才有）

[高级模式](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.Repository.RepositoryItemTextEdit.UseAdvancedMode)

从 TextEdit 类派生的所有编辑器（除了[TokenEdit](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.TokenEdit)、[RepositoryItemHypertextLabel](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.Repository.RepositoryItemHypertextLabel)和[HyperLinkEdit](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.HyperLinkEdit)）都可以在高级模式下运行。在这种模式下，编辑器使用自定义 DevExpress 文本框而不是标准的 Windows 窗体屏蔽框。此自定义文本框支持独特的功能，例如嵌入式编辑器标签和自定义插入符号动画。

## 2. XtraEditors.SimpleButton——按钮

**不支持添加背景图片backgroundimage，会被压在skin或Appearance后面。若想实现图片按钮，使用pictureEdit**

### （1）属性

AllowFocus——允许被focus，即鼠标点击时可选中，出现虚线选中框

**Appearance**

> BackColor——当BackColor2为Color.Empty时，指定背景色。当BackColor2指定颜色时，背景色为渐变色，指定渐变的起始色
>
> BackColor2——指定背景渐变色的终止色
>
> BorderColor——指定边框颜色
>
> Font
>
> > StrikeOut——划掉
> >
> > UnderLine——下划线
> >
> > GradientMode——渐变色类型：水平、垂直、前对角线、后对角线
> >
> > ForeColor——Text颜色
>
> Options——设定是否使用相关设置
>
> > UseBackColor——使用BackColor指定的值
> >
> > ...
>
> TextOptions
>
> > HAlignment——Text的水平位置
> >
> > VAlignment——Text的垂直位置
> >
> > WordWrap——是否换行
> >
> > Trimming——

**LookAndFeel——**

> 优先级：UseDefaultLookAndFeel > UseWindowsXPTheme > Style
>
> 拖动defaulLookAndFell控件，可设定全局的LookAndFeel。
>
> UseDefaultLookAndFeel——TRUE时，LookAndFeel被屏蔽，使用defaulLookAndFell控件设定的。优先级最高
>
> UseWindowsXPTheme——当UseDefaultLookAndFeel==FALSE时，若设为TRUE，且系统使用XP皮肤，则使用XP主题
>
> **Style**——当UseDefaultLookAndFeel和UseWindowsXPTheme均设为FALSE时，有效。
>
> **一旦Style使用skin，Appearance中设置的渐变效果就会失效，只保留BackColor1的效果。使用其他时，Appearance的渐变才有效**
>
> > Flat——按钮有一点立体边缘，按钮按下时有细微按下效果
> >
> > UltraFlat——按钮完全没有边框，鼠标移到上方时按钮整体变淡蓝色，按下时蓝色变深
> >
> > Style3D——按钮有明显立体效果，按下时有明显按下效果
> >
> > 

DialogResult——按钮按下时返回给parent form的结果：OK/Cancel/Yes/No

Dock——按钮在窗口锚定的位置

Font——与Appearance中Font是同一个，不要在这里设置

**ImageOption——是否屏蔽了BackGroundImage？？不支持BackGroundImage！！！**

> Image——位图bmp
>
> ImageToTextAlignment——图片位于文字的四边，选择三个位置中的某一个
>
> SVGImage——矢量图vector
>
> Location——位于按钮四边的某一个位置

```C#
//添加外部多张图片，通过imageList和imageIndex指定显示哪一张
//多张位图改变
this.simpleButton20.ImageOptions.ImageList = this.imageList1;            

private void simpleButton20_Click(object sender, EventArgs e)
{
    indexSimpleButton20ImageChanged = (++indexSimpleButton20ImageChanged) % 3;  //不要写成(indexSimpleButton20ImageChanged++) % 3
    if (indexSimpleButton20ImageChanged == 0)
    {
        this.simpleButton20.ImageOptions.ImageIndex = 0;

    }else if (indexSimpleButton20ImageChanged == 1)
    {
        this.simpleButton20.ImageOptions.ImageIndex = 1;
    }else if (indexSimpleButton20ImageChanged == 2)
    {
        this.simpleButton20.ImageOptions.ImageIndex = 2;
    }
}
```

```C#
//添加单张矢量图
this.simpleButton19.ImageOptions.SvgImage = ((DevExpress.Utils.Svg.SvgImage)(resources.GetObject("simpleButton19.ImageOptions.SvgImage")));
this.simpleButton19.ImageOptions.SvgImageSize = new System.Drawing.Size(20, 20);
```

```C#
//选择矢量图时将其添加到Project.Properties.Resource中，即可通过代码改变显示的矢量图
this.simpleButton21.ImageOptions.SvgImage = global::DevExpressDemo1.Properties.Resources.actions_arrow1left;

private void simpleButton21_Click(object sender, EventArgs e)
{
    indexSimpleButton21SvgImageChanged = (++indexSimpleButton21SvgImageChanged) % 4;  
    if (indexSimpleButton21SvgImageChanged == 0)
    {
        this.simpleButton21.ImageOptions.SvgImage = global::DevExpressDemo1.Properties.Resources.actions_arrow1up;
    }
    else if (indexSimpleButton21SvgImageChanged == 1)
    {
        this.simpleButton21.ImageOptions.SvgImage = global::DevExpressDemo1.Properties.Resources.actions_arrow1right;
    }
    else if (indexSimpleButton21SvgImageChanged == 2)
    {
        this.simpleButton21.ImageOptions.SvgImage = global::DevExpressDemo1.Properties.Resources.actions_arrow1down;
    }
    else if (indexSimpleButton21SvgImageChanged == 3)
    {
        this.simpleButton21.ImageOptions.SvgImage = global::DevExpressDemo1.Properties.Resources.actions_arrow1left;
    }
}
```

```C#
//选择单张位图
this.simpleButton1.ImageOptions.Image = ((System.Drawing.Image)(resources.GetObject("simpleButton1.ImageOptions.Image")));
```

ShowFocusRectangle——focus按钮时，是否显示选中**虚线框**

ToolTip——鼠标放置在控件上时的提示文本

ToolTipTitle——鼠标放置控件上时的标题

ToolTipIconType——设置提示文本的图标

SuperTip——该属性优先级高于ToolTip，该属性设置时ToolTip被屏蔽。这个属性是设置鼠标放在按钮旁边时，按钮是否有提示信息的属性，以及需要显示的信息。默认为空。

PaintStyle——

AppearanceDisable——按钮被disabled时

AppearanceHover——鼠标通过按钮时

AppearancePressed——按钮被按下



```C#
//如果真想修改Style，比较好的建议是在运行时修改其值，例如：
private void simpleButton1_Click(object sender, EventArgs e)
{
    this.defaultLookAndFeel1.LookAndFeel.UseWindowsXPTheme = false;
    this.defaultLookAndFeel1.LookAndFeel.Style = LookAndFeelStyle.Office2003;//要使用命名空间using DevExpress.LookAndFeel;

}
```



## 3. XtraEditors.ComboBoxEditor——下拉框



## 4. XtraEditors.CheckEdit——复选框



## 5. XtraEditors.TimeEdit——日期（DataTimePicker）



## 6. XtraBars.Navigation.TileBarItem.TileBarItem——



## 7. XtraMessageBox——消息框

Messages,Notification,Dialogs——代替普通messageBox，可以使用DevExpress skins。

* 显示

  DialogResult r = XtraMessageBox.Show();	//r是一个DialogResult类型枚举

* 计时自动关闭

  ```C#
  XtraMessageBoxArgs args = new XtraMessageBoxArgs();
  args.AutoCloseOptions.Delay = 5000; //计时单位ms
  args.Caption = "Auto-close message";
  args.Text = "This message closes automatically after 5 seconds.";
  args.Buttons = new DialogResult[] { DialogResult.OK, DialogResult.Cancel };
  XtraMessageBox.Show(args).ToString();	//XtraMessageBox.Show(XtraMessageBoxArgs) 
  ```

* 显示"do not ask again"复选框

```C#
args.DoNotShowAgainCheckBoxVisible = true;  //显示do not ask again
```

* 自定义按钮Icon

  上面用code添加的按钮都无icon。这里使用SvgImageCollection来存储图片，该控件可选择自带矢量图或导入。

  ```C#
  args.Showing += argsShowing;
  
  private void argsShowing(object sender, XtraMessageShowingArgs e)
          {
              foreach (var control in e.Form.Controls)
              {
                  SimpleButton button = control as SimpleButton;
                  if (button != null)
                  {
                      button.ImageOptions.SvgImageSize = new Size(16, 16);	//设定按钮中icon的尺寸
                      //button.Height = 25;
                      switch (button.DialogResult.ToString())
                      {
                          case ("OK"):
                              button.ImageOptions.SvgImage = svgImageCollection1[0];  //按钮图片
                              break;
                          case ("Cancel"):
                              button.ImageOptions.SvgImage = svgImageCollection1[1];
                              break;
                          case ("Retry"):
                              button.ImageOptions.SvgImage = svgImageCollection1[2];
                              break;
                      }
                  }
              }
          }
  
  ```

  

## 8. XtraCharts——图表



## 9. DevExpress.XtraNavBar.navBarControl——侧面按钮面板

* add group
* add item
* add separator
* run designer

## 



# 6. Common Controls

## 1. DevExpress.XtraEditors.SvgImageBox

https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox?v=20.1

### （1）属性

> BackColor——设置背景颜色，设置Transparent时背景其实黑色。直接放在form上时默认白色，放进容器时背景默认transparent。
>
> **BackgroundImage——为啥设置背景图不显示？？？**
>
> Dock——锚定位置	
>
> ForeColor——**文本**的字体颜色（**怎么添加文本？？？**）
>
> **ItemHitTestType——图标选中是需要精确选中，还是选中边框即可**
>
> > Precious
> >
> > BoundingBox
>
> SvgImage——图片	
>
> Size——尺寸
>
> SizeMode——图片显示模式
>
> > Squeeze
> >
> > Clip
> >
> > Zoom
>
> Location——位置
>
> LookAndFeel——设置皮肤。皮肤会和初始的外观叠加，**改变图标的原本颜色**。未研究这种叠加会造成的效果，最好不修改皮肤。
>
> TabIndex——Tab键选择的顺序
>
> OptionSelection
>
> > **OptionsSelection.SelectionMode**——单选或多选
> >
> > OptionsSelection.UseCtrlToMultiSelect——
>
> **ItemAppearance——设置所有Image element的默认外观**
>
> > Normal.FillColor = System.Drawing.Color.Lime;——设置**默认的**填充颜色
> >
> > Hovered.BorderColor = System.Drawing.Color.Red;——设置所有Image element的**悬停时**边框的颜色，Hovered的设定是针对每一个element的
> >
> > Selected.FillColor = System.Drawing.Color.Lime;——设置所有Image element**被选中时**填充的颜色，若图片由多个元素组成，需要分别选中
>
> **小三角Customize——自定义Image element外观等属性**
>
> > Appearance——进入Customize后，设置具体的某个Image element的外观，覆盖ItemAppearance设置的默认外观
> >
> > ID——默认是""
> >
> > Tag——默认是null

### （2）自定义SvgImage

Image的每一个元素都是SvgImageItem类型，由Group element和regular element组成，其中后者组成前者。若element是Group element，则SvgImageItem.IsGroup=true；

* **初始时属性**设置

  小三角处点击Customize，可访问SvgImageBox设计器。

  可更改Normal/Hovered/Selected时的item的**Appearance**。

* 代码设置**运行时**设置

  ```C#
  //RootItems访问Image element的根节点，可以是Group或regular，都可访问到
  SvgImageBox.RootItems[0].Visible = false;
  //若RootItems访问到的element是Grope element时，通过RootItems.Items访问Group内部元素
  SvgImageBox.RootItems[0].Items[1].Visible = false;
  ```

  ```C#
  svgImageBox5.RootItems[0].Items[1].Visible = false;
  svgImageBox5.RootItems[0].Items[0].Appearance.Normal.FillColor = Color.Aqua;
  ```

  <img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210707095238262.png" alt="image-20210707095238262" style="zoom:50%;" />

### （3）指定Image element

* 查找Image element常用函数

  ```C#
  //通过ID查找，未找到时返回null
  public SvgImageItem FindItemById(string id)
  //通过tag查找，未找到时返回null
  public SvgImageItem FindItemById(string id)
  //通过委托方式遍历所有image element，返回一个item
  public SvgImageItem FindItem(Predicate<SvgImageItem> predicate);
  //通过委托凡是遍历所有image element，返回一个item集合items
  //通过ForEach再遍历items中的所有item
  public SvgImageItem FindItems(Predicate<SvgImageItem> predicate);
  //返回符合指定条件的item的
  public List<SvgImageItem> SvgImageItem.FindAncestors(Predicate<SvgImageItem>)
  ```

* 通过ID属性

  ID属性为String类型，在尖括号中显示，<11>

  ```C#
  //遍历image element，找出所有Id="13"的item组成的集合
  var itemsId = svgImageBox5.FindItems(item => item.Id != null && item.Id.Equals("13"));	//若不设置Id，则默认为null
  itemsId.ForEach(item => item.Visible = false);
  //已知遍历的结果只有一个element时
  var itemId = svgImageBox5.FindItem(item => item.Id!=null && item.Id.Equals("13"));	//返回一个元素
  ```

  <img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210707103209367.png" alt="image-20210707103209367" style="zoom:50%;" />

  

* 通过Tag属性

  Tag是object类型，所以在设置时可选择类型再设value

  ```C#
  //遍历image element，找出Tag="shining"的将其Appearan.Normal.FillColor设为DarkRed
  var itemsTag = svgImageBox5.FindItems(item => item.Id != null && item.Tag.Equals("shining"));	//若不设置Tag，则默认为null
  itemsTag.ForEach(item => item.Appearance.Normal.FillColor=Color.DarkRed);
  ```

  <img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210707105506023.png" alt="image-20210707105506023" style="zoom:50%;" /> 	

  

### （4）事件

> [SvgImageBox.QueryHoveredItem](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox.QueryHoveredItem?v=20.1)——当您将鼠标光标移动到项目上时，允许您将自定义项目指定为“悬停”，而不管它们的Visibility。例如，当您将鼠标悬停在组的项目上时，您可以将组指定为“悬停”。
>
> [SvgImageBox.ItemEnter](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox.ItemEnter?v=20.1)——鼠标光标进入图形路径或边界矩形时触发。
>
> [SvgImageBox.ItemLeave](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox.ItemLeave?v=20.1)——鼠标光标离开图形路径或边界矩形时触发。
>
> [SvgImageBox.ItemClick](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox.ItemClick?v=20.1)——单击项目时触发。

### （5）设定悬停效果

默认不会突出悬停Hovered效果

* 初始时属性中设置

  ```C#
  svgImageBox6.ItemAppearance.Hovered.BorderColor = Color.Red;	//设置的是每个item被鼠标划过时的悬停效果
  ```

  

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210707112600824.png" alt="image-20210707112600824" style="zoom:50%;" />

* 使用`SvgImageBox.ItemHitTestType`属性指定鼠标选中时的精度要求。
* 使用`SvgImageBox.HoveredItem`属性访问当前悬停的项目
* 要判断item是否悬停，使用bool变量`SvgImageItem.Hovered`。 



### （6）选中效果

* SvgImageBox.OptionsSelection.SelectionMode——默认多选、可选择单选

* 单击item时会被选中，但是默认没有选中效果

  SvgImageBox.ItemAppearance.Selected设定被选中的item的外观变化

* **选中相关属性**

  > [SvgImageItem.Selected](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageItem.Selected?v=20.1) - 获取或设置项目是否被选中
  >
  > ```C#
  > public bool Selected { get; set; }
  > ```
  >
  > ```C#
  > //将Id为0的item设为选中
  > var itemsTag = svgImageBox6.FindItems(item => item.Id != null && item.Id.Equals("0"));
  > itemsTag.ForEach(item => item.Selected=true);
  > ```
  >
  > 
  >
  > 注意和ItemAppearance中的Selected区别

  > SvgImageBox.Selection——返回被选中item的集合
  >
  > ```C#
  >  foreach(var item in svgImageBox.Selection) {
  >         string itemDisplayName = string.Format("Seat number: {0}", ((string)item.Tag).ToUpper()); 
  >         selectedItemsListBox.Items.Add(itemDisplayName);
  >     }
  > ```
  >
  > 

  > [SvgImageBox.Select](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox.Select(DevExpress.XtraEditors.SvgImageItem)?v=20.1) - 选择指定的item
  >
  > ```C#
  > public void Select(SvgImageItem item)
  > ```
  >
  > ```C#
  > //通过Select(item)设定被选择的item
  > var itemId = svgImageBox6.FindItem(item => item.Id != null && item.Id.Equals("1"));
  > svgImageBox6.Select(itemId);
  > ```

  

  > [SvgImageBox.Unselect](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox.Unselect(DevExpress.XtraEditors.SvgImageItem)?v=20.1) - 取消选择指定的item
  >
  > ```C#
  > public void Unselect(SvgImageItem item)
  > ```

  > [SvgImageBox.SelectionChanged](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox.SelectionChanged?v=20.1) - 通知事件，被选中的item改变时触发
  >
  > ```C#
  > void OnSvgImageBoxSelectionChanged(object sender, EventArgs e) {
  >     selectedItemsListBox.Items.Clear();
  >     foreach(var item in svgImageBox.Selection) {
  >         string itemDisplayName = string.Format("Seat number: {0}", ((string)item.Tag).ToUpper()); 
  >         selectedItemsListBox.Items.Add(itemDisplayName);
  >     }
  > }
  > ```

  > 
  >
  > - [SvgImageBox.SelectionChanging](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SvgImageBox.SelectionChanging?v=20.1) - 当项目选择即将改变时触发。允许您取消当前操作..

  <img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210707145548942.png" alt="image-20210707145548942" style="zoom:50%;" />

### （7）demo

* SVG图片是SVG是一种图像[文件格式](https://baike.baidu.com/item/文件格式/6156907)，它的[英文](https://baike.baidu.com/item/英文/3079091)全称为Scalable Vector Graphics，意思为可缩放的[矢量图形](https://baike.baidu.com/item/矢量图形/1450649)，基于XML描述的图片，用户可以直接用[代码](https://baike.baidu.com/item/代码/86048)来描绘图像，可以用任何文字处理工具打开SVG图像。用来描述二维矢量及矢量/栅格图形。SVG提供了3种类型的图形对象：矢量图形（vectorgraphicshape例如：由直线和曲线组成的路径）、图象(image)、文本(text)。

* 添加Svg资源

  ImagePicker中Import，添加到Resource文件夹中。

* 注意区分：SvgImageBox.ItemAppearance和item.Appearance

```C#

```

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210708092849542.png" alt="image-20210708092849542" style="zoom: 80%;" />

## 2.  DevExpress.XtraEditors.PictureEdit——

### （1）属性

* Image/SvgImage——选择图片

* Properties.SizeMode——显示方式：Zoom、Stretch等

* Properties.PictureAlignment——图片显示位置

* Properties.ShowMenu——**右键**时是否显示**菜单**（copy、paste、delete、cut、open）

* Properties.ShowZoomSubMenu——右键菜单是否显示**放缩**按钮

* Properties.ZoomingOperationMode——鼠标**滚轮**控制放缩

* Properties.ShowEditMenuItem——右键菜单显示**编辑**按钮。打开`图片编辑器`。

  代码中：打开图片编辑器，使用[PictureEdit.ShowImageEditorDialog](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.PictureEdit.ShowImageEditorDialog)

* Properties.ShowCameraMenuItem——右键菜单是否显示`take picutre from camera`，允许最终用户从网络摄像头拍摄照片

  > Auto——插入网络摄像头时自动显示按钮
  >
  > Never——默认。永远不显示按钮
  >
  > Always——始终显示

  代码中：**ShowTakePictureDialog**

* Properties.Options.OptionsMask——掩膜

  > MaskLayoutMode——在图片上的布局位置
  >
  > MaskType——设置是否有Mask以及Mask的形状
  >
  > Offset——在图片上的偏移
  >
  > Size——掩膜的尺寸，容纳的图片的像素数

### （2）事件

* LoadCompleted
* TakePictureDialogShowing——当TakePictureDialog显示之前触发
* TakePictureDialogClosed——当TakePictureDialog关闭时触发
* ImageEditorDialogClosed——
* ImageEditorDialogShowing——

### （3）图像Mask

Properties.Options.OptionsMask.MaskType

## 3. DevExpress.XtraEditors.LabelControl——标签

### （1）属性

* AllowHTMLString——是否在Text中允许HTML格式文本。

  HTML文本以LabelControl.HyperlinkClick事件响应，并设置事件函数。

* AllowHmltextInToolTip——是否允许在ToolTip中显示Html文本

* Appearance——默认情况的外观

  > TextOptions
  >
  > > HAlignment——Text的水平位置。默认是Near
  > >
  > > VAlignment——Text的垂直位置。默认是Center

* ImageAlignToText——Icon显示在文本的位置

* ImageOptions——图片相关设置

  > Alignment——图片位置（和ImageAlignToText有啥区别？？）
  >
  > HoveredImage——悬停时显示的图片
  >
  > PressedImage——按下时显示的图片
  >
  > Image——ImageList。以ImageIndex选择图片
  >
  > LineVisible——当LabelControl尺寸大于文本时，会在文本旁显示`线`
  >
  > > LineLocation——线的位置：Top、Center、Bottom。默认Center
  > >
  > > LineOrientation——线的方向：Vertical、Horizontal。默认Horizontal。
  > >
  > > LineStyle——线型
  > >
  > > LineColor——线颜色
  >
  > ToolTip——提示内容
  >
  > ToolTipIconType——提示内容的Icon
  >
  > ToolTipTitle——提示内容的标题
  >
  > **UseMnemonic**——设为true时，可在Text中以&标记助记键。 ALT + 助记键会将焦点设置到 [Label](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.forms.label?view=net-5.0) tab 键顺序中跟随的控件（不知道为啥，ATL加助记键无效）

  ### （2）事件

  * HyperlinkClick——用户单击包含在当前LabelControl 中的超链接时触发

    

