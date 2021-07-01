# 安装

## 1. 汉化

https://www.devexpresscn.com/post/1081.html

将汉化包zh-Hans放到项目文件bin/Debug中，在Program.cs的Main函数中添加一句代码

```C#
//汉化代码
System.Threading.Thread.CurrentThread.CurrentUICulture = new System.Globalization.CultureInfo("zh-Hans");
System.Threading.Thread.CurrentThread.CurrentCulture = new System.Globalization.CultureInfo("zh-Hans");

```



# 常用控件

## 1. XtraEditors.TextEdit——文本框（TextBox）

### （1）属性



## 2. XtraEditors.SimpleButton——按钮

### （1）属性

AllowFocus——允许被focus，即鼠标点击时可选中，出现虚线选中框

Appearance——

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
>
> DialogResult——按钮按下时返回给parent form的结果：OK/Cancel/Yes/No
>
> Dock——按钮在窗口锚定的位置
>
> Font——与Appearance中Font是同一个，不要在这里设置
>
> ImageOption——是否屏蔽了BackGroundImage？？
>
> LookAndFeel——
>
> ToolTip——
>
> PaintStyle——
>
> ShowFocusRectangle——focus按钮时，是否显示选中虚线框

AppearanceDisable——

AppearanceHover——

AppearancePressed——



## 3. XtraEditors.ComboBoxEditor——下拉框



## 4. XtraEditors.CheckEdit——复选框



## 5. XtraEditors.TimeEdit——日期（DataTimePicker）













