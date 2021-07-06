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

* 页面组成

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

# 四、常用控件

## 1. XtraEditors.TextEdit——文本框（TextBox）

### （1）属性



## 2. XtraEditors.SimpleButton——按钮

**不支持添加背景图片backgroundimage，会被压在skin或Appearance后面。若想实现图片按钮，使用pictureEdit**

### （1）属性

AllowFocus——允许被focus，即鼠标点击时可选中，出现虚线选中框

**Appearance——**

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

代替普通messageBox，可以使用DevExpress skins。

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



## 9. navBarControl——侧面按钮面板

* add group
* add item
* add separator
* run designer

## 



# 五、CommonControls

## 1. SvgImageBox

### （1）属性

> SvgImage——图片
>
> Size——尺寸
>
> SizeMode——图片显示模式
>
> Location——位置
>
> TabIndex——Tab键选择的顺序
>
> OptionsSelection.Selection——
>
> OptionsSelection.UseCtrlToMultiSelect——
>
> ItemAppearance.Selected.BorderColor = System.Drawing.Color.Red;——选中时图片的边框的颜色
>
> ItemAppearance.Selected.FillColor = System.Drawing.Color.Lime;——选中时图片填充的颜色



