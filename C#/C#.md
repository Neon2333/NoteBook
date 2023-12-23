lambda表达式和匿名方法、字段和属性、委托和事件、多线程、vs使用高级

迭代器：ienumerable

Linq：from/where/select

# 代码规范

变量应该是程序中使用最多的标识符了，变量的命名规范可能是一套C++命名准则中最重要的部分：变量的命名变量名由作用域前缀＋类型前缀＋一个或多个单词组成。为便于界定，每个单词的首字母要大写。对于某些用途简单明了的局部变量，也可以使用简化的方式，如：i, j, k, x, y, z ....　作用域前缀作用域前缀标明一个变量的可见范围。作用域可以有如下几种：前缀说明无局部变量m_类的成员变量（member）sm_类的静态成员变量（static member）s_静态变量（static）g_外部全局变量（global）sg_静态全局变量（static global）gg_进程间共享的共享数据段全局变量（global global）除非不得已，否则应该尽可能少使用全局变量。类型前缀类型前缀标明一个变量的类型，可以有如下几种：前缀说明n整型和位域变量（number）e枚举型变量（enumeration）c字符型变量（char）b布尔型变量（bool）f浮点型变量（float）p指针型变量和迭代子（pointer）pfn特别针对指向函数的指针变量和函数对象指针（pointer of function）g数组（grid）i类的实例（instance）对于经常用到的类，也可以定义一些专门的前缀，如：std::string和std::wstring类的前缀可以定义为"st"，std::vector类的前缀可以定义为"v"等等。类型前缀可以组合使用，例如"gc"表示字符数组，"ppn"表示指向整型的指针的指针等等。　推荐的组成形式变量的名字应当使用"名词"或者"形容词＋名词"。例如："nCode", "m_nState"，"nMaxWidth" ....

| 类型               | 写法   |
| ------------------ | ------ |
| private成员        | m_val  |
| private static成员 | ms_val |
| static变量         | s_val  |
| 全局变量           | g_val  |
| 全局静态变量       | gs_val |
| 枚举类型           | et_val |
| 枚举变量           | e_val  |
| int                | iVal   |
| float              | fVal   |
| double             | dVal   |
| string             | sVal   |
| bool               | bVal   |
| 数组               | aVal   |
| 指针               | pVal   |
| char               | cVal   |
| DateTime           | dtVal  |
| delegate           | dlgVal |
| event              | etVal  |



# 知识点收集

## 0. VS快捷键

| 按键      | 功能         |
| --------- | ------------ |
| F12       | 跳转定义     |
| shift+F12 | 查找所有引用 |
|           |              |



## 1. C#取整

```c#
向下取整：
    Math.Floor()	//3.1->3
向上取整：
    Math.Ceiling()	//3.1->4
```

## 2. Enumerable集合最大、最小、均值

* 最值

  实现了IEnumrable的集合可调用Max()/Min()/Average()方法

  https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.max?view=net-7.0

  ```c#
  Max<TSource>(IEnumerable<TSource>, Func<TSource,Int32>)
  Max<TSource>(IEnumerable<TSource>, Func<TSource,Double>)
  Max<TSource, TResult>(IEnumerable<TSource>, Func<TSource,TResult>) 
  ```

  ```c#
  yMaxLine = points.Max<PointF>(p => p.Y);
  ```

  

* 均值

  https://learn.microsoft.com/zh-cn/dotnet/api/system.linq.enumerable.average?view=net-7.0

  ```c#
   yAvgLine = points.Average<PointF>(p => p.Y);
  ```

## 3. 控件的Owner和Parent属性

Parent属性：访问父容器

Owner属性：访问拥有窗体的窗体。当一个窗体归另一个窗体所有，则Owner关闭或最小化时，该窗体也会关闭或最小化。

## 4. 设置字段默认值、属性访问器默认值

* 字段是类内部使用，设置默认值：

  ```c#
  public class Tree{
      public int Height{get;set;}=10;
      
      private string color="green";
      public string Color{
          get{
              return color;
          }
          set{
              color=value;
          }
      }
  }
  ```

* 属性是提供给类外访问的。

  对于简单类型的属性，比如Int32，Boolean等等这些Primitive类型，你可以在属性的声明前设置一个**DefaultValueAttribute**，在**Attribute的构造函数[DefaultValue()]里传入设置默认值**。但是这个**只是用来设置可视化设计器的。**

  字段的默认值不会因此而被初始化，**必须要在代码中手动初始化和DefaultValue相同的值**，如上初始化字段所示。

  ```c#
  //X范围
  private int xmin = 0;	//手动初始化和DefaultValue相同的值
  [
      Browsable(true),
      CateGory("布局"),
      Description(" X轴数值最小值 "),
      DefaultValue(0)
  ]
  public int Xmin
  {
      get => this.xmin;
      set
      {
          this.xmin = value;
          pictureBox_plot.Invalidate();	//修改属性后，重绘
      }
  }
  ```

* 复杂属性设置默认值

  自己定义Reset()函数负责重置，ShouldSerialize()。

  VS能够根据方法的名称来识别这种方法，比如Reset\<PropertyName>方法把**重置为默认值**，ShouldSerialize\<PropertyName>方法**检查属性是否是默认值**。过去我们把它称之为魔术命名法，应该说是一种不好的编程习惯，可是现在微软依然使用这种机制。

  ```c#
  private Color yMaxLineColor = Color.Red;
  public Color YMaxLineColor { get => yMaxLineColor; set => yMaxLineColor = value; }
  [
      Browsable(true),
      CateGory("外观"),
      Description( " 数据最大值标线 " ),
  ]
  public void ResetYMaxLineColor()
  {
      YMaxLineColor = Color.Red;
  }
  public bool ShouldSerializeYMaxLineColor()
  {
      return YMaxLineColor != Color.Red;
  }
  ```


## 5. double取3位小数

```c#
小数点后的位数固定string.Format("{0:F2}", 4384.5) //4384.50
```

## 6. String.Format()

```c#
String.Format("{0} is {1}, val1, val2");	//"val1 is val2"
string str = $"{val0} is {val1}";
```

## 7. 控件重绘

Invidate()

OnPaint()

```c#
protected override void OnPaint(PaintEventArgs e)

　　　　　　　 {

　　　　　　　　　　　 base.OnPaint(e);

　　　　　　　　　　　 StringFormat style = new StringFormat();

　　　　　　　　　　　 style.Alignment = StringAlignment.Near;

　　　　　　　　　　　 switch (alignmentValue)

　　　　　　　　　　　 {

　　　　　　　　　　　　　　　 case ContentAlignment.MiddleLeft:

　　　　　　　　　　　　　　　　　　　 style.Alignment = StringAlignment.Near;

　　　　　　　　　　　　　　　　　　　 break;

　　　　　　　　　　　　　　　 case ContentAlignment.MiddleRight:

　　　　　　　　　　　　　　　　　　　 style.Alignment = StringAlignment.Far;

　　　　　　　　　　　　　　　　　　　 break;

　　　　　　　　　　　　　　　 case ContentAlignment.MiddleCenter:

　　　　　　　　　　　　　　　　　　　 style.Alignment = StringAlignment.Center;

　　　　　　　　　　　　　　　　　　　 break;

　　　　　　　　　　　 }

　　　　　　　　　　　 // Call the DrawString method of the System.Drawing class to write　　 

　　　　　　　　　　　 // text. Text and ClientRectangle are properties inherited from

　　　　　　　　　　　 // Control.

　　　　　　　　　　　 e.Graphics.DrawString(

　　　　　　　　　　　　　　　 DisplayText,

　　　　　　　　　　　　　　　 Font,

　　　　　　　　　　　　　　　 new SolidBrush(TextColor),

　　　　　　　　　　　　　　　 ClientRectangle, style);

　　　　　　　 }

　　　 }
```

## 8. 用户控件DataSource属性写法

https://blog.csdn.net/softart/article/details/1935242

# 一、基础

## 0. .Net和C#

.Net是一个平台，一般指.Net Framework框架，类似JVM，除了C#还有VB等语言。C#是编程语言，可以在.Net平台下开发。

## 1. 注释

### （1）普通注释

vs快捷键：ctrl-k-c	ctrl-k-u

```c#
//
/**/
```

### （2）XML注释

对类、方法进行注释

```c#
///
```

### （3）#region和#endregion

折叠代码

## 2. VS常用快捷键

> * 代码对齐：ctrl-k-d
> * 代码提示：ctrl-j
> * 快速选中一行：shift-end、shift-home
> * MSDN帮助文档：F1
> * ctrl-l：剪切/删除一行
> * 翻页：ctrl-up、ctrl-down
> * 移动某行：alt-up、alt-down

## 3. 变量

### （1）成员常量

c/c++中都有全局常量变量，但C#中的常量变量都在类中。函数中的是局部常量，在类中的是成员常量。

成员常量和静态变量一样，不能通过对象访问，可以通过类名访问。

* const修饰的。初始化后值不能修改：

```c#
const int a = 2;	//值要确定于编译期
```

* readonly修饰的，初始化值可以修改：

```c#
ref readonly int aConstant; // aConstant can't be value-reassigned.
readonly ref int Storage; // Storage can't be ref-reassigned.
readonly ref readonly int CantChange; // CantChange can't be value-reassigned or ref-reassigned.
```

### （2）数组

* 定义

  ```c#
  int[] arr = new int[]{1,2,3,4};		//定义时初始化
  ```

* 



### （3）double和decimal

double可表示的范围远远大于decimal，但decimal的精度远远高于double。

decimal数值后要加个m，否则认为是double。

```c#
decimal a = 5000m;	
```

### （2）占位符

```c#
string name = Console.ReadLine();
string age = Console.ReadLine();
string gender = Console.ReadLine();
Console.WriteLine("name is {0}, gender is {2}, age is {1}",name, age, gender);	
Console.Readkey();
```

控制小数点后位数：

```c#
double a = 3.3333333;
Console.WriteLine("name is {0:0.00}", a);	//3.33
```

### （3）@取消转义字符

### （4）类型转换

#### 自动转换（隐式转换）

**没有精度丢失。**低精度转高精度。如：int转double。

```c#
int a = 1;
double b = a;	//将低精度转高精度
```

#### 强制转换（显式转换）

**发生了精度丢失。**高精度转低精度，如：double转int。

```C#
double a = 1.1;
int b = (int)a;
```

#### int类型加减乘除还是int。表达式中只要有double结果就是double

```c#
int a =10;
int b = 3;
double c = a / b;	//c=3
double d = a*1.0 / b;	//d=3.0		将int提升为double类型方法：乘以1.0
```

#### Convert类型转换

类型不兼容时，用Convert转换。

```c#
int Convert.toInt32(object value);
```

```c#
string str = "123";
double a = Convert.toDouble(str);
int b = Convert.toInt32(str);
```

#### Parse()和TryParse()

```c#
int int.Parse(string);
bool int.TryParse(string, out int);
```

#### 所有类型都可以转成string

```c#
.ToString()
```

### （5）变量的隐式初始化

根据变量类型不同，创建变量后自动初始化的值也不同。

值类型默认是0（bool类型是false），引用类型为null。

## 4. 数组

* 定义

  ```c#
  int[] arr = new int[3];
  int[,] arr = new int[3][4];		//定义二维数组
  int[,,] arr = new int[3][4][5];
  ```

* 初始化

  ```c#
  int[] arr = new int[] {1,2,3};		//定义时就初始化
  int[,] arr = new int[][] {{1,2}, {3,4}, {5,6}};
  ```

  可简写成：

  ```c#
  int[] arr = {1,2,3};		
  int[,] arr = {{1,2}, {3,4}, {5,6}};
  ```

* 交错数组

  元素是数组的数组，子数组长度不定，需要各个单独初始化。

  ```c#
  int[] arr = new int[3][];	
  arr[0] = new int[] {1,2,3}
  arr[1] = new int[] {2,3}
  arr[2] = new int[] {4,5,6,7}
  ```

* foreach遍历

  ```c#
  foreach(var i in arr)
  ```

  当arr中存的元素是值类型时不能修改数组的值，当元素是引用类型时可以通过i修改元素。


### Clone()和CloneTo()

CopyTo()和Clone()都属于浅拷贝,这一点是毋庸置疑的.对于浅拷贝:如果数组中的成员为值类型(如:int,float,double,byte等),则完全复制数值到目标数组中,如果是引用类型(如用户自定义类型:class Student,class People,或者是类库中的类类型:ArrayList等),则指复制引用给目标数组.文字有时候不如代码来得容易理解.但是这里也许用图更容易理解,看下图:

假定创建一个学生类数组Student[],然后浅拷贝到另一个学生类数组Student1[]中。

那么CopyTo()和Clone()方法的区别是什么呢?其实他们的区别,也就是MSDN上说的最大的区别就是用法上的区别.我们可以在VS弹出智能提示的时候看看他们的返回值,CopyTo()的返回值是void,使用方法如下Array1.CopyTo(Array2,0);其中Array2必须是一个已经实例化的数组.而Clone()的返回值是object.使用方法如下Array2 = Array1.Clone();其中Array2不必实例化.这样,我相信理解这两个方法的区别就很容易了.本质上并没有什么区别.都属于浅拷贝.如果拷贝所有的数组,就是用Clone().但是如果只是拷贝一部分,就可以选择CopyTo()了,CopyTo()的参数提示是这样的CopyTo(Array array,int Index).第二个参数index(索引)是指明从数组中的第几个对象开始复制.

相信到这里.应该很容易理解CopyTo()和Clone().下面说说浅拷贝和[深拷贝](https://so.csdn.net/so/search?q=深拷贝&spm=1001.2101.3001.7020)的区别.

如上面所说的,浅拷贝对于值类型则复制值,对于引用类型则**复制对象的引用**(类似于指针).深拷贝则是完完全全的创建一个个新对象.对原来数组中的所有对象全部创建新对象.对新数组中的修改不会影响原来数组中的值或对象.但是如何实现深拷贝呢?.NET库中似乎没有深拷贝的方法.这和实现深拷贝的原理有关系.若用户希望实现深拷贝.希望出现两个完全一样但又互不影响的数组.则必须自己写方法,对原数组中的每个对象实现拷贝,层层深入,直到这个对象中的对象中的对象……中的对象为值类型为止,因为只有值类型才是完全拷贝,对一个值进行修改不会影响另一个相同的值.这么说又有点难理解了.实现深拷贝的方法,如下图:

https://www.cnblogs.com/JamelAr/p/14156387.html

## 结构体

C#中结构体成员默认的访问权限是private。类成员（字段、方法）默认是private，构造函数默认是public。



## 5. 异常

### （1）try catch finally

https://blog.csdn.net/ananlele_/article/details/109056883

* 异常

  所有异常的父类是System.Exception。.NET框架内置了常见的异常。像DivideByZeroExcpetion等异常类是Exception的子类（派生类）。

  若没有进行try catch捕获异常，则.NET提供的默认机制将终止整个程序。

* 关键字作用

  ```c#
  try
  {
  	可能存在异常的代码块    
  }
  catch
  {
      异常处理代异常处理代
      throw new Exception();	//throw手动抛出异常
  }
  finally
  {
      必定会执行的清理代码
  }
  ```

  执行顺序：先try，若未发生异常，则跳过catch，执行finally；若发生了异常，则执行catch，再执行finally。

* try中若有return，则会先执行return，后执行finally。

* try后必须跟catch或finally中的至少一个。

  可以跟多个catch，用来捕获不同的异常进行不同的处理。但是需要将更加典型、直观的异常放在前面catch，将无参catch放在最后，一般不要只用无参catch。

  ```C#
  using System;
  class MyException
  {
      public static void Main()
      {
          int x = 0;
          int div = 0;
          try
          {
              div = 100 / x;
              Console.WriteLine("此行未执行");
          }
          catch (DivideByZeroException de)
          {
              Console.WriteLine("DivideByZeroException");
          }
          catch (Exception)
          {
              Console.WriteLine("Exception");
          }
          finally
          {
              Console.WriteLine("Finally块");
          }
          Console.WriteLine($"结果为 {div}");
      }
  }
  ```

* catch后不跟参数、跟(Exception)表示捕获所有异常。

* throw

  可以在try代码块中手动抛出异常。

  可以在catch代码块中将异常向更高的上下文重新抛出，由更高的上下文进行处理。这里只写一个throw。

* 用户自定义异常

  ```C#
  //用户自定义的异常
  class CustomException : Exception
  {
      public CustomException(string str)
      {
          Console.WriteLine("用户定义的异常");
      }
  }
   
  class MyException
  {
      public static void Main()
      {
          try
          {
              throw new CustomException("RAJESH");	//手动抛出异常
          }
          catch (Exception e)				//多态
          {
              Console.WriteLine("此处捕获异常" + e.ToString());
              throw;	//有异常抛给上文
          }
          Console.WriteLine("LAST STATEMENT");
      }
  ```

## 5. 枚举

### （1）定义

```c#
public enum State
{
    a=2;
    b=4;
    c;
    d;
    e;
}
//2、4、5、6、7
State st = State.a;
```

### （2）与string、int转换

* 与int直接强转

  ```c#
  int aa = (int)State.a;
  ```

* 与string强转

  ```c#
  object = Enum.Parse(Type typeEnum, string value);
  ```

  ```c#
  string str = "2";
  State st = Enum.Parse(Typeof(State), str);
  ```

### （3）修改成员数值类型——枚举记录开关状态

默认是int，修改成员可通过：

```c#
enum ee:ulong
```

这样成员的类型从int改为ulong。

我们可以用变量的每个bit位作为开关量的标识，这样uint32可表示32个开关量。可根据需要的开关量位数，修改enum成员的类型。

```c#
enum enumType:ushort{state1=0x00, state2=0x01, state3=0x04, state4=0x08...}
```

* 设定一个enumType类型的flag变量，比如置state1、state2、state3：

  ```c#
  enumType ops = state1|state2|state3;
  ```

* 判断某个状态的开关：

  ```c#
  bool useState1 = ((ops&state1)==state1);
  bool useState2 = ((ops&state2)==state2);
  ```



## 6. out/ref/params关键字

### （1）out——多个返回值

* 函数返回值当是多个相同类型的值时，可以使用数组返回。

  ```c#
  public static int[] f(int[] a)
  {
      int[] res = new int[3];
      return res;
  }
  ```

* 返回值是多个不同类型的值可用out（相同类型也可用）

  **返回的是变量的引用。**

  **out定义的形参在函数体内必须有赋值。（即必须要被初始化，才能访问那块内存）**

  不用写return了。

  ```c#
  public static f(int[] arr,out int max,out int min,out int sum,out int avg)
  {
      max=arr[0];
      min=arr[0];
      sum=0;
  	for(int i=0;i<arr.Length;++i)
      {
          if(max<arr[i]){
              max=arr[i];
          }
          if(min>arr[i]){
              min=arr[i];
          }
          sum+=arr[i];
      }	    
      avg=sum/arr.Length;	//avg必须被赋值
  }
  ```

  ```c#
  //写自己的Int.TryParse(string,out int)
  public static bool MyTryParse(string str, out int res){
      try
      {
      	res=Convert.toInt(str);
          return true;
      }
      catch
      {
      	return false;    
      }
  }
  ```

### （2）ref——传参数的引用

向形参中传递参数的引用，函数中对参数的修改将作用于原变量，都是作用于同一块内存区域。

**因此，传入ref形参的变量在传入前必须被初始化过，否则没有开辟内存。**

**实参传入时也要加ref。**

```c#
public void ff(ref int a, ref string c){...}

int m = 1;
string str = "hello";
ff(ref m, ref str);
```

* 索引器不能以ref修饰

  如：

  ```c#
  Dictionary dict = new Dictionary();
  dict.Add(key, val);
  
  Myfunc(ref dict[key]);	//这会报错
  
  int vval;
  dict.TryGetValue(key, out vval);
  Myfunc(vval);
  ```

  

### （3）params——一维变长数组

* 只能修饰一维数组
* 参数列表只能有一个params参数
* 只能修饰参数列表最后一个参数

* **允许调用函数时，向参数列表传入个数不定的参数作为一个变长数组，也可以直接传入一个整体的数组。**

```c#
public static void test(string str, int a, params int b[])
{
	for(int i=0;i<b.Length;++i){
        Console.WriteLine(b[i]);
    }    
}

test("aa", 0, 1, 2, 3, 4, 5, 6);	//传入的1,2,3,4,5,6作为数组
int[] c = { 2, 3, 4, 5, 6 };		
test("bb", 0, c);					//直接传入数组c
```

## 7. 值类型和引用类型

引用类型存放于堆，值类型存放于栈。

### （1）类型

* 引用类型

  array、string、class、delegate、interface

* 值类型

  基本类型、enum、struct

### （2）存储位置不同

* 值类型：在栈上直接存变量值

  **但是作为类的成员变量时，还是存储在堆上，因为堆是引用类型。**

* 引用类型：变量值存在堆上，在栈上也会开辟空间，存的是堆上的地址

  ```c#
  string a = "1234";
  // "1234"存在堆上，栈上存的地址
  ```

### （3）传递方式不同

体现在2个方面，一是实参传递给形参，而是变量赋值。

#### 传参

形参值类型：在栈中为形参开辟了内存，函数块中对形参进行操作不影响变量。

形参引用类型：

形参带ref：系统不在栈上开辟内存，而是形参和实参指向同一块内存，对形参的修改就会改变实参。

传入ref的实参必须是变量不能是数值，且传入的实参必须要先初始化。

## 8. string

### （1）string的不可变性和赋值

string和自定义类对象不同，虽然两者都是引用类型。但后者赋值`Person p1=new Person(); Person p2=p1;`后，指向同一块堆内存，改变p2，p1的值也会改变。

但string特殊在会重新开辟堆内存。

```c#
string s1 = "111";
string s2= s1;		//s2和s1指向同一块堆内存
s2="222";	
//s1是"111"还是"222"？
ANS:"111"
因为string是不可变的，给s2赋值"222"会开辟一块新的堆内存。
```

![image-20230211215626194](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230211215626194.png)

int型的n1重新赋值时，在栈中的值会被取代。

而string类型的s1，在栈中存的是地址指向堆中存储字符串值的位置。当对s1的值发生改变时，原先堆中存值的堆内存不销毁，重新开辟一块堆内存存储新值，s1存储新地址。（因此，字符串值的频繁改变：赋值、拼接等，会产生大量的堆内存垃圾。GC在程序结束时，会扫描内存，引用计数为0的堆内存会立即销毁。）

### （2）String.Format



### （3）string一些API

#### s.Remove、s.Insert、s.ToCharArray

string可以看成一个**只读**char数组，可通过索引get到对应字符。但不能通过索引set对应字符。

```c#
string s = "1234";
char c = s[0];
```

若想要改变string中某个字符：

```c#
string s = "hello";
str = s.Remove(3,1);	//str.Remove(int startIndex, int count)
str = s.Insert(3,"L");	//str.Insert(int index, string str)
//helLo
```

```c#
string s = "hello";
char[] ss = s.ToCharArray();	//ToCharArray()将string转成char数组
ss[3]='L';
s = new string(ss);				//new string(char[] ss)可将字符数组转成string
```

#### StringBuilder-对字符串进行大量的拼接、赋值操作

因为每次对string进行改变时会在内存产生垃圾，所以需要大量拼接和赋值时，使用StringBuilder类。

```c#
string s = null;
StringBuilder sb = new StringBuilder();
for(int i=0;i<10000;++i){
    //s+=i;			//耗时很长。因为每拼接一次都要开辟新内存。
    sb.Append(i);	//像sb对象里append字符串，不开辟新内存，一直操作同一块内存，速度快好几个数量级。
}
Console.WriteLine(sb.toString());
```

#### s.Length

#### s.ToUpper、s.ToLower

大小写转换

#### s.Equals

```c#
s.Equals(string value, StringComparision comparisonType)
// StringComparision是枚举类型，指定比较方式。
//StringComparision.OridinalIgnoreCase为忽略大小写比较
```

#### Split

返回值是string数组

```c#
string[] Split(params char[] chs, StringSplitOptions.RemoveEmptyEntries)
//以ch字符数组中的字符为分割符
//StringSplitOptions是枚举类型。RemoveEmptyEntries表示分割后返回的string数组中不包括空字符串""。
//None表示不去除空字符串""。这样出现chs中字符的地方将会是""    
```

#### Replace

将字符串中的oldvalue替换为newvalue

```c#
string Replace(string oldValue, string newValue)
```

#### Substring

取子串

```c#
string Substring(int startIndex, int len)
```

#### Contains

```c#
bool Contains(string val);
```

#### IndexOf

返回子串第一次出现的index

```c#
int IndexOf(string value, int startIndex)
```

#### LastIndexOf

返回子串最后一次出现的index

#### StartsWith

判断是否以子串value开始

```c#
bool StartsWith(string value)
```

#### EndsWith

判断是否以子串value结束

```c#
bool EndsWith(string value)
```

#### str.Trim

去掉字符串左侧和右侧的空字符

```c#
str.Trim()
str.TrimStart()
str.TrimEnd()
```

#### string.IsNullOrEmpty()

判断字符串是否为空

```C#
bool string.IsNullOrEmpty(string str)
```

#### Join

将数组每个元素以指定的分割符分隔

```c#
string string.Join(string seperator, params object[] values)
```

```c#
string[] str = {"111", "222", "333", "444"};
string strNew = str.Join("|", str);
Console.WriteLine(strNew);		// 111|222|333|444
```



## 9. 字段和属性

属性不是字段，是函数。

属性控制属性的读写权限，也可在其中加入逻辑代码，进行计算或是对赋值进行限定。

写好field后，**快捷键ctrl-r-e快速生成property**。

```c#
class Person
{
    private string _name;	//字段
    private int _age;
    private string _addr;
    private string _sex;
    
    public string Name	//属性。写法1
    {
        get
        {
        	return this._name;    
        }
        set
        {
        	this._name=value;    
        }
    }
    
    public int Age{get=>this._age; set=>this._age=value;}	//等价于上面的写法。写法2
    
    public string Addr{get;set;}	//等价于上面的写法，语法糖。写法3
    
    public string Sex=>"男";		//等价于{get=>this._sex;set=>"男";}	
}
```



## 10. static

### （1）非静态类

```c#
public class Person {
    private int a;
    private static int b;
    private void test(){}
    private static void test2(){}
}
```

* 非静态类，可以有实例成员（非静态），也可以有静态成员。

* 实例成员函数可以调用所有成员，包括静态成员。

* **静态函数只能调用静态成员，不能调用实例成员。test2()不能访问a。**

  静态成员是类的所有成员所有的，而实例成员为对象所有，新创建一个对象就会开辟一块新内存空间，就会有新的a。所以静态函数访问a是无意义的，因为有多少个对象就有多少个a。

### （2）静态类

一般用来封装一些全局使用的变量、方法。

构造方法要是**静态构造方法**。

静态类不能派生子类，静态类是密封类。（sealed class myClass）

```C#
public static class Global{
    public static int a;
    public static void deal(){}
}
```

* 只能有静态成员

## 11.var

类型推断，仅对函数内部的**局部变量**起作用。

**定义时必须初始化。**

```c#
var v = "123";
```

初始化后不能再给它赋与初值不同的值。

不会影响性能。

* 获取变量类型：

  ```c#
  var a = "123";
  Type type_a = a.GetType();
  ```

* 当变量的类型是已知时，var可选。当指定的变量类型是只供编译器本身访问时，必须用var。

  ```c#
  // Example #1: var is optional when
  // the select clause specifies a string
  string[] words = { "apple", "strawberry", "grape", "peach", "banana" };
  var wordQuery = from word in words
                  where word[0] == 'g'
                  select word;
  
  // Because each element in the sequence is a string,
  // not an anonymous type, var is optional here also.
  foreach (string s in wordQuery)
  {
      Console.WriteLine(s);
  }
  
  // Example #2: var is required because
  // the select clause specifies an anonymous type
  var custQuery = from cust in customers
                  where cust.City == "Phoenix"
                  select new { cust.Name, cust.Phone };
  
  // var must be used because each item
  // in the sequence is an anonymous type
  foreach (var item in custQuery)
  {
      Console.WriteLine("Name={0}, Phone={1}", item.Name, item.Phone);
  }
  ```




## 12. final

* 用于修饰变量、字段、函数、类

  用于变量时，该变量只能赋值一次，不可修改；

  用于方法时，该方法不能被重写（就是参数列表不变。重载是参数列表不同。）或隐藏；

  用于类时，该类不能被继承。

## 13. new

new关键字并不是创建类对象特有的，而仅仅只是表示在开辟内存。

对值类型变量来说，new表示在栈上开辟内存。

对引用类型来说，表示在堆上开辟内存，new表达式返回的是引用，这个引用赋值给变量，变量开辟在栈上。

```c#
Person p;	//在栈上开辟个内存
p = new Person();	//在堆上开辟内存，将引用返回给变量p
```

**还有个用法是子类屏蔽父类同名成员。**

在派生类中使用new声明此方法的隐藏。隐藏时，访问父类则调用父类的方法，访问子类则调用子类的方法。

```c#
namespace 隐藏
{
    class Program
    {
        static voidMain(string[] args)
        {
            ClassNew CN =new ClassNew();
           CN.SetName("new");
 
            BaseClass BC =CN;
           BC.SetName("基类");
        }
                 //基类
        public class BaseClass
        {
            public void SetName(string name)
            {
               Console.WriteLine("基类：我的名字是" + name);
            }
        }
//派生类
        public class ClassNew : BaseClass
        {
            //这里如果不使用new，将生成警告！
            //屏蔽了父类的SetName方法。哪怕是父类引用指向子类对象，调用SetName也是调用子类的。
            new public void SetName(string name)
            {
               Console.WriteLine("new：我的名字是" + name);
            }
        }
    }
}

/*运行结果：new：我的名字是new

 基类：我的名字是基类*/
```

## 14. 重写override

继承时发生，在子类中重新定义父类中的方法，子类中的方法和父类的方法是一样的，即方法名，参数，返回值都相同。

基类方法声明为virtual(虚方法)，派生类中使用override申明此方法的重写。

## 14. 重载overload

方法名相同，但函数签名不同。（方法名、形参个数、形参类型，返回值不是签名的一部分）

## 15. virtual和abstract

父类中声明为virtual的方法，可以被子类override。若不想父类的某个函数被override，将其修饰为final。

**LSP原则：不要override父类的非抽象方法。不要这么用！只override抽象类。**

```c#
class ff{
    virtual public void func(){}
}

class ss:ff{
    override public void func(){}
}
```

父类的**方法声明为abstract为抽象方法，父类即抽象类**，抽象类不可被实例化，只能作为父类被继承，子类override实现抽象方法。

## 16. ？与可空类型Nullable

?.运算符：NULL检查运算符，若对象为NULL则不进行取对象操作直接返回NULL：

```C#
int? firstX = points?.FirstOrDefault()?.X;
```

通过?可将非空类型int、double等设为null：

```c#
int? a = null;
int? b = 3;
//等价于:
Nullable<int> a = new Nullable<int>(3);
```

通过??可进行判断赋值：

```c#
int? a = null;
int b = a??3.14;	//若a为null，则将3.14赋值给b
```

https://www.cnblogs.com/youmingkuang/p/11459615.html

# 二、OOP

## 16. 构造函数

创建对象时执行，初始化**属性**。

不显式写时，会有一个默认的无参构造。显式写了以后，默认无参构造会消失，需要自己显式增加一个无参构造。

可重载。

```c#
class Person
{
    private int age;
    private string name;
    private string sex;
    
    public int Age{get;set;}
    public string Name{get=>this.name;set=>this.name=value;}
    public string Sex{get;set;}
    
    public Person(int age, string name, string sex)
    {
    	    this.Age=age;
        	this.Name=name;
        	this.Sex=sex;
    }
    
    public Person(int age, string name):this(age, name, "male"){}
    
    ~Person()
    {
        
    }
    
}
```

#### 静态构造函数

构造函数可以为静态。静态构造函数只能有一个，不能有参数，不能有访问权限修饰符。主要用于初始化类的静态字段（因为只有静态方法可以访问静态字段）。



## 17. this

在类中代指当前类的对象。

在类中显式调用**全参构造函数。**

```c
public Student(int age, string name):this(age, name, "male")	//this会显示调用全参构造，减少冗余代码
```

## 18. 析构函数

C#中有gc（garbage collection）机制，自动回收垃圾。

析构方法的调用时机和GC一样是不可预测什么时候调用的，因此也不应当依赖于它被调用。析构函数是确保已分配的非托管资源总能被释放的一个补救措施。如果可能就不应当被调用，**譬如说手动释放了非托管资源，此时应当通知GC取消对对象的析构函数的调用。**

**最后，如果你一定要手动分配非托管资源，那么记住析构函数是保险丝，是最后的保障，不是常规的做法。**

若需要立刻释放资源，可通过IDisposable接口实现Dispose方法，写释放资源的代码。然后调用GC.depressfinalize关闭调用析构函数。

## 19. 命名空间

用于解决类的重名问题，命名空间中有若干类。若不同命名空间中有同名的类，调用同名类时为了避免歧义，要加上命名空间。如：

项目1中有类A，项目2中有类A。项目2想要调用项目1的类A，需要：

> ①添加引用
>
> ![image-20230211213242222](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230211213242222.png)
>
> ②引入命名空间
>
> ```c#
> using Project1
> ```
>
> 

## 20. 继承

* 继承的优点：

  子类拥有父类的所有方法和属性，从而可以减少创建类的工作量。提高了代码的重用性。

  提高了代码的扩展性，子类不但拥有了父类的所有功能，还可以添加自己的功能。

* 继承的缺点：

  继承是侵入性的。只要继承，就必须拥有父类的***所有属性和方法***。

  降低了代码的灵活性。因为继承时，父类会对子类有一种约束。

  **增强了耦合性。当需要对父类的代码进行修改时，必须考虑到对子类产生的影响。**

* object类是所有类的父类。

* 避免类中代码冗余，将类中共有的部分提取成父类。

* **构造子类对象时，会先调用父类的构造在子类对象内部创建一个父类对象，这样子类对象才能访问父类中的成员。**

* 调用父类构造函数

  

  ```c#
  class Person{
      int _age;
      string _name;
      string _gender;
      
      public int Age{get;set;}
      public string Name{get;set;}
      public string Gender{get;set;}
      
      public Person(int age, string name, string gender){
          this._age=age;
          this._name=name;
          this._gender=gender;
      }
      
      public void sayHello(){
          Console.WriteLine("Person");
      }
  }
  
  class Coder:Person{
      string _language;
      
      public string Language{get;set;}
      
      //base调用父类Person的构造，将age/name/gender传入父类构造
      public Coder(int age, string name, string gender, string language):base(age,name,gender){
          this._language=language;	
      }
      
      //与父类同名的函数sayHello，将覆盖父类Person的sayHello
       public new void sayHello(){
          Console.WriteLine("Coder");
      }
  }
  ```

* 当子类中有和父类**同名的成员**（函数、变量）时，**将覆盖父类的成员**，子类对象在调用时只能调用子类的调用不到父类的。**用new关键字声明子类中和父类同名的成员，可以这条语句中屏蔽父类成员。**子类中可以使用base访问父类成员。

  但是不要这样写，违反LSP原则，不要override非抽象方法，可以重载overload。正确的写法是：

  子类中可以对父类的非抽象方法进行重载。

* 重载：**函数签名**不同，函数名相同。

* 函数签名：参数列表的个数、类型。不包括返回值。

* 不可——如果不想父类的非抽象方法被子类override，可以用final修饰父类方法，不可被覆写。

* 必须要——抽象父类`abstract public class Person`不可实例化，必须要被子类继承且override了其中的抽象方法`public void ff();`子类才可以实例化，否则子类也是抽象类。

* 都行——父类的方法被virtual修饰时，子类可以override该方法，也可以不override。

* 若不想父类方法被override，可用final修饰。

  

  LSP中规定不覆写

  ```c#
  Coder cc = new Coder();
  cc.sayHello();	//Coder。而不是Person
  ```

  ```c#
  ## 21. 里氏替换原则（LSP）
  
  设计模式6大原则其一
  
  易于理解的LSP说明：https://www.jianshu.com/p/dfcdcd5d9ece
  
  * 子类对象可以赋值给父类变量。
  
    ```c#
     Person p = new Student();
  ```

* 若父类变量中装的是子类对象，则可以将这个父类变量强转为子类对象。

  ```c#
  Student ss = (Student)p;
  ss.sayHello();	//Student
  ```

* LSP原则内容

> 



## 22. is和as强转

都是用于强转

* is转换成功则返回true，失败返回false

  ```C#
  if(p is Student)
  {
  	Student ss = (Student)p;
  	ss.sayHello();	//student
  }
  else
  {
      Console.WriteLine("转换失败");
  }
  ```

* as转换成功返回转换后对象，失败时返回null。

  **必须用于引用类型或可赋值为null的变量。**
  
  ```c#
  Student t = p as Student;
  t.sayHello();	//student
  ```
  
  

## 23. 委托

### 概念

**委托是具有相同签名、返回值的有序函数列表**，是引用类型。

方法列表：绑定到委托上的方法。委托会依次调用方法列表中的每个方法。**执行函数的顺序按照注册的顺序。**

判定方法列表是否为空：将委托变量和null比较。

初始化委托时，会将第一个方法放入方法列表：MyDel md = func;

### 定义委托的几种语法

通过等号赋值绑定到方法列表的方法只能是第一个方法，后面的绑定需要使用+=；

**若再次使用赋值给委托变量，就会产生新的委托变量，原来的委托变量将会被GC回收。**

### 委托添加、移出方法

* 通过+=添加方法，通过-=移出方法

* 通过Delegate的静态方法

  ```c#
  delegate void MyDelegate(int num);
  
  //创建3 个MyDelegate 委托类的实例
  MyDelegate myDelegatel = new MyDelegate(this.PrintNum);
  MyDelegate myDelegate2 = new MyDelegate(this.PrintDoubleNum);
  MyDelegate myDelegate3 = new MyDelegate(this.PrintTripleNum);
  
   MyDelegate myDelegates = null;
  //使用Delegate 类的静态方法Combine
   myDelegates = (MyDelegate)Delegate.Combine(myDelegates, myDelegatel);
   myDelegates = (MyDelegate)Delegate.Combine(myDelegates, myDelegate2);
   myDelegates = (MyDelegate)Delegate.Combine(myDelegates, myDelegate3);
   //将myDelegates 传入Print 方法
   this.Print(10, myDelegates);
  
  ///移除方法 
  myDelegates = (MyDelegate)Delegate.Remove(myDelegates, myDelegatel);
  ///移除所有
  myDelegates = (MyDelegate)Delegate.RemoveAll(myDelegates, myDelegatel);
  
  private void Print(int v, MyDelegate myDelegates)
  {
          
  }
  ```

绑定匿名方法使用lambda表达式，可在委托里内联一小段函数。

```c#
public delegate void DLG(int a);	//定义委托类型DLG
Dlg dlg = new DLG(a=>return a++);	//通过new DLG注册方法
DLG dlg += a=>return a++;			//定义委托实例dlg并注册一个lambda方法
```

委托在编译的时候确实会编译成类。因为Delegate是一个类，所以在任何可以声明类的地方都可以声明委托。

 **委托是一个类，它定义了方法的类型，使得可以将方法当作另一个方法的参数来进行传递，这种将方法动态地赋给参数的做法，可以避免在程序中大量使用If-Else(Switch)语句，同时使得程序具有更好的可扩展性。（https://www.cnblogs.com/neo98/articles/4916163.html）**

为什么要引入委托：https://www.cnblogs.com/zhili/archive/2012/10/22/Delegate.html

### 多播

可在委托上注册多个同签名的函数，调用委托时将按照注册顺序依次调用函数。

```c#
DLG dlg = sum;
dlg += sub;
dlg += multip;
dlg?.Invoke(1,2);	//3,-1,2
```

### 委托调用函数

委托变量可以直接当做函数传入参数使用。

**但是建议这么使用委托，可增加健壮性：**

```c#
dlg?.Invoke()	//防止dlg为null
```



## 24. 内置泛型委托Action、Func、Predicate

内置泛型委托类型。

Action对应的函数签名没有返回值，Func对应的函数签名有返回值。**最多16个参数**。

```c#
Action
Action<T>
Action<T1,T2>
Action<T1,T2,T3>
Action<T1,T2,T3,T4>
...
```

```c#
Func<TResult>
Func<T,TResult>
Func<T1,T2,TResult>
Func<T1,T2,T3,TResult>
Func<T1,T2,T3,T4,TResult>
...
```

Predicate是返回值为bool的Func，且输入参数只能有1个。

等价于 Func<T,bool>

```c#
var predicate = new Predicate<int>(x => x % 2 == 0)
```

```c#
Func<int a, double b, string str> dlg+=(int a, double b)=>return (a+b).toString();
```



## 25. 事件

### 移出事件绑定的所有方法

```C#
/// <summary>
        /// 移除所有的事件绑定
        /// </summary>
        /// <param name="clearEvent"></param>
        public static void clear_event<T>(ref T clearEvent) where T : Delegate
        {
            Delegate[] dels = (clearEvent as Delegate).GetInvocationList(); //event是特殊的delegate，所以类型还是delegate
            foreach (Delegate d in dels)
            {
                Delegate.RemoveAll(clearEvent, d);
                //得到方法名
                //object delObj = d.GetType().GetProperty("Method").GetValue(d, null);
                //string funcName = (string)delObj.GetType().GetProperty("Name").GetValue(delObj, null);
            }
        }
```

事件是成员，不是类型。要声明为public，外部程序才能向其注册handler方法。

**事件的声明依靠委托，其内部含有一个私有的委托。**

* 声明事件的步骤：

  > * 定义委托类型。**可以使用内置委托类型`EventHandler`**，使用`EventHandler(pbject sender, EventArgs)`事件为标准事件。
  >
  > * 定义事件。
  >
  >   ```c#
  >   public event EventHandler myevent1, myevent2;
  >   public static event EventHandler myevent;
  >   ```
  >
  > * 订阅事件，绑定事件handler。
  >
  >   ```c#
  >   myevent1 += (sender, args)=>{...}
  >   myevent1 += new EventHandler(func);
  >   ```
  >
  > * 触发事件。
  >
  >   ```c#
  >   //在函数ff中手动触发事件myEvent1
  >   public void ff(object sender, EventArgs e){
  >       if(myEvent1!=null){
  >           myEvent1(sender, e);
  >       }
  >   }
  >   ```

  

## 26. 自定义委托、事件传递参数

类EventArgs对象e中无法存储数据。**将参数从事件触发处传递到事件handler处。**

#### 方法1——通过自定义类MyEventArgs继承EventArgs，封装参数

```c#
//自定义MyEventArgs
class MyEventArgs:EventArgs{
    private string msg;
    public MyEventArgs(string msg){
        this.msg = msg;
    }
}
```

步骤：

* 自定义委托。可使用自定义类MyEventArgs作为参数的委托。

  ```c#
  public void delegate myEventHandler(object sender, MyEventArgs e);	//使用自定义的MyEventArgs
  ```

* 使用自定义的委托定义事件。

  ```c#
  public event myEventHandler myEvent;	//定义事件myEvent
  ```

* 绑定事件处理函数。

  ```c#
  public void func(object sender, MyEventArgs e){
      Console.WriteLine(e.msg);	//传出参数
  }
  
  myEvent+=func;		//将func添加到方法列表
  ```

* 触发事件

  ```c#
  public void trigger(object sender, EventArgs e){
      MyEventArgs args = new MyEventArgs("hello");	//封装参数"hello"
      myEvent(sender, args);		//触发事件，将参数传递到func定义处。
  }
  ```

#### 方法2——使用内置的泛型EventHandler事件定义

使用泛型事件定义，跳过自定义委托，自定义事件。

步骤：

* 自定义类myEventArgs继承自EventArgs类，封装信息，用于传递信息。

* 声明泛型事件

  ```c#
  public event EventHandler<myEventArgs> myEvent;
  ```

* 事件handler

  ```c#
  public void func(object sender, MyEventArgs e){...}
  ```

* 触发事件

  同上



## 27. 观察者模式(observer)

publisher、subscriber

https://www.cnblogs.com/zhili/p/ObserverPattern.html

### （1）事件订阅流程

publisher定义事件：

```c#
public event EventHandler myevent;
```

subscriber定义事件handler并订阅publisher的事件myevent：

```c#
public void func(object sender, EventArgs e){...}
myevent+=func;	//订阅
```

publisher状态发生改变触发事件，subscriber被广播通知调用handler：

```c#
public void trigger(object sender, EventArgs e){
    myevent(sender, e);	//向所有订阅者发布广播
}
```

https://blog.csdn.net/iteye_4195/article/details/82448068?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-82448068-blog-112131243.235%5Ev27%5Epc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-82448068-blog-112131243.235%5Ev27%5Epc_relevant_default&utm_relevant_index=3

### （2）通过事件传递参数

见26，将参数从publisher（触发事件，发生变化的一方）传递到subscriber（事件handler，接收变化做出反应的一方）。



## 28. 接口

接口就是一组未实现的方法的集合。类实现接口后，就可以调用其中的方法。

#### 声明

不能包含变量，只能包含方法、属性、事件，且都不能实现。

不能有任何访问权限修饰符，默认是public。

名称惯例以I开头。

#### 实现

只有类、结构可以实现接口，实现时必须实现接口中的所有成员。

当表示某个子类继承于父类的同时还要实现某些接口：

```c#
class Son:Father,Iinterface1,Iinterface2{
    ...
}
```

#### 使用

可以通过类使用实现的方法。

还有种用法：将实现了接口的类的对象强转为接口的引用变量，通过这个引用变量调用实现的方法。所有实现了该接口的类，都可以转换成这个接口的变量。

#### 接口可继承



## 29. 多态

父类引用指向子类对象。父类引用能否调用子类独有的成员？

# 三、其他



## 30. 外部方法

当方法声明包含[extern]修饰符时，称该方法为外部方法。外部方法是在外部实现的，编程语言通常是使用C#以外的语言。外部方法不可以是泛型。

```c#
class program
{
     	[DllImport("User32.dll")]
        //声明外部方法 使用关键字extern 由于配合DllImport属性使用，所以必须包含static关键字
        public static extern int MessageBox(int h, string m, string c, int type);
        static int Main(string[] args)
        {
            Console.WriteLine("请输入你的姓名");
            string name = Console.ReadLine();
            //利用return进行弹出对话框,所以需要将Main方法改为有返回值
            return MessageBox(0, "您好："+name+"\n\n"+"欢迎来到这里","提示", 0);
            Console.ReadKey();
        }
}
```

## 31. 扩展方法





## 32. typeof/GetType()

```c#
Type t = typeof(myClass);
FieldsInfo[] fi =t.GetFields();
MethodsInfo[] mi = t.GetMethods();
```

## 33. using语句

using statement分配资源，using语句会将statement隐式的放入try catch语句中，并在finally中加一个Dispose释放资源。所以使用using语句可以自动释放使用的资源。

## 34. 装箱拆箱

装箱：将值类型转成引用类型。

拆箱：将引用类型转成值类型。

## 35. 泛型（generic）

### （1）where约束

让编译器知道那些类型的参数可被接受。

where子句：

![image-20230312180600596](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230312180600596.png)

where T:nameofClass——表示T必须继承该类

where T:nameofInterface——表示T必须实现了该接口

```c#
class MyClass <T1,T2,T3> 
    where T2:struct
    where T2:class
{
	...        
}
```

### （2）泛型类

### （3）泛型委托

### （4）泛型方法

```c#
public void func(S, T)(S p):where S:Person
{
    ...
}
```

## 36. 定时器



## 37. DateTime

https://learn.microsoft.com/zh-cn/dotnet/standard/base-types/standard-date-and-time-format-strings

## 38. 反射（reflection）与特性（attribute）

https://liuhaowen.blog.csdn.net/article/details/118695042?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-118695042-blog-4891368.235%5Ev36%5Epc_relevant_default_base3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-4-118695042-blog-4891368.235%5Ev36%5Epc_relevant_default_base3&utm_relevant_index=5

https://blog.csdn.net/ananlele_/article/details/107979117



## 39. 索引器（indexer）

使可通过[]下标访问定义了索引器的类的成员。

允许索引是其他类型，不一定非要是int。

```c#
element-type this[int index]
{
   // get 访问器
   get
   {
      // 返回 index 指定的值
   }

   // set 访问器
   set
   {
      // 设置 index 指定的值
   }
}
```

```c#
using System;
namespace IndexerApplication
{
   class IndexedNames
   {
      private string[] namelist = new string[size];
      static public int size = 10;
      public IndexedNames()
      {
         for (int i = 0; i < size; i++)
         namelist[i] = "N. A.";
      }
      public string this[int index]
      {
         get
         {
            string tmp;

            if( index >= 0 && index <= size-1 )
            {
               tmp = namelist[index];
            }
            else
            {
               tmp = "";
            }

            return ( tmp );
         }
         set
         {
            if( index >= 0 && index <= size-1 )
            {
               namelist[index] = value;
            }
         }
      }

       public int this[string name]
      {
         get
         {
            int index = 0;
            while(index < size)
            {
               if (namelist[index] == name)
               {
                return index;
               }
               index++;
            }
            return index;
         }

      }
       
      static void Main(string[] args)
      {
         IndexedNames names = new IndexedNames();
         names[0] = "Zara";
         names[1] = "Riz";
         names[2] = "Nuha";
         names[3] = "Asif";
         names[4] = "Davinder";
         names[5] = "Sunil";
         names[6] = "Rubic";
         for ( int i = 0; i < IndexedNames.size; i++ )
         {
            Console.WriteLine(names[i]);
         }
         Console.ReadKey();
      }
   }
}
```



## 30. 容器

集合分为泛型集合和非泛型集合。

前者位于System.Collection.Generic命名空间，后者位于System.Collection。

**C# 将数据存于容器中是将原始数据直接存于容器中。而C++则是将原始数据拷贝了一份存于容器中，所以数据要能拷贝。**

### 非泛型集合

非泛型集合内部存储的是Object类型：ArrayList，Stack，Queue，HashTable



### 泛型集合

泛型集合：`List<T>、Stack<T>、Queue<T>、Dictionary<TKey,TValue>`

#### （1）Dictionary

* 取值

  ```c#
  if(dict.ContainsKey(key)){
      var val = dict[key];
  }
  ```

  ```c#
  dict.TryGetValue(key, out val);
  ```


* key的唯一性和相等比较

  一个key映射到唯一的一个value。通过key1==key2，从而可取值或是判断value1==value2。

  当key类型是基本类型时很直观。若key的类型是类，这个类需要有GetHashCode()和Equal()函数。

  https://www.cnblogs.com/wk2522466153/p/17584640.html







## 



## 41. 一些容易忽略的点

* struct/class默认访问权限是internal。struct成员默认访问权限是private。

* VS的应用程序和控制台程序的设置

  ![image-20230725155415767](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230725155415767.png)



# 四、多线程

线程是轻量的进程，C#程序运行时第一个线程自动创建，称为**主线程**。

线程生命周期开始于Thread对象创建时，状态有：未启动、就绪、休眠、死亡。

何时使用多线程：
1、提高CPU的利用率，从而提高了程序的效率；
2、当程序运行会卡住软件界面，为了使人不用焦虑等待时，采取线程与委托来处理，从而使软件界面运行流畅；
3、处理大量数据时间较长（显示一个进度条给界面）或不需要马上得到结果反馈给软件界面时；

## 1. Thread

创建线程对象：

```c#
Thread th = new Thread(new ThreadStart(func));	//new Thread传入的参数类型是委托类型：void delegate()
```

启动线程：

```c#
th.Start();
```

暂停线程：

```c#
th.Sleep();
```

终止线程：

```c#
th.Abort();
```

#### 如何优雅的终止线程

**Thread.Abort是在调用线程内抛出一个ThreadAbortException从而导致线程终止**。但是只有目标线程在调用**托管代码**时Abort()才会立即返回终止线程，若正在调用非托管代码（如外部C++代码）则不会立即返回。通过Thread.ThreadState获取状态可知，调用Abort抛出异常后，状态为ThreadState.AbortRequested，真正终止后状态为ThreadState.Aborted。

所以调用Abort()后需要循环判断ThreadState是否为Aborted，才能判断线程是否真正终止：

```c#
var thread = new Thread(
    new ThreadStart(
        () =>
            {
                while (true)
                {
                    //该线程会进行无限循环，自己不会结束
                    Thread.Sleep(100);
                }
            }));
 
thread.IsBackground = true;
thread.Start();//启动线程
 
thread.Abort();//调用Thread.Abort方法试图强制终止thread线程
 
//上面调用Thread.Abort方法后线程thread不一定马上就被终止了，所以我们在这里写了个循环来做检查，看线程thread是否已经真正停止。其实也可以在这里使用Thread.Join方法来等待线程thread终止，Thread.Join方法做的事情和我们在这里写的循环效果是一样的，都是阻塞主线程直到thread线程终止为止
while (thread.ThreadState!=ThreadState.Aborted)
{
    //当调用Abort方法后，如果thread线程的状态不为Aborted，主线程就一直在这里做循环，直到thread线程的状态变为Aborted为止
    Thread.Sleep(100);
}
 
//当跳出上面的循环后就表示我们启动的线程thread已经完全终止了
```

**但用Abort终止线程不是好方法，它通过抛异常终止线程可能会造成一些意想不到的问题，所以更好的方法是：**

定义终止标志位，当需要子线程终止时，改变标志位，在子线程的循环中判断标志位，若标志位改变则通过return终止子线程。适用于循环执行的子线程。

```c#
USBOP.ThreadStopFlg = true;         
while ((USBReadThread.ThreadState != System.Threading.ThreadState.Stopped) && (USBReadThread.ThreadState != System.Threading.ThreadState.Aborted))
{
    Thread.Sleep(10);	//主线程休眠等待子线程终止
}

//子线程循环中添加：
if (ThreadStopFlg == true) //判断是否该结束线程了  
{  
    ThreadStopFlg = false;  
    return;  
} 
```

阻塞调用线程，等待其他线程结束：

```c#
Join();
```

> 获取当前正在运行线程：CurrentThread
>
> 获取或设置某线程是否为后台线程：IsBackGround
>
> 获取线程状态：**ThreadState**（ThreadState.Running、ThreadState.Aborted等）



## 2. 异步委托BeginInvoke

为了降低创建、销毁线程的成本，CLR为每一个进程维护了一个线程池。开始，线程池是空的，如果进程使用的线程被创建并执行完毕后，不会被销毁而会被放入线程池。之后，进程需要一个线程，就会从线程池中还原一个线程，节省很多时间。

UI主线程要能及时相应用户操作，若程序后台需要执行很长时间就需要放到另一个线程。

通过EndInvoke获取子线程返回值：

* AsyncResult对象和IAsyncResult接口对象

  ![image-20230331123955719](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230331123955719.png)

* BeginInvoke和EndInvoke

  当调用BeginInvoke时，系统创建了一个AsyncResult对象，但返回的是IAsyncResult对象。

  > IAsyncResult.IsCompleted返回异步方法是否完成
  >
  > **IAsyncResult.AsyncState是BeginInvoke的最后一个参数，可在回调函数中通过IAsyncResult.AsyncState访问，起到了将BeginInvoke的最后一个参数传递到回调函数中的功能。**

  EndInvoke调用时若异步方法没有执行完，则当前线程会阻塞在EndInvoke直到异步方法返回值。

  ```c#
  delegate int dlg(int, int) = new delegate((a,b)=>return a+b);
  void func_callback(IAsyncResult iar){}	//回调函数
  IAsyncResult dlg.BeginInvoke(1, 2, new AsyncCallback(func_callback), null)	
  //BeginInvoke第三个参数是委托，传入函数编译器自动创建委托
  ```
  
  ```c#
  T result = dlg.EndInvoke(out p, IAsyncResult iar);	//异步方法中若有out参数，放到EndInvoke中执行
  ```
  
  **异步方法执行的三种方式：阻塞主线程等待子线程、主线程轮询子线程执行状态、子线程执行完调用回调函数。**

  * 轮询

  ```c#
  delegate int DLG(int a, int b);
  DLG dlg = new DLG((a, b) => return a + b);
  IAsyncResult iar  = dlg.BeginInvoke(1, 2, null, null)
  while(!iar.IsCompleted)
  {
      //异步方法未结束就继续执行某些操作
  }
  ```


  * 回调

    当异步方法执行完毕时自动调用回调函数。在回调里通过里氏转换将IAsyncResult转为AsyncResult获得委托。然后通过委托调用EndInvoke，此时异步方法已经执行完毕。

    ```c#
    public static void funcCallback(IAsyncResult iar)
    {
        string hello = (string)iar.AsyncState;
        Console.WriteLine(iar.IsCompleted);
        AsyncResult ar = (AsyncResult)iar;
        DLG ddlg = ar.AsyncDelegate;
        int ret = ddlg.EndInvoke(iar);
    }
    
    private void button1_Click(object sender, EventArgs e)
    {
        Dlg dlg = new DLG((a, b) => return a+b);
        IAsyncResult iar = dlg.BeginInvoke(2, 3, funcCallBack, "hello");
    }
    ```


**Thread.Start和BeginInvoke开辟的线程有什么区别：**

> 我们知道线程池有工作线程和IO线程，你写的多线程和并发只能操控到工作线程，而异步恰恰可以把线程池里的IO线程调动起来处理关于一切涉及IO方面的工作，取网络IO，文件IO。
>
> thread开启的线程和begininvoke有什么区别？https://www.cnblogs.com/zjoch/archive/2012/04/12/2443714.html
>
> Control.Invoke 方法 (Delegate) :在拥有此控件的基础窗口句柄的线程上执行指定的委托。
> Control.BeginInvoke 方法 (Delegate) :在创建控件的基础句柄所在线程上异步执行指定委托。
> Control的Invoke和BeginInvoke的参数为delegate，委托的方法是在Control的线程上执行的，也就是我们平时所说的UI线程。

## 3. Task/async

https://www.cnblogs.com/zhili/archive/2013/05/15/Csharp5asyncandawait.html

https://learn.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/calling-synchronous-methods-asynchronously

https://learn.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-using-delegates





## 4. Lock锁



## 5. Interlocked锁

https://blog.csdn.net/SmillCool/article/details/127118858

Interlocked是为多个线程共同访问的变量提供原子操作，这个类是一个静态类 它提供了以线程安全的方式递增、递减、交换和读取值的方法。
效率高于lock，但只能解决简单的同步问题：自增、自减、加、读取、赋值

https://blog.51cto.com/13713878410/1530358
这些方法都是线程安全的。
![image](https://img2023.cnblogs.com/blog/2415825/202305/2415825-20230515141606689-1126969250.png)

```c#
public static uint Exchange (ref uint location1, uint value);
//将value赋值给location1
//返回值：location1的原始值

public static float CompareExchange (ref float location1, float value, float comparand);
//location1与comparand比较，若相等，则将location1置value
//返回值location1的原始值
```

```c#
private int value1 = 0;
        public void TestIncrementUnSafe()
        {
            for (int i = 0; i < 5; i++)
            {
                Thread t = new Thread(IncrementValue1);
                t.Name = "t1 " + i;
                t.Start();
            }
            Thread.Sleep(2000);
            //value maybe 500000
            Console.WriteLine("value1 = " + value1);
        }
        private int value2 = 0;
        public void TestIncrementSafe()
        {
            for (int i = 0; i < 5; i++)
            {
                Thread t = new Thread(IncrementValue2);
                t.Name = "t2 " + i;
                t.Start();
            }
            Thread.Sleep(2000);
            //value should be 500000
            Console.WriteLine("value2 = " + value2);
        }
        private void IncrementValue1()
        {
            for (int i = 0; i < 1000000; i++)
            {
                value1++;
                //Console.WriteLine(Thread.CurrentThread.Name);
            }
        }
        private void IncrementValue2()
        {
            for (int i = 0; i < 1000000; i++)
            {
                Interlocked.Increment(ref value2);
            }
        }
/*
控制台输出：

运行结果1

value1 = 4612592
value2 = 5000000

运行结果2

value1 = 4697979
value2 = 5000000
*/

private int value3 = 0;
        public void TestExchangeSafe()
        {
            for (int i = 0; i < 5; i++)
            {
                Thread t = new Thread(ExchangeValue3);
                t.Name = "t2 " + i;
                t.Start();
            }
            Thread.Sleep(2000);
            //value should be 83
            Console.WriteLine("value3 = " + value3);
        }
        private void ExchangeValue3()
        {
            Interlocked.Exchange(ref value3, 83);
        }
        private int value4 = 0;
        public void TestCompareExchangeSafe()
        {
            for (int i = 0; i < 5; i++)
            {
                Thread t = new Thread(ExchangeValue3);
                t.Name = "t2 " + i;
                t.Start();
            }
            Thread.Sleep(2000);
            //value should be 99 or 0
            Console.WriteLine("value4 = " + value4);
        }
        private void ExchangeValue4()
        {
            //if value4=0, set value4=99
            Interlocked.CompareExchange(ref value4, 99,0);
        }
/*
控制台输出:

value4 = 0
value3 = 83
*/
```





# 五、LinQ

LINQ是.NET Framework 3.5的新特性。

https://www.cnblogs.com/lifepoem/archive/2011/10/25/2223765.html

## 1.前言
**sequence是实现了IEnumerable<T>的对象**
查询语法：类似SQL语句。
方法语法：System.Linq.Enumerable中定义了40个查询运算符。链式查询运算符。（https://www.cnblogs.com/lifepoem/archive/2011/10/27/2226556.html）
两种语法互补使用，方法语法使用的更多。
LINQ中最基本的数据单元是sequences和elements。
## 2.方法语法
### （1）查询运算符签名
Func<TSource, bool>委托匹配 TSource => bool表达式，接受TSource输入参数，返回一个bool值。当结果为true时，表示该元素会包含在输出sequence中。
```C#
 public static IEnumerable<TSource> Where<TSource>
            (this IEnumerable<TSource> source, Func<TSource, bool> predicate)

//静态方法写法：
IEnumerable<string> query = System.Linq.Enumerable.Where(names, n=>n.Contains('a'));
//Where第一个参数为实现了IEnumerable的集合，第二个参数为返回值为bool的委托。

//扩展方法写法：
IEnumerable<string> query=name.Where(n=>n.Contains('a'));	//扩展方法的写法可以方便的写成链式操作
//使用var简写
var query = name.Where(n=>n.Contains('a'));
```
大部分查询运算符都接受一个lambda表达式作为参数，Lambda表达式格式为：(parameters) => expression-or-statement-block。
正是扩展方法让LINQ查询运算符的链接成为了可能。
### （2）常用查询运算符
而对Where查询运算符来讲，它并不需要对输出element进行类型推断，因为它只是对输入elements进行过滤而不作转换，因此输出element和输入element具有相同的数据类型。
where 从句表示一种筛选过程，当条件满足的时候，这样的对象才可能被迭代返回出来。**如果多次条件筛选的话，那就相当于是 && 处理一样的效果。**
```c#
var student =
    from student in students
    where student.Age >= 18
    where student.Chinese + student.English + student.Math >= 180	//多次的Where相当于查询条件的&&
    select student.Name;
```
对于OrderBy查询运算符来讲，Func<TSource, TKey>把输入元素映射至一个排序键值。TKey由Lambda表达式的结果推断出来，比如我们可以按长度或按字母顺序对names数组进行排序。

First()——返回sequence中的第一个element
Last()——返回最后一个element
ElementAt()——返回某个index的element
OrderBy(lambda)——按照lambda指定的属性排序
Count()——对sequence计数
Min()——返回sequence的min
Max()
Contains(T ele)——返回是否包含某个ele
Any()——判断sequence中是否存在满足条件的ele
```c#
int[] intArray = new int[] { 0, 1, 2, 3 };
var items = intArray.Any(i => ((i % 2) == 0));
foreach (int item in items)
             Console.WriteLine(item);
          Console.ReadLine();
```
All()——判断sequence中是否所有ele都满足条件
Concat()——两个sequence连接在一起
Union()——两个sequence取并集


## 3.查询语法
### (1)查询语法及使用
查询表达式要求要以Select或Group结束。
### (2)方法语法和查询语法结合
方法语法有查询运算符，有的查询运算符没有对应的查询语法，如：Count()、First()、Last()。这种需要先以查询语法来写，最后结合查询运算符。适用于较为复杂的查询需求。
```C#
string[] names = { "Tom", "Dick", "Harry", "Mary", "Jay" };
 
            // 计算包含字母”a”的姓名总数
            int matches = (from n in names where n.Contains("a") select n).Count();     // 3
            // 按字母顺序排序的第一个名字
            string first = (from n in names orderby n select n).First();     // Dick
```

## 4.Linq的延迟执行
https://www.cnblogs.com/lifepoem/archive/2011/10/29/2228589.html
他们不是在查询创建的时候执行，而是在遍历的时候执行（换句话说，当enumerator的MoveNext方法被调用时）。

### （1）重复查询
延迟执行带来的一个影响是，当我们重复遍历查询结果时，查询会被重复执行。
调用ToArray、ToList可令Linq立刻执行，将查询结果保存到Array和List<>中。

### （2）局部变量捕获
如果查询的lambda表达式引用了程序的局部变量时，如果在查询定义之后改变了该变量的值，那么查询结果也会随之改变。
```c#
    int[] numbers = { 1, 2 };

            int factor = 10;
            IEnumerable<int> query = numbers.Select(n => n * factor);
            factor = 20;
            foreach (int n in query)
                Console.Write(n + "|");     // 20|40|
```

这个特性在我们通过foreach循环创建查询时会变成一个真正的陷阱。假如我们想要去掉一个字符串里的所有元音字母，我们可能会写出如下的query：

复制代码
              IEnumerable<char> query = "How are you, friend.";

            query = query.Where(c => c != 'a');
            query = query.Where(c => c != 'e');
            query = query.Where(c => c != 'i');
            query = query.Where(c => c != 'o');
            query = query.Where(c => c != 'u');
    
            foreach (char c in query) Console.Write(c); //Hw r y, frnd.
复制代码
尽管程序结果正确，但我们都能看出，如此写出来的程序不够优雅。所以我们会自然而然的想到使用foreach循环来重构上面这段程序：

复制代码
            IEnumerable<char> query = "How are you, friend.";

            foreach(char vowel in "aeiou")
                query = query.Where(c => c != vowel);
     
            foreach (char c in query) Console.Write(c); //How are yo, friend.
复制代码
结果中只有字母u被过滤了，咋一看，有没有吃一惊呢！但只要仔细一想就能知道原因：因为vowel定义在循环之外，所以每个lambda表达式都捕获了同一变量。当我们的query执行时，vowel的值是什么呢？不正是被过滤的字母u嘛。要解决这个问题，我们只需把循环变量赋值给一个内部变量即可，如下面的temp变量作用域只是当前的lambda表达式。

复制代码
            IEnumerable<char> query = "How are you, friend.";

            foreach (char vowel in "aeiou")
            {
                char temp = vowel;
                query = query.Where(c => c != temp);
            }
     
            foreach (char c in query) Console.Write(c); //Hw r y, frnd.
复制代码

### (3)查询运算符的模型和延迟执行
和传统的集合类型如array，linked list不同，一个装饰者sequence并没有自己用来存放元素的底层结构，而是包装了我们在运行时提供的另外一个sequence。此后当我们从装饰者sequence中请求数据时，它就会转而从包装的sequence中请求数据。

比如调用Where会创建一个装饰者sequence，其中保存了输入sequence的引用、lambda表达式还有其他提供的参数。下面的查询对应的装饰者sequence如图所示：

        IEnumerable<int> lessThanTen = new int[] { 5, 12, 3 }.Where(n => n < 10);


 当我们遍历lessThanTen时，实际上我们是在通过Where装饰者从Array中查找数据。

而查询运算符链接创建了一个多层的装饰者，每个查询运算符都会实例化一个装饰者来包装前一个sequence，比如下面的query和对应的多层装饰者sequence:

            IEnumerable<int> query = new int[] { 5, 12, 3 }
                .Where(n => n < 10)
                .OrderBy(n => n)
                .Select(n => n * 10);


在我们遍历query时，我们其实是在通过一个装饰者链来查询最初的array。
需要注意的是，如果在上面的查询后面加上一个转换运算符如ToList，那么query会被立即执行，这样，单个list就会取代上面的整个对象模型。

## 5.过滤运算符
https://www.cnblogs.com/lifepoem/archive/2011/11/16/2250676.html
![image](https://img2023.cnblogs.com/blog/2415825/202305/2415825-20230506105634025-934090996.png)

## 6.排序、分组
* OrderBy, ThenBy——升序排序
	ThenBy只会对那些在前一次排序中拥有相同键值的elements进行重新排序，我们可以连接任意数量的ThenBy运算符。
	```c#
	 // 先按长度排序，然后按第二个字符排序，再按第一个字符排序
            IEnumerable<string> query =
                names.OrderBy (s => s.Length).ThenBy (s => s[1]).ThenBy (s => s[0]);
	```
* OrderByDescending, ThenByDescending——对一个sequence按降序排序
* Reverse——按倒序返回一个sequence

### 自定义排序规则
要排序先要定义比较规则。
基本型别都提供了默认的比较算法，如string提供了按字母进行比较，int提供了按整数大小进行比较。
* 当我们创建了自己的实体类，如Student，默认想要对其按照年龄进行排序，则需要为**实体类继承IComparable接口，实现CompareTo方法。**
```c#
 class Student:IComparable
    {
        public string Name { get; set; }
        public int Age { get; set; }

        #region IComparable Members

        public int CompareTo(object obj)
        {
            Student student = obj as Student;
            if (Age > student.Age)
            {
                return 1;
            }
            else if (Age == student.Age)
            {
                return 0;
            }
            else
            {
                return -1;
            }
            //return Age.CompareTo(student.Age);
        }
        #endregion
    }
```
* 如果不想使用年龄作为比较器了，那怎么办。这个时候IComparer的作用就来了，可使用IComparer来实现一个自定义的比较器。
```c#
class SortName: IComparer
    {
        #region IComparer Members

        public int Compare(object x, object y)
        {
            Student s1 = x as Student;
            Student s2 = y as Student;
            return s1.Name.CompareTo(s2.Name);
        }

        #endregion
    }
```
在排序的使用为Sort方法提供此比较器：
```c#
studentList.Sort(new SortName());
```
* 上面的代码我们使用了一个已经不建议使用的集合类ArrayList。当泛型出来后，所有非泛型集合类已经建议不尽量使用了。进行了装箱和拆箱。而这是会影响性能的。如果我们的集合中有成千上万个复杂的实体对象，则在排序的时候所耗费掉的性能就是客观的。而泛型的出现，就可以避免掉拆箱和装箱。故上文代码中的ArrayList，应该换成List<T>，对应的，我们就该实现IComparable<T>和IComparer<T>。
```c#
class Student:IComparable<Student>
    {
        public string Name { get; set; }
        public int Age { get; set; }

        #region IComparable<Student> Members

        public int CompareTo(Student other)
        {
            return Age.CompareTo(other.Age);
        }

        #endregion
    }

    class SortName: IComparer<Student>
    {
        #region IComparer<Student> Members

        public int Compare(Student x, Student y)
        {
            return x.Name.CompareTo(y.Name);
        }
	}


```

# 六、 设计模式

**关注使用场景**

## 1. UML

泛化：最终代码中，泛化关系表现为**继承非抽象类**。用一条带空心箭头的直接表示

实现：最终代码中，实现关系表现为**继承抽象类**。用一条带空心箭头的虚线表示

依赖：用一套**带箭头的虚线**表示的。在最终代码中，依赖关系体现为类构造方法及类方法的传入参数，箭头的指向为调用关系。（A类是B类的函数形参、函数返回值、函数局部变量、静态成员函数）

聚合：用一条带空心菱形箭头的直线表示。A聚合到B上，或者说B由A组成。

组合：用一条带实心菱形箭头直线表示，如下图表示A组成B，或者B由A组成。组合关系是一种强依赖的特殊聚合关系，如果整体不存在了，则部分也不存在了。

关联：关联关系是用一条直线表示的。在最终代码中，关联对象通常是以成员变量的形式实现的。

![image-20230525105644759](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230525105644759.png)

![image-20230422113402097](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230422113402097.png)

## 2. 单例模式Singleton

单例模式(https://www.runoob.com/design-pattern/singleton-pattern.html)：某个类只创建一个对象。适用于某个全局使用的类，需要频繁的创建对象，为了防止对象的频繁创建、销毁浪费资源，令某个类只能产生一个对象。

**如何保证在多线程、反射、类序列化的情况下都只创建一个对象。**

线程安全：https://blog.csdn.net/xiaochenXIHUA/article/details/108508258

```c#
private static readonly object _Synchronized = new object();
//创建一个只读对象作为lock。一次只能有一个线程进入lock代码块，线程要排队。
```

```c#
using System;
using System.Collections.Generic;
using System.Text;
 
namespace Singleton
{
    //懒汉式单例
    public class SingletonLazy
    {
        //定义一个保存实例的静态变量，私有
        private static SingletonLazy_Instance;	
        //定义一个保证线程同步标识
        private static readonly object _Synchronized = new object();
        private SingletonLazy() { }
 		
        //可全局访问的创建实例的方法
        public static SingletonLazy GetInstance()
        {
            if (SingletonLazy_Instance==null)
            {
                lock (_Synchronized)
                {
                    //双重锁定
                    if (SingletonLazy_Instance==null)
                    {
                        _Instance = new SingletonLazy();
                    }
                }
            }
            return SingletonLazy_Instance;
        }
    }//Class_end
    
    //饿汉式
    public class SingletonHungry
    {
        private static SingletonHungry_instance = new SingletonLazy_Instance();
        private SingletonHungry(){}
        
        public static SingletonHungry GetInstance()
        {
            if(SinglgetonHungry_instance==null)
            {
                return SingletonLazy_Instance;
            }   
        }
    }
}
```

## 3. 简单工厂SimpleFactory

简单工厂模式专门定义一个类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。

![image-20230422114006051](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230422114006051.png)

## 4. 工厂方法模式FactoryMethod





## 5. 抽象工厂模式



## 6. 观察者模式

https://blog.csdn.net/qq_40666028/article/details/80916020

https://www.cnblogs.com/fengfuwanliu/p/10892940.html

https://zhuanlan.zhihu.com/p/346509500

### 前言

### 耦合的情况
```c#
class Subject
{
	private string args;	//被观察者的某个会变化的状态
	private List<Observer> observers;	//所有的观察者，与观察者耦合
	
	public void Attach(Observer ob)
	{
		observers.Add(ob);
	}
	
	public void Detach(Observer ob)
	{
		observers.Remove(ob);
	}
	
	public void notify()
	{ 
		foreach(var o in observers)
		{
			o.Update();
		}
	}	//args改变时，通知观察者
}

class Observer
{
	private Subject sub;	//需要访问被观察者的状态args，与被观察者耦合
	public Observer(Subject sub)
	{
		this.sub = sub;
	}
	public void Update()
	{
		Console.WriteLine(sub.args);
	}
}
```
> Subject需要在状态发生改变时通知所有观察者，所以依赖Observer。
> Observer需要对Subject的通知做出响应Update，需要访问Subject的参数，依赖Subject。
> 两个具体类耦合，双向依赖。
### 解耦合
首先依据依赖倒转，具体类不要依赖具体类，依赖抽象。里氏原则。
```c#
class abstract Subject
{
	protected string args;
	protected List<Observer> observers;
	public abstract void Attach(Observer ob);
	public abstract void Detach(Observer ob);
	public abstract void notify();
}

class ConcreteSubject:Subject
{
	public override void Attach(Observer ob)
	{
		observers.Add(ob);
	}
	public override void Detach(Observer ob)
	{
		observers.Remove(ob);
	}
	public override void notify()
	{
		foreach(var ii in observers)
		{
			ii.update();
		}
	}
}

class abstract Observer
{
	private Subject sub;
	public Observer(Subject sub)
	{	
		this.sub = sub;	
	}
	public abstract void Update();
}

class ConcreteObserver:Observer
{
	public override void Update()
	{
		Console.WriteLine(sub.args);
	}
}

```

![image](https://img2023.cnblogs.com/blog/2415825/202305/2415825-20230519003415800-1572595864.png)

　　定义对象之间的一种一对多依赖关系，使得每当一个对象状态发生改变时，其相关依赖对象皆得到通知并被自动更新。观察者模式的别名包括发布-订阅（Publish/Subscribe）模式、模型-视图（Model/View）模式、源-监听器（Source/Listener）模式或从属者（Dependents）模式。观察者模式是一种对象行为型模式。

Subject（抽象目标）：目标又称为主题，它是指被观察的对象。在目标中定义了一个观察者集合，一个观察目标可以接受任意数量的观察者来观察，它提供一系列方法来增加和删除观察者对象，同时它定义了通知方法notify()。目标类可以是接口，也可以是抽象类或具体类。

ConcreteSubject（具体目标）：具体目标是目标类的子类，通常它包含有经常发生改变的数据，当它的状态发生改变时，向它的各个观察者发出通知；同时它还实现了在目标类中定义的抽象业务逻辑方法（如果有的话）。如果无须扩展目标类，则具体目标类可以省略。

Observer（抽象观察者）：观察者将对观察目标的改变做出反应，观察者一般定义为接口，该接口声明了更新数据的方法update()，因此又称为抽象观察者。

ConcreteObserver（具体观察者）：在具体观察者中维护一个指向具体目标对象的引用，它存储具体观察者的有关状态，这些状态需要和具体目标的状态保持一致；它实现了在抽象观察者Observer中定义的update()方法。通常在实现时，可以调用具体目标类的attach()方法将自己添加到目标类的集合中或通过detach()方法将自己从目标类的集合中删除。

### EventHandler方式实现

上面的ConcreteSubject依赖Observer，因此维护了所有的观察者，通过遍历手动执行各个observer的Update()。但若Publisher不知道有多少Subscriber，或者最好**不要依赖抽象Observer**。再者，observer往往是一些被封装好的控件或内部类，不存在一个公共的抽象类Observer，去实现相同的Update。而且Update()也不会是同样的方法名。

C#的委托，可以通过多播方式自动执行所有在委托上注册过的函数。这样Publisher不必知道有多少Subscriber，Subscriber也不需要有一个抽象类。

C#内置事件委托`EventHandler<EventArgs>`通过多播执行注册在事件上的函数。

* 通过定义类继承EventArgs，封装参数，可将参数从publisher传递到observer。
* publisher触发事件的函数命名为:On_func，在客户端处将On_func注册到publisher的事件上。

```c#
public class Subjcet
{
    private string args;
    public event EventHandler<string> Update;
    public string Args{get=>args;set=>args=value;}
    
    public Subject(string args)
    {
        args = args;
    }
    
    public void notify();
}

public class Boss:Subject
{
    private string args;
    public event EventHandler<string> Update;
    public string Args{get=>args;set=>args=value;}
    
    public Subject(string args):base(args){}
    
    public void notify()
    {
        Update();
    }
}

class StockObserver
{
    private string name;
    private Subject sub;
    public StockObserver(string name, Subject sub)
    {
        this.name=name;
        this.sub=sub;
    }
    
    public void CloserMarker()
    {
        Console.WriteLine($"args={sub.args}");
    }
}

class NBAbserver
{
    private string name;
    private Subject sub;
    public StockObserver(string name, Subject sub)
    {
        this.name=name;
        this.sub=sub;
    }
    
    public void CloserWatchNBA()
    {
        Console.WriteLine($"args={sub.args}");
    }
}

//Main:
Boss boss = new Boss();
StockObserver tongshi1 = new StockObserver("zhang", boss);
StockObserver tongshi2 = new StockObserver("wang", boss);

boss.Update += new EventHandler(tongshi1.CloserMarker);
boss.Update += new EventHandler(tongshi2.CloserWatchNBA);

boss.args = "回来了";	//boss的状态发生改变
boss.notify();	//boss触发subscriber的响应
```

### 函数命名规则

事件event EventHandler：XXXXEventHandler

Publisher触发事件的函数：OnXXXXEventHandler()

### 异步执行多播

将多个方法分别绑定委托，以list存储。分别BeginInvoke异步执行。

```c#
using System;
using System.Collections.Generic;
using System.Text;

namespace 观察者模式
{
    public delegate void SpeakDelegate(string speak);

    class Child
    {

        public List<SpeakDelegate> SpeakHandleList = new List<SpeakDelegate>();

        public void Speak(string speak)
        {
            foreach (SpeakDelegate SpeakHandle in this.SpeakHandleList)
            {
                if (SpeakHandle != null)
                    SpeakHandle.BeginInvoke(speak, FinishListion, null);	//speak为参数，FinishListion为回调，null为传入回调的参数
            }
        }

        private void FinishListion(IAsyncResult result)
        {
           
        }

    }
}

```

## 适配器模式Adapter

## 外观模式Facade

## 包装模式Decorator



# 七、三层架构

UIL、BLL、DAL、Model

https://www.jianshu.com/p/7f628015a243

# 八、数据库

## 1. ORM-EntityFramework

https://www.dbs724.com/146176.html

.net core：https://blog.csdn.net/DREAM5555/article/details/115345172

### EF6操作SQLserver



### EF6操作MySQL



## 2. 简单工厂+反射+配置文件

# 九、 Socket

https://www.cnblogs.com/zhili/archive/2012/09/23/QQ_P2P.html

## 1. 基本TCP流程

### （1）Server

* 创建一个监听服务器端口的Socket对象，指明协议，IP，端口：

  ```c#
  Socket ss = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
  //AddressFamily.InterNetwork——IPv4
  //SocketType.Stream——可靠双向字节流
  //ProtocolType.Tcp——TCP
  serverSocket.Bind(new IPEndPoint(this.bindIP, bindPort));   //IPEndPoint是IP:port
  ```


* 监听本机端口：

  ```c#
  serverSocket.Listen(listenRequests);	
  //listenRequests——最多允许同时连接的Client数
  ```

* 创建一个与客户端建立连接后，用于收发消息的socket

  ```c#
  clientConnectSocket = serverSocket.Accept();    //Accept()建立连接并返回用于通信的socket
  ```

* 向client发送消息

  ```c#
  clientConnectSocket.send(byte[] buffer)
  ```

* 接收消息

  ```c#
  byte[] receiveBuff;
  int bytes = clientConnectSocket.Receive(receiveBuff, receiveBuff.Length, 0);
  ```

* 断开

  ```c#
  serverSocket.Close();
  clientConnectSocket.Close();
  ```

### （2）Client

* 创建用于和服务器进行传递数据的Socket，其中IP是本机IP，port是系统自动分配。
* 向server发送消息
* 从server接收消息

## 2. 常用API

### （1）头文件

System.Net

System.Net.Sockets

### （2）DNS解析

#### IPHostEntry——主机信息

服务器主机信息的容器，将服务器的URI、IP地址列表（一个URI可能对应好几台服务器，因此也就有好几个IP）、一组别名相关联并封装。

```C#
IPHostEntry hostInfo = Dns.GetHostEntry("www.bing.com");	//将uri "www.bing.com"经过DNS解析，返回存储着主机信息的	//IPHostEntry实例
```

> * IPHostEntry.AddressList——获取/设置主机的IP地址列表
>
>   ```C#
>   public IPAddress[] AddressList	{get; set;}
>   ```
>
>     ```c#
>   // Get 'IPHostEntry' object containing information like host name, IP addresses, aliases for a host.
>           IPHostEntry hostInfo = Dns.GetHostByName(hostString);
>           Console.WriteLine("Host name : " + hostInfo.HostName);
>           Console.WriteLine("IP address List : ");
>           for(int index=0; index < hostInfo.AddressList.Length; index++)
>           {
>               Console.WriteLine(hostInfo.AddressList[index]);
>           }
>     ```
>
> * HostName——获取主机的DNS名称/URI
>
>   ```C#
>    public string HostName { get; set; }
>   ```
>
> * Aliases——获取主机别名
>
>   ```c#
>    public string[] Aliases { get; set; }
>   ```

### IPAddress——IP

获取服务器的IP地址相关信息。

Tostring()转化为点分十进制形式的IP。

> * Address——十进制形式IP（已过时）
>
> * AddressFamily——IP地址使用的地址族

### Dns

该类提供简单的DNS解析功能。

> * Dns.GetHostEntry(string)——将**主机名或 IP 地址**解析为IPHostEntry实例。（Resolve已过时）
>
> * Dns.GetHostName()——获取本地计算机主机名
>
> * Dns.GetHostAddresses(string)——返回指定主机的 Internet 协议 (IP) 地址
>
>   ```c#
>   public static System.Net.IPAddress[] GetHostAddresses (string hostNameOrAddress);
>   ```
>
> * BeginGetHostEntry(String, AsyncCallback, Object)——将主机名或 IP 地址**异步**解析为IPHostEntry实例
>
>   ```c#
>   public static IAsyncResult BeginGetHostEntry (string hostNameOrAddress, AsyncCallback? requestCallback, object? stateObject);
>   ```

#### demo

```c#
 private void button_addrEx_Click(object sender, EventArgs e)
        {
            IPHostEntry host = Dns.GetHostEntry(textBox_uri.Text);
            //foreach(IPAddress addr in host.AddressList)
            //{
            //    long ip = addr.Address;
            //    textBox_ip.Text += ip.ToString() + "\r\n";
            //    textBox_ip.Text += addr.ToString() + "\r\n";
            //}
            textBox_host.Text = host.HostName;
            IPAddress[] ips= Dns.GetHostAddresses(textBox_uri.Text);
            foreach(IPAddress addr in ips)
            {
                long ip = addr.Address;
                textBox_ip.Text += ip.ToString() + "\r\n";	//Windows中换行符是\r\n
                textBox_ip.Text += addr.ToString() + "\r\n";
            }
        }
```

### （3）TCP

#### NetworkStream类——用于网络访问的基础流

https://learn.microsoft.com/zh-cn/dotnet/api/system.net.sockets.networkstream?view=net-7.0

 **NetworkStream**实现了通过网络套接字发送和接收数据的标准，并且NetworkStream支持网络流的的同步和异步访问。

```c#
/*构造*/
public NetworkStream (Socket socket);

//设置为 true 可指示 NetworkStream 将拥有 Socket；否则为 false。
public NetworkStream (Socket socket, bool ownsSocket);
```

#### 方法

> * Read——从NetworkStream读取数据，并将其存储到字节数组中
>
>   ```C# 
>   public override int Read (byte[] buffer, int offset, int size);
>   ```
>
>   > - buffer——Byte类型的数组，它是内存中用于存储从NetworkStream读取的数据的位置。
>   >
>   > - offset——buffer 中数据存储的起始位置
>   >
>   > - size——要从中读取的字节数
>   > - 返回值——从NetworkStream中实际读取的字节数
>
> * Write——从字节数组的指定范围向NetworkStream写入数据
>
>   ```C#
>   public override void Write (byte[] buffer, int offset, int size);
>   ```
>
>   > * buffer——存储要写入流的数据
>   > * offset——buffer要写入数据的起始位置
>   > * size——buffer要写入字节长度
>
> * ReadByte——从NetworkStream中读取一个字节，并将流内的位置向前提升一个字节，或者如果已到达流结尾，则返回 -1
>
>   ```C#
>   public override int ReadByte ();
>   ```
>
> * WriteByte——将一个字节写入 NetworkStream内的当前位置，并将流内的位置向前提升一个字节
>
>   ```C#
>   public override void WriteByte (byte value);	//value要写入流当前位置的一个字节
>   ```

> * Length
> * Socket

#### TcpClient类——提供客户端连接

```C#
public TcpClient (string hostname, int port);	//初始化 TcpClient 类的新实例并连接到指定主机上的指定端口。
```

> - hostname——要连接到的远程主机的 DNS 名。
>
> - port——要连接到的远程主机的端口号。

```C#
//Creates a TCPClient using host name and port.
TcpClient tcpClientB = new TcpClient ("www.contoso.com", 11000);
```

>* Connect——客户端连接到指定IP和端口的远程主机
>
>  ```C#
>  public void Connect (System.Net.IPAddress address, int port);
>  ```
>
>> IPAddress——你打算连接到的主机的 IPAddress。
>> port——要连接到的端口号。
>
>```C#
>public void Connect (System.Net.IPAddress[] ipAddresses, int port);
>```
>
>> ipAddresses——要连接到的主机的 IPAddress 数组。
>> port——要连接到的端口号。
>
>* Close——释放TCPClient实例，不关闭TCP连接
>
>  ```c#
>  public void Close ();
>  ```

```c#
  TcpClient tcpClient = new TcpClient ();
  // Uses the GetStream public method to return the NetworkStream.
  NetworkStream netStream = tcpClient.GetStream ();
 if (netStream.CanWrite)
  {
      Byte[] sendBytes = Encoding.UTF8.GetBytes ("Is anybody there?");
      netStream.Write (sendBytes, 0, sendBytes.Length);
  }
  else
  {
      Console.WriteLine ("You cannot write data to this stream.");
      tcpClient.Close ();
      // Closing the tcpClient instance does not close the network stream.
      netStream.Close ();
      return;
  }

if (netStream.CanRead)
  {
      // Reads NetworkStream into a byte buffer.
      byte[] bytes = new byte[tcpClient.ReceiveBufferSize];
    
     // Read can return anything from 0 to numBytesToRead.
      // This method blocks until at least one byte is read.
      netStream.Read (bytes, 0, (int)tcpClient.ReceiveBufferSize);

      // Returns the data received from the host to the console.
      string returndata = Encoding.UTF8.GetString (bytes);
	  Console.WriteLine ("This is what the host returned to you: " + returndata);
  }
  else
  {
      Console.WriteLine ("You cannot read data from this stream.");
      tcpClient.Close ();
       // Closing the tcpClient instance does not close the network stream.
      netStream.Close ();
      return;
  }
  netStream.Close();
```

```c#
static void Connect(String server, String message)
{
  try
  {
    // Create a TcpClient.
    // Note, for this client to work you need to have a TcpServer
    // connected to the same address as specified by the server, port
    // combination.
    Int32 port = 13000;
    TcpClient client = new TcpClient(server, port);

    // Translate the passed message into ASCII and store it as a Byte array.
    Byte[] data = System.Text.Encoding.ASCII.GetBytes(message);

    // Get a client stream for reading and writing.
   //  Stream stream = client.GetStream();

    NetworkStream stream = client.GetStream();

    // Send the message to the connected TcpServer.
    stream.Write(data, 0, data.Length);

    Console.WriteLine("Sent: {0}", message);

    // Receive the TcpServer.response.

    // Buffer to store the response bytes.
    data = new Byte[256];

    // String to store the response ASCII representation.
    String responseData = String.Empty;

    // Read the first batch of the TcpServer response bytes.
    Int32 bytes = stream.Read(data, 0, data.Length);	//bytes是从stream中实际读取的字节数
    responseData = System.Text.Encoding.ASCII.GetString(data, 0, bytes);
    Console.WriteLine("Received: {0}", responseData);

    // Close everything.
    stream.Close();
    client.Close();
  }
  catch (ArgumentNullException e)
  {
    Console.WriteLine("ArgumentNullException: {0}", e);
  }
  catch (SocketException e)
  {
    Console.WriteLine("SocketException: {0}", e);
  }

  Console.WriteLine("\n Press Enter to continue...");
  Console.Read();
}
```

### （4）TCPListener类——服务器端监听

server在指定的IP和端口上监听客户端的连接

```c#
public TcpListener (System.Net.IPAddress localaddr, int port);
```

> localaddr——本地IP
>
> port——监听的端口

```c#
TcpListener server=null;
Int32 port = 13000;
IPAddress localAddr = IPAddress.Parse("127.0.0.1");
server = new TcpListener(localAddr, port);
```

> * TCPListener.Start——服务器端启动监听客户端的连接请求
>
>   ```c#
>   public void Start ();
>   ```
>
> * TCPListener.Stop——关闭监听
>
>   ```C#
>   public void Stop ();
>   ```
>
> * TCPListener.AcceptSocket——接受客户端的连接请求，并返回用于收发数据的Socket
>
>   需要先Start监听
>
>   ```C#
>   public System.Net.Sockets.Socket AcceptSocket ();	
>   ```
>
>   ```C#
>   // Accepts the pending client connection and returns a socket for communciation.
>              Socket socket = tcpListener.AcceptSocket();
>              Console.WriteLine("Connection accepted.");
>   
>              string responseString = "You have successfully connected to me";
>   
>              //Forms and sends a response string to the connected client.
>              Byte[] sendBytes = Encoding.ASCII.GetBytes(responseString);
>              int i = socket.Send(sendBytes);
>              Console.WriteLine("Message Sent /> : " + responseString);
>   ```
>
> * TCPListener.AccecptTcpClient——接受挂起的客户端连接请求
>
>   ```C#
>   public System.Net.Sockets.TcpClient AcceptTcpClient ();
>   ```
>
>   > 返回值——TcpClient 

```c#
using System;
using System.IO;
using System.Net;
using System.Net.Sockets;
using System.Text;

class MyTcpListener
{
  public static void Main()
  {
    TcpListener server=null;
    try
    {
      // Set the TcpListener on port 13000.
      Int32 port = 13000;
      IPAddress localAddr = IPAddress.Parse("127.0.0.1");

      // TcpListener server = new TcpListener(port);
      server = new TcpListener(localAddr, port);

      // Start listening for client requests.
      server.Start();

      // Buffer for reading data
      Byte[] bytes = new Byte[256];
      String data = null;

      // Enter the listening loop.
      //循环接收客户端的连接请求
      while(true)
      {
        Console.Write("Waiting for a connection... ");

        // Perform a blocking call to accept requests.
        // You could also use server.AcceptSocket() here.	也可使用server.AcceptSocket()
        TcpClient client = server.AcceptTcpClient();	//阻塞，等待客户端连接
        Console.WriteLine("Connected!");

        data = null;

        // Get a stream object for reading and writing
        NetworkStream stream = client.GetStream();

        int i;

        // Loop to receive all the data sent by the client.
        while((i = stream.Read(bytes, 0, bytes.Length))!=0)
        {
          // Translate data bytes to a ASCII string.
          data = System.Text.Encoding.ASCII.GetString(bytes, 0, i);
          Console.WriteLine("Received: {0}", data);	//显示从服务器接受的数据

          // Process the data sent by the client.
          data = data.ToUpper();

          byte[] msg = System.Text.Encoding.ASCII.GetBytes(data);

          // Send back a response.
          stream.Write(msg, 0, msg.Length);	
          Console.WriteLine("Sent: {0}", data);	//将从服务器接收的字符串，变为大写，发送回服务器
        }

        // Shutdown and end connection
        client.Close();
      }
    }
    catch(SocketException e)
    {
      Console.WriteLine("SocketException: {0}", e);
    }
    finally
    {
       // Stop listening for new clients.
       server.Stop();
    }

    Console.WriteLine("\nHit enter to continue...");
    Console.Read();
  }
}
```

### （5）Socket类

不同主机间的进程进行互相通信的端点。通信时至少需要一对socket，一个在客户端一个在服务端，一个Socket类实例包含了客户端端点或服务器端点的socket信息

```c#
public Socket (AddressFamily addressFamily, SocketType socketType, ProtocolType protocolType);
```

```c#
  //Creates the Socket for sending data over TCP.
  Socket s = new Socket(AddressFamily.InterNetwork, SocketType.Stream,
     ProtocolType.Tcp );

  // Connects to host using IPEndPoint.
  s.Connect(EPhost);
  if (!s.Connected)
  {
     strRetPage = "Unable to connect to host";
  }
  // Use the SelectWrite enumeration to obtain Socket status.
   if(s.Poll(-1, SelectMode.SelectWrite)){
        Console.WriteLine("This Socket is writable.");
   }
   else if (s.Poll(-1, SelectMode.SelectRead)){
         Console.WriteLine("This Socket is readable." );
   }
   else if (s.Poll(-1, SelectMode.SelectError)){
        Console.WriteLine("This Socket has an error.");
   }
```

### （6）HTTP

#### WebClient

提供将数据发送到指定Uri和从指定Uri获取数据的方法

```c#
WebClient client = new WebClient();
Stream data = client.OpenRead(textBoxUri.Text);
StreamReader reader = new StreamReader(data);
textBox_content.Text = reader.ReadtoEnd();
reader.close();
data.close();
```

#### WebRequest



#### WebResponse



#### 浏览器控件

![image-20230805112345299](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230805112345299.png)

```c#
private void button1_Click(object sender, EventArgs e)
 {
 	webBrowser1.Navigate(textBox_searchUri.Text, false);
 }
```



## 3. 异步Accept、Receive







## 4. 序列化和反序列化

https://www.cnblogs.com/amylis_chen/p/11578598.html
https://www.cnblogs.com/1549983239yifeng/p/14789106.html
https://www.cnblogs.com/xueyubao/p/11262320.html
https://blog.csdn.net/qq_37179591/article/details/105736690
https://www.cnblogs.com/guogangj/p/7489218.html
![](https://img2023.cnblogs.com/blog/2415825/202306/2415825-20230629003317582-1631410764.png)

### （1）二进制序列化

#### 设置某些字段、属性不二进制序列化

添加Attribute：[System.NonSerialized]

#### 无法找到程序集“Client, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null”

![image](https://img2023.cnblogs.com/blog/2415825/202306/2415825-20230629124228334-1113851261.png)
类分处于服务器命名空间下和客户端命名空间下，命名空间的不一致也就是类的不一致导致了对象在反序列化时出现错误。

##### 解决1——SerializationBinder

[SerializationBinder类](https://learn.microsoft.com/zh-cn/dotnet/api/system.runtime.serialization.serializationbinder?view=net-7.0&redirectedfrom=MSDN)

```c#
public class UBinder : SerializationBinder
    {
        public override Type BindToType(string assemblyName, string typeName)
        {
            // 从E:\TibetGlobal.dll读取
            var path = Path.Combine("E:\\", "TibetGlobal.dll");
            var assembly = Assembly.LoadFrom(globalPath);
            return assembly.GetType(typeName);
            }
        }
    }

 public class UBinder : SerializationBinder
        {
                public override Type BindToType(string assemblyName, string typeName)
                {
                        Assembly ass = Assembly.GetExecutingAssembly();
                        return ass.GetType(typeName);
                }
       }
————————————————
版权声明：本文为CSDN博主「yunzhonghefei1」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yunzhonghefei1/article/details/106528366


var formatter = new BinaryFormatter
{
    Binder = new UBinder()
};
var obj = formatter.Deserialize(pipeServer);
```

##### 解决2——DLL



### （2）XML序列化

#### 无法XML序列化的情况

常见的两种模式，Binary和XML序列化，Binary二进制序列化没有什么限制，但是XML序列化有限制：


    （1）需序列化的字段必须是公共的(public)
    （2）需要序列化的类都必须有一个无参的构造函数
    （3）枚举变量可序列化为字符串，无需用[XmlInclude]
    （4）**序列化非基本类型对象，必须在对象前用[XmlInclude]将可能需要序列化的自定义复杂类型进行事先声明。**该规则递归作用到子元素。
        如导出ArrayList对象，若其成员是自定义的，需预包含处理：
        ```c#
          using System.Xml.Serialization;
          [XmlInclude(typeof(自定义类))]
          [Serilizable]
        ```
    （5）Attribute中的IsNullable参数若等于false，表示若元素为null则不显示该元素。
        也就是说：针对值类型（如结构体）该功能是实效的
        若数组包含了100个空间，填充了10个类对象，则序列化后只显示10个节点
        若数组包含了100个空间，填充了10个结构体对象，则序列化后会显示100个节点
    （6）真正无法XML序列化的情况
        某些类就是无法XML序列化的（即使使用了[XmlInclude]）
            IDictionary（如HashTable）
            System.Drawing.Color
            System.Drawing.Font
            SecurityAttribute声明
        父类对象赋予子类对象值的情况
        对象间循环引用
    （7）对于无法XML序列化的对象，可考虑
        使用自定义xml序列化（实现IXmlSerializable接口）
        实现IDictionary的类，可考虑（1）用其它集合类替代；（2）用类封装之，并提供Add和this函数
        某些类型需要先经过转换，然后才能序列化为 XML。如XML序列化System.Drawing.Color，可先用ToArgb()将其转换为整数 
        过于复杂的对象用xml序列化不便的话，可考虑用二进制序列化


在很多情况下，二进制序列化对对象的序列化信息更加完整、准确。

#### xml设置某些字段、属性不序列化

对public属性进行设置：
[System.Xml.Serialization.XmlIgnore]

#### XML序列化常用注解Attribute



### （3）DataTable的序列化



## 5. XML



## 6. JSON

# 十、日志



# 十一、串口通信

RS232

RS485/422

# 十二、C#新特性

## 1. dynamic



 
