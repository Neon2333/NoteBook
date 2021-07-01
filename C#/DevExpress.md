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



## 3. XtraEditors.ComboBoxEditor——下拉框



## 4. XtraEditors.CheckEdit——复选框



## 5. XtraEditors.TimeEdit——日期（DataTimePicker）













