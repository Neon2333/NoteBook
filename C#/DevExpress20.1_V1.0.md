

# 疑问总结

* 注意看designer.cs中的内容，了解代码形式怎么编写

* TextEdit的高级模式找不到`UseAdvancedMode`属性——版本问题

* LabelControl.UseMnemonic属性设置后，无法使用ALT+助记键选中相应label——

* [ListBoxControl的ContextButtons属性怎么添加RatingContextButton？？](#jump_ContextButtons)

* [ListBoxControl的动态指定模板怎么用？？？](#jump_模板)

* [SearchControl搜索设定在某一行上搜索时。绑定的表格是什么控件？？？](#jump_QSearchControl)

  ![image-20210712094824583](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712094824583.png)

* 怎么给template添加图片？？（CheckListBoxControl）

  ![image-20210713095400589](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210713095400589.png)

  ![image-20210713095443021](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210713095443021.png)

* TimeEdit和TimeSpanEdit的DisplayFormat和Mask

*  ImageComboBoxEdit选择下拉框中项目无法改变？？？

* 使用BehaviorManager绑定WindowsUIButtonPanel和NavigationFrame时，不知道为什么WindowUIButtonPanel的Button无法显示？？？？

---

# 博客汇总

https://zhuanlan.zhihu.com/p/34037924

---

# 安装

## 1. 汉化

https://www.devexpresscn.com/post/1081.html

将汉化包zh-Hans放到项目文件bin/Debug中，在Program.cs的Main函数中添加一句代码

```C#
//汉化代码
System.Threading.Thread.CurrentThread.CurrentUICulture = new System.Globalization.CultureInfo("zh-Hans");
System.Threading.Thread.CurrentThread.CurrentCulture = new System.Globalization.CultureInfo("zh-Hans");
```

---

# 属性优先级



---

# 窗体

## 1. XtraForm

*  XtraForm.FormBorderEffect设置边框被选中时发光或是阴影。ActiveGlowColor设定选中时边框的发光颜色，InactiveGlowColor设定未选中时边框的发光颜色。

* Opacity设定窗体透明度

  


## 2. Ribbon Form

### （1）问题

* 修改标题的[RibbonControl.ApplicationCaption](https://docs.devexpress.com/WindowsForms/DevExpress.XtraBars.Ribbon.RibbonControl.ApplicationCaption)和[RibbonControl.ApplicationDocumentCaption 两个属性不存在

* 增加边框宽度的`WindowsFormsSettings.FormThickBorder`的WindowsFormsSettings找不到是什么？
* Quick Access Toolbar 找不到在哪
* StatusBar找不到
* ribbon display mode selector找不到

### （2）注意

* 2个标题字体可以分别使用`defaultBarAndDockingController1.Controller.AppearancesRibbon.FormCaption.ForeColor`和`defaultBarAndDockingController1.Controller.AppearancesRibbon.FormCaptionForeColor2`来设置，前者在AppearanceRibbon.FormCaption中，一个在AppearanceRibbon中。

* add page/add page group/add item

### （3）页面组成

backstageViewControl——在RibbonForm右上角三角形处`add backstage view`

backstageViewClientControl——backstageViewControl右侧的空白部分 

ribbon——RibbonForm上方的菜单栏和图标快捷方式





## 3. Tabbed Form

怎么添加？？

## 4. Fluent Design Form

### （1）属性

> enableAcrylicAccent——鼠标在左侧element移动时有光效



---

# 官方Demo

[官方Demo](dxdemo://Win/XtraEditors/MainDemo/SvgImageBox)



---

#  常用控件

## [1. XtraEditors.SimpleButton——按钮](#jump_SimpleButton)

## [2. XtraEditors.MessageBox](#jump_MessageBox)

## [3. XtraEditors.TextEdit——文本框（TextBox）](#jump_TextEdit)

## [ 4. PictureEdit](#jump1)

##  [5. LabelControl](#jump2)

##  XtraEditors.ComboBoxEditor——下拉框



##  XtraEditors.CheckEdit——复选框



## XtraEditors.TimeEdit——日期



## XtraBars.Navigation.TileBarItem.TileBarItem





## XtraCharts——图表



## DevExpress.XtraNavBar.navBarControl——侧面按钮面板

* add group
* add item
* add separator
* run designer

---

# Common Controls——通用控件

##  <span id="jump_SimpleButton">1. XtraEditors.SimpleButton——按钮<span id="jump_SimpleButton">

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



## <span id="jump_MessageBox">2. XtraMessageBox——消息框<span id="jump_MessageBox">

Messages,Notification,Dialogs——代替普通messageBox，可以使用DevExpress skins

DialogResult r = XtraMessageBox.Show();	//r是一个DialogResult类型枚举

### （1）属性



### （2）计时自动关闭

```C#
XtraMessageBoxArgs args = new XtraMessageBoxArgs();
args.AutoCloseOptions.Delay = 5000; //计时单位ms
args.Caption = "Auto-close message";
args.Text = "This message closes automatically after 5 seconds.";
args.Buttons = new DialogResult[] { DialogResult.OK, DialogResult.Cancel };
XtraMessageBox.Show(args).ToString();	//XtraMessageBox.Show(XtraMessageBoxArgs) 
```

### （3）显示"do not ask again"复选框

```C#
args.DoNotShowAgainCheckBoxVisible = true;  //显示do not ask again
```

### （4）自定义按钮Icon

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

## <span id="jump_TextBox">3. XtraEditors.TextEdit——文本框（TextBox）</span>

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



## 4. DevExpress.XtraEditors.SvgImageBox——SVG图片框（ICON）

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

  <img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210707145548942.png" alt="image-20210707145548942" style="zoom:70%;" />

### （7）demo

* SVG图片是SVG是一种图像[文件格式](https://baike.baidu.com/item/文件格式/6156907)，它的[英文](https://baike.baidu.com/item/英文/3079091)全称为Scalable Vector Graphics，意思为可缩放的[矢量图形](https://baike.baidu.com/item/矢量图形/1450649)，基于XML描述的图片，用户可以直接用[代码](https://baike.baidu.com/item/代码/86048)来描绘图像，可以用任何文字处理工具打开SVG图像。用来描述二维矢量及矢量/栅格图形。SVG提供了3种类型的图形对象：矢量图形（vectorgraphicshape例如：由直线和曲线组成的路径）、图象(image)、文本(text)。

* 添加Svg资源

  ImagePicker中Import，添加到Resource文件夹中。

* 注意区分：SvgImageBox.ItemAppearance和item.Appearance

```C#

```

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210708092849542.png" alt="image-20210708092849542" style="zoom: 80%;" />



## <span id="jump1">5. DevExpress.XtraEditors.PictureEdit——图片框</span>

### （1）属性

* Image/SvgImage——选择图片

* Properties.SizeMode——显示方式：Zoom、Stretch等

* Properties.PictureAlignment——图片显示位置

* Properties.ShowMenu——**右键**时是否显示**菜单**（copy、paste、delete、cut、open）

* Properties.ShowZoomSubMenu——右键菜单是否显示**放缩**按钮

* Properties.ZoomingOperationMode——鼠标**滚轮**控制放缩

* Properties.ShowEditMenuItem——右键菜单显示**编辑**按钮。打开`图片编辑器`。

  代码中：打开图片编辑器，使用[PictureEdit.ShowImageEditorDialog](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.PictureEdit.ShowImageEditorDialog)

* Properties.ShowCameraMenuItem——右键菜单是否显示`take picutre from camera`，允许最终用户从`网络摄像头`拍摄照片

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

Properties.Options.OptionsMask.MaskType——Mask的形状

![image-20210712101917104](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712101917104.png)



## 6. DevExpress.XtraEditors.ImageSlider——图片滑动框

### <img src="D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210715162109333.png" alt="image-20210715162109333" style="zoom:50%;" />



## <span id="jump2">7. DevExpress.XtraEditors.LabelControl——标签</span>

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





## 8. XtraEditors.ListBoxControl——列表框

可填充来自data source的数据。

默认将item呈现为string

每个item都可以显示多个图片或文本，并设置为不同的外观和排版。

### （1）属性

* Items——ListBoxItem的集合，未绑定数据源时，使用索引增加、删除、访问元素

> * 使用Items可访问**ListBoxItemCollection**对象
>
>   ```C#
>   ListBoxControl.Items.ListBoxItemCollection	//item的集合
>   ```

* [BaseListBoxControl.DataSource](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.BaseListBoxControl.DataSource?v=20.1)——绑定数据源。绑定数据源后，Items集合为空。使用**GetItem**访问元素，返回数据源中的单个记录

  > ```C#
  > public object GetItem(int index)	//index从0开始
  > ```
  >
  > * DataSource属性改变时，会触发**DataSourceChanged事件**
  >
  > * 
  >
  >   

* MultiColumn——设置是否可以**多列**显示。若false，则item多到行数无法显示时，会出现**滑动条**。若true，则可多列显示

  ![image-20210708234507974](https://i.loli.net/2021/07/08/OFlJgzWie5HtjRv.png)

* SelectionMode——设置可同时选择**单个/多个**item

  ```C#
  public SelectionMode SelectionMode { get; set; }	//类型是SelectionMode枚举
  ```

  ![image-20210708234549790](https://i.loli.net/2021/07/08/t4k2B3eWfAZJ8X9.png)

* <span id="jump_ContextButtons">[ContextButtons](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.BaseListBoxControl.ContextButtons?v=20.1)——调用ContextButton管理器</span>

  BaseListBoxControl.ContextButtonClick——以集中方式处理context button的单击

  > [**RatingContextButton**](https://docs.devexpress.com/WindowsForms/DevExpress.Utils.RatingContextButton._members?v=20.1)——五角星评级
  >
  > * RatingContextButton.ItemCount——设定星的个数。默认5个
  > * RatingContextButton.Rating——获取当前选择的等级
  > * RatingContextButton.FillPrecision——可设置使用半整数。默认是整数

* ContextButtonsOptions——ContextButtons设置

* DataSource——绑定数据源

* HighlightentedItemStyle——高亮item的风格：宽或窄

* SortOrder——item的升序/降序
* Templates——模板编辑器

### （2）事件

* SelectedIndexChanged——选择的item改变时触发

* BaseListBoxControl.ContextButtonClick

  

### （3）DataSource绑定数据源

#### <1> Access

![image-20210712084354760](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712084354760.png)



#### <2>MySQL







### （4）搜索、过滤器

使用[SearchControl](#jump_SearchControl)——将ListBoxControl与之关联

```C#
SearchControl.Client = listBoxControl1;
```

![image-20210709103029462](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210709103029462.png)

### （5）<span id="jump_模板">模板</span>

* 当Templates非空时使用模板，要不使用模板，需清空Templates

* 默认时，Templates的第一个模板被用于当前ListBox的**所有item**

  可创建多个模板，分别用于**不同的item**。或通过**BaseListBoxControl.CustomItemTemplate事件**让不同情况下使用不同的模板

  ```C#
  //不同item指定不同模板
  
  //CustomItem.Template事件指定不同模板
  ```

  

* 模板可用于ListBoxControl、CheckedListBoxControl和ImageListBoxControl

* 若ListBoxControl绑定了DataSource，则**Columns**会显示数据源中所有可访问的字段以待使用

  ![image-20210709105307844](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210709105307844.png)

* **Elements**——用来显示添加到模板中的元素

  可用加号添加，也可以直接从Columns中拖到下方的模板编辑中。

  数据源中有的字段用**拖曳**的方法直接从Columns将需要的拖进模板编辑中，因为加号添加的时候都重叠在一个地方然后还要拖曳。

  ![image-20210709105540061](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210709105540061.png)

  添加数据源中没有的字段，用添加的方法

  ![image-20210712094554107](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712094554107.png)

  ![image-20210712094930196](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712094930196.png)

* Merge——合并单元格，Umerge——取消合并，Hide Grid——隐藏/取消隐藏网格线

  右键添加/删除单元格

  网格尺寸可以拖动改变

  拖动多个element到同一个单元格时，会重叠，需要设置AnchorAlignment

  ![image-20210709111239042](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210709111239042.png)

  AnchorElement——AnchorAlignment的基准Element

  ![image-20210709111348188](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210709111348188.png)

  

* 保存模板——BaseListBoxControl.CustomItemTemplate事件，动态使用不同模板

  ![image-20210709105928924](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210709105928924.png)

  

## <span id="jump_SearchControl">9. SearchControl——搜索栏</span>

搜索框，过滤数据

### （1）属性

* SearchControl.Client——绑定要过滤数据的控件

* Properties.Options.AllowAutoApply——false为按下Enter后才触发搜索

* Search.Properties.Options.FindDelay——在非自动搜索的情况下，设置延迟搜索

* ShowDefaultButtonsMode——搜索按钮、清除按钮的显示模式

  > AutoChangeSearchToClear/Default——搜索框无文本时显示搜索按钮，有文本时显示清除按钮
  >
  > AutoShowClear——搜索按钮一直显示。当搜索框有文本时显示清除按钮
  >
  > Always——两个按钮都一直显示

* ShowMRUButton——搜索历史按钮
* NullValuePrompt——搜索框中显示提示信息（按下Enter，当前文本添加进提示信息）

### （2）<span id="jump_QSearchControl">事件</span>

https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.SearchControl.QueryIsSearchColumn

* SearchControl.QueryIsSearchColumn——设定在某一行进行搜索

  每列都会触发

  读取Caption获得列标题，FieldName获取字段名，DataType获取数据类型

  设置IsSearchColumn=false，设置某列不在搜索范围，从而设定在搜索范围内的列

  ```C#
  private void searchControl1_QueryIsSearchColumn(object sender, QueryIsSearchColumnEventArgs args) {
      if (args.FieldName != "ShipCountry")
          args.IsSearchColumn = false;    
  }
  ```

  

##  10. XtraEditors.CheckEdit——复选框

### （1）属性

* Properties.Options.AllowGrayed——是否显示不确定框Indeterminate
* CheckState——指定复选框的状态：**Unchecked**、**Checked**或**Indeterminate**枚举值
* Checked——get/set，Checked状态

```C#
this.checkEdit42.Location = new System.Drawing.Point(401, 234);
this.checkEdit42.Name = "checkEdit42";
this.checkEdit42.Properties.Caption = "hide button";
this.checkEdit42.Size = new System.Drawing.Size(106, 19);
this.checkEdit42.TabIndex = 1;
```



### （2）样式

* Properties.Options.CheckBoxOptions.Style——设置样式

  ![image-20210712105117045](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712105117045.png)

> Custom——自定义。通过ImageOptions设置Checked/Unchecked的**图片、图片大小**。



* Style是Svg时

  ![image-20210712112839952](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712112839952.png)

  Properties.CheckBoxOptions——用于在相应状态中绘制矢量字形的颜色、尺寸（Style是Svg是前提）

  > **SvgColorUnchecked**
  >
  > **SvgColorChecked**
  >
  > **SvgColorGrayed**
  >
  > **SvgImageSize——默认18*18**

  ```C#
  	private void checkEdit43_CheckStateChanged(object sender, EventArgs e)
          {
              if (this.checkEdit43.Checked == true)
              {
                  this.checkEdit43.Properties.CheckBoxOptions.SvgImageSize = new Size(50,50);
              }
              else
              {
                  this.checkEdit43.Properties.CheckBoxOptions.SvgImageSize = new Size(16, 16);    //svg默认大小16*16
  
              }
          }
  ```

  ![image-20210712114245025](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712114245025.png)

  ![image-20210712114305519](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712114305519.png)

  

* 复选框根据当前应用的皮肤和调色板呈现

  ![image-20210712105157956](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712105157956.png)

* GlyphAlignment——设定文字和框的相对位置

![image-20210712111437657](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712111437657.png)

## 11. CheckedListBoxControl——复选框列表

### （1）属性

> BaseCheckedListBoxControl.Items - 允许您在未绑定模式下使用项目填充控件。
> BaseListBoxControl.DataSource - 使用此属性可使用数据源中的项目填充控件。
> BaseListBoxControl.Templates - 此属性支持项模板功能。请参阅模板化列表框项目以了解更多信息。
> BaseListBoxControl.CustomizeItem - 允许您动态自定义模板化项目。
> BaseListBoxControl.CustomItemTemplate - 允许您为列表框项目提供自定义模板。
> BaseListBoxControl.ValueMember - 获取或设置绑定数据源中的字段名称，其内容分配给项目值。
> BaseListBoxControl.DisplayMember - 获取或设置为列表框项提供显示文本的数据源字段的名称。当列表框项基于项模板呈现时，不支持此属性
> BaseListBoxControl.MultiColumn - 获取或设置列表框项是否可以跨多列排列。
> BaseListBoxControl.SelectionMode - 获取或设置是可以选择单个还是多个项目。
> BaseListBoxControl.ContextButtons - 提供对控件中显示的上下文按钮集合的访问。
> BaseListBoxControl.SelectedIndexChanged - 允许您响应项目选择。
> BaseListBoxControl.ItemHeight - 获取或设置项目的高度。
> BaseCheckedListBoxControl.GetItemEnabled - 使您能够在绑定模式下禁用特定项目。

### （2）事件

* [BaseListBoxControl.SelectedIndexChanged](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.BaseListBoxControl.SelectedIndexChanged?v=20.1) - 允许您响应项目选择
* [BaseCheckedListBoxControl.GetItemEnabled](https://docs.devexpress.com/WindowsForms/DevExpress.XtraEditors.BaseCheckedListBoxControl.GetItemEnabled?v=20.1) - 使您能够在绑定模式下禁用特定项目

### （3）热跟踪

* HotTrackItems——设置为true，有热跟踪效果

* 无法在SelectedMode=multi时使用

  ![image-20210713094138292](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210713094138292.png)

* HotTrackSelectMode——

  > SelectItemOnClick——虽然有热跟踪，但是高亮框需要Click才选中
  >
  > SelectItemOnHotTrack——热跟踪，高亮框跟着鼠标走
  >
  > SelectItemOnHotTrackEx

### （4）API



##  12. XtraEditors.ComboBoxEditor——下拉框

### （1）属性

* TextEditStyle——是否可编辑文本

* Properties.Items——集合可以是任何类型的。如果项目是**字符串**类型，则项目值和显示文本匹配。在其他情况下，项目的显示文本由项目的**ToString**方法确定

* RepositoryItemComboBox.AutoComplete——true时可输入字符串前几个字符来自动补全字符串。默认为true

* RepositoryItemComboBox.CycleOnDblClick——

* ComboBoxEdit.SelectedIndex——获取/设置选择item的index

  ```C#
  public virtual int SelectedIndex { get; set; }
  ```

* ComboBoxEdit.SelectedItem——当选中某个项目时，将选中item分配给SelectedItem

  

## 13. CheckedComboBoxEdit——



## 14. ImageComboBoxEdit——ComboxBoxEdit可以显示图标

### （1）属性

* Items——ImageComboBoxItem类型

  > Description——文本
  >
  > ImageIndex——图片集合的序号

* 图片集合支持

> ImageCollection——支持图片透明度
>
> SharedImageCollection——支持图片透明度，可在多个form的控件之间共享图像
>
> SvgImageCollection——存储可在高分辨率设备上放缩的Svg图片
>
> ImageList﻿

* LargeImages——下拉框显示的图片
* SmallImages——编辑框中显示的图片



## 15. RadioGroup——提供一组单选按钮

### （1）属性

* Items——添加RadioButton

  > Description——设定按钮文本
  >
  > Value——设定按钮的值。选中相应按钮时，会将item.Value赋值给Editvalue。若不设置，则Value=null

  ```C#
  // Create five items.
  object[] itemValues = new object[] {10, 11, 12, 13, 14};
  string [] itemDescriptions = new string [] {"Circle", "Rectangle", "Ellipse", "Triangle", "Square"};
  for(int i = 0; i < itemValues.Length; i++) {
      radioGroup1.Properties.Items.Add(new RadioGroupItem(itemValues[i], itemDescriptions[i]));
  //Select the Rectangle item.
  radioGroup1.EditValue = 11;
  ```

* ItemsLayout——设置Item的布局：Column（默认）/Flow

* SelectedIndex——选中的按钮的index

  ```C#
  public int SelectedIndex { get; set; }
  ```

### （2）事件

* SelectedIndexChanged——选中的按钮发生改变时触发

* EditValueChanged——RadioGroup的EditValue被赋予新值

  （先触发EditValueChanged再触发SelectedIndexChanged）

## 16. XtraEditors.TimeEdit——日期（DataTimePicker）

### （1）属性

> Properties.SpinStyle——时间编辑按钮是横向还是竖向
>
> Properties.TimeEditStyle——选择触控UI模式
>
> ![image-20210713143731860](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210713143731860.png)
>
> Time——编辑时间
>
> ```C# 
> public virtual DateTime Time { get; set; }
> ```
>
> **DisplayFormat**——
>
> > FormatString——
> >
> > FormatType——



### （2）代码中设定时间

* Time属性

  ```C#
   public Control_TimeEdit()
          {
              InitializeComponent();
  
              this.timeEdit4.Time = new DateTime(2021, 7, 13, 10, 30, 25, 670);
          }
  ```

### （3）时间显示格式——修改Mask

![image-20210713135016279](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210713135016279.png)

![image-20210713140001508](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210713140001508.png)

**若要在TextEdit中显示日期，也是使用Mask设置显示。**

### （4）TimeSpanEdit——时间跨度编辑

* ReadOnly——设置只读

```C#
DateTime NYDate = new DateTime(2021, 3, 8, 0, 0, 0, 0);
        public Control_TimeEdit()
        {
            InitializeComponent();

            this.timeEdit4.Time = new DateTime(2021, 7, 13, 10, 30, 25, 670);
            this.timeEdit5.Time = new DateTime(2021, 7, 13, 10, 30, 25, 670);

            timeSpanEdit2.Properties.DisplayFormat.FormatType = DevExpress.Utils.FormatType.Custom;
            timeSpanEdit2.Properties.DisplayFormat.FormatString = "{0:dd} days, {0:hh} hours, {0:mm} minutes and {0:ss} seconds";
            timeSpanEdit2.Properties.ReadOnly = false;
            timeSpanEdit2.Properties.Mask.EditMask = "dd:HH:mm:ss";
            RefreshTimer();
        }

        private void RefreshTimer()
        {
            DateTime currentDate = DateTime.Now;
            TimeSpan timeToNY = currentDate - NYDate;
            timeSpanEdit1.EditValue = timeToNY;
        }

        private void simpleButton1_Click(object sender, EventArgs e)
        {
            RefreshTimer();
        }

        private void simpleButton1_Click_1(object sender, EventArgs e)
        {
            labelControl1.Text = timeSpanEdit1.Properties.GetDisplayText(timeSpanEdit1.EditValue) + " left for the New Year!";

        }
```

## 17. XtraCharts——图表



## 19. DevExpress.XtraNavBar.navBarControl——侧面按钮面板





---

# Navigation & Layout——容器

## 1.  XtraBars.Navigation.TileBarItem.TileBar——磁贴

### （1）属性

* tilebar内添加tileGroup，在tileGroup中添加tileBar

* 在tileGroup的Text中设置标题

![image-20210715083153298](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210715083153298.png)

* tileBar——ScrollMode，滑动模式

  AllowSelectedItem——设为true，才允许磁贴被选中

* tileBarItem——DropDownControl，设置磁贴下拉菜单。用`tileBarDropDownContainer`，再往里添加控件（还可以添加tileBar）

  DropDownOptions——设置下拉菜单

  > AutoHeight——是否自动设置高度
  >
  > BackColorMode——下拉菜单的背景色
  >
  > > UseTileBackColor——使用磁贴颜色作为下拉菜单颜色
  > >
  > > UseBeakColor——使用BeakColor
  >
  > ImageOptions
  >
  > > ImageAlignment——图片位置
  >
  > TextAlignment——文字位置

* Hover、selected效果由`AppearanceItem`设置
* 将tileBarDropDownContainer置于底层，tileBar置于顶层



## 2. tileNavPane——分层次磁贴（最多三层）

### （1）概念

* 按钮分类

   TileNavCategory——顶层按钮

   TileNavItem——二层按钮

   TileNavSubItem——三层按钮

* 默认三层，可设置显示两层，将`TileNavPane.DefaultCategory`设为`ShowDefaultCategoryItems`

   

## 3. WindowsUIButtonPanel

**使用时，一定要注意Button是PushButton还是CheckButton。若是Checkbutton，则需要把GroupIndex设为同一正整数，通过每个button的Tag搭配ButtonChecked事件使用，可触发每个按钮被按下时的动作**

![image-20210715171703403](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210715171703403.png)

### （1）属性

* Orientation——设定按钮排列是水平/垂直

* BackColor——面板的背景颜色

* ForeColor——按钮和文本颜色

* UseButtonBackgroundImages——去掉按钮圆圈

  **Hover效果**手动设置——有圆圈时自带hover效果。去掉圆圈，若需Hover效果改变**图标和文本**颜色时，则需要手动设置`AppearanceButton.Hover.ForeColor`

  **选中效果**手动设置——BackColor是图标色，ForeColor是文本色

* Buttons

  > Add WindowsUIButton——添加按钮
  >
  > Add WindowsUISeparator——添加分割线
  >
  > Style——设定是pushbutton/checkbutton
  >
  > Caption——文本
  >
  > UseCaption——使用文本
  >
  > GroupIndex——pushbutton时设定按钮的index。checkbutton设为同一值则所有checkbutton设为一组

* ButtonInterval——按钮间距

* Dock——锚定

### （2）事件

* ButtonCheckd——通过`((WindowsUIButton)e.Button`访问按下的按钮

  ```C#
  using DevExpress.XtraBars.Docking2010;
  
  //checkButton
  void windowsUIButtonPanel1_ButtonChecked(object sender, ButtonEventArgs e) {
              string tag = ((WindowsUIButton)e.Button).Tag.ToString();	//checkButton时Caption被禁用了
              switch(tag) {
                  case "Ad1":
                      /* Navigate to page A */
                      break;
                  case "Ad2":
                      /* Navigate to page B */
                      break;
                  case "Ad3":
                      /* Navigate to page C*/
                      break;
                  case"Ad4":
                      /* Navigate to page D */
                      break;
                  case "Ad5":
                      /* Navigate to page E */
                      break;
              }
          }
  
  ```

## 4. BehaviorManager——将换页按钮绑定到页面

### （1）target为tileBar/imageSlider

通过`BehaviorManager`中的`Pager Navigation Behavior`将`pager`和`target`绑定，target可为`TileControl`、`TileBar`、`ImageSider`、`NavigationFrame`，pager可为`RadioGroup`和`WindowsUIButtonPanel`

![image-20210715155745451](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210715155745451.png)

```C#
//代码绑定target和pager
BehaviorManager bm = new BehaviorManager(this.components);
bm.Attach<PagerBehavior>(navigationFrame1, behavior => {
    behavior.Properties.Pager = windowsUIButtonPanel1;
    behavior.CustomizePagerItem += Behavior_CustomizePagerItem;
});

```

![image-20210715171806083](D:\WorkSpace\工作笔记\C#\DevExpress20.1.assets\image-20210715171806083.png)

### （2）target为 NavigationFrame

使用BehaviorManager绑定WindowsUIButtonPanel和NavigationFrame时，不知道为什么WindowUIButtonPanel的Button无法显示？？？？



## 5. NavigationFrame——

### （1）属性

* Pages——保存着NavigationPage，NavigationPage可容纳其他各种控件

  

  ![image-20210716102704733](D:\WorkSpace\工作笔记\C#\DevExpress20.1_V1.0.assets\image-20210716102704733.png)



* SelectedPage——当前展示的NavigationPage

  **通过WindowUIButtonPanel设定翻页**

  ```C#
  private void windowsUIButtonPanel2_ButtonChecked(object sender, DevExpress.XtraBars.Docking2010.ButtonEventArgs e)
          {
              string tag = ((WindowsUIButton)e.Button).Tag.ToString();	//checkButton时Caption被禁用了
              switch (tag)
              {
                  case "Contacts":
                      navigationFrame1.SelectedPage = navigationPage1;
                      break;
                  case "Calendar":
                      navigationFrame1.SelectedPage = navigationPage2;
  
                      break;
                  case "Mail":
                      navigationFrame1.SelectedPage = navigationPage3;
  
                      break;
              }
          }
  ```



* TransitionType——页面切换的不同效果

  

  

## 6. AccordionControl——



## 7. TabPane

