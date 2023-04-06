lambda表达式和匿名方法、字段和属性、委托和事件、多线程、vs使用高级

迭代器：ienumerable

Linq：from/where/select

# 一、基础

## 0. .Net和C#

.Net是一个平台，一般指.Net Framework框架。C#是编程语言，可以在.Net平台下开发。

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

如上面所说的,浅拷贝对于值类型则复制值,对于引用类型则复制对象的引用(类似于指针).深拷贝则是完完全全的创建一个个新对象.对原来数组中的所有对象全部创建新对象.对新数组中的修改不会影响原来数组中的值或对象.但是如何实现深拷贝呢?.NET库中似乎没有深拷贝的方法.这和实现深拷贝的原理有关系.若用户希望实现深拷贝.希望出现两个完全一样但又互不影响的数组.则必须自己写方法,对原数组中的每个对象实现拷贝,层层深入,直到这个对象中的对象中的对象……中的对象为值类型为止,因为只有值类型才是完全拷贝,对一个值进行修改不会影响另一个相同的值.这么说又有点难理解了.实现深拷贝的方法,如下图:

https://www.cnblogs.com/JamelAr/p/14156387.html





## 5. 异常

### （1）try catch finally

https://blog.csdn.net/ananlele_/article/details/109056883

* 异常

  所有异常的父类是System.Exception。.NET框架内置了常见的异常，。像DivideByZeroExcpetion等异常类是Exception的子类（派生类）。

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
  State st = Enum.Parse(Typeof(State), a);
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
  public static MyTryParse(string str, out int res){
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

### （3）params——变长数组

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

类型推断，仅队函数内部的**局部变量**起作用。

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

还有个用法是子类屏蔽父类同名成员。

## 14. 重载overload

方法名相同，但函数签名不同。（方法名、形参个数、形参类型，返回值不是签名的一部分）

## 15. virtual和abstract

父类中声明为virtual的方法，可以被子类override。若不想父类的某个函数被override，将其修饰为final。

LSP原则：不要override父类的非抽象方法。

```c#
class ff{
    virtual public void func(){}
}

class ss:ff{
    override public void func(){}
}
```

父类的方法声明为abstract为抽象方法，父类即抽象类，抽象类不可被实例化，只能作为父类被继承，子类override实现抽象方法。

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

析构方法的调用时机和GC一样是不可预测什么时候调用的，因此也不应当依赖于它被调用。析构函数是确保已分配的非托管资源总能被释放的一个补救措施。如果可能就不应当被调用，譬如说手动释放了非托管资源，此时应当通知GC取消对对象的析构函数的调用。

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

  继承是侵入性的。只要继承，就必须拥有父类的***所有***属性和方法。

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
  cc.sayHello();	//Coder。而不是Person。
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

委托是具有相同签名、返回值的有序函数列表，是引用类型。

方法列表：绑定到委托上的方法。委托会依次调用方法列表中的每个方法。

判定方法列表是否为空：将委托变量和null比较。

初始化委托时，会将第一个方法放入方法列表：MyDel md = func;

通过等号赋值绑定到方法列表的方法只能是第一个方法，后面的绑定需要使用+=；若再次使用赋值给委托变量，就会产生新的委托变量，原来的委托变量将会被GC回收。

给委托添加方法使用+=，移出方法使用-=。

绑定匿名方法使用lambda表达式，可在委托里内联一小段函数。

```c#
public void delegate dlg(int a);
dlg+=a=>return a++;
```

委托在编译的时候确实会编译成类。因为Delegate是一个类，所以在任何可以声明类的地方都可以声明委托。

 **委托是一个类，它定义了方法的类型，使得可以将方法当作另一个方法的参数来进行传递，这种将方法动态地赋给参数的做法，可以避免在程序中大量使用If-Else(Switch)语句，同时使得程序具有更好的可扩展性。（https://www.cnblogs.com/neo98/articles/4916163.html）**

为什么要引入委托：https://www.cnblogs.com/zhili/archive/2012/10/22/Delegate.html



## 24. 内置泛型委托Action和Func

内置泛型委托类型。

Action对应的函数签名没有返回值，Func对应的函数签名有返回值。

```c#
Action
Action<T>
Action<T1,T2>
Action<T1,T2,T3>
Action<T1,T2,T3,T4>
```

```c#
Func<TResult>
Func<T,TResult>
Func<T1,T2,TResult>
Func<T1,T2,T3,TResult>
Func<T1,T2,T3,T4,TResult>
```

```c#
Func<int a, double b, string str> dlg+=(int a, double b)=>return (a+b).toString();
```



## 25. 事件

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

# 三、其他

## 29. 多线程、异步委托

线程是轻量的进程，C#程序运行时第一个线程自动创建，称为**主线程**。

线程生命周期开始于Thread对象创建时，状态有：未启动、就绪、休眠、死亡。

何时使用多线程：
1、提高CPU的利用率，从而提高了程序的效率；
2、当程序运行会卡住软件界面，为了使人不用焦虑等待时，采取线程与委托来处理，从而使软件界面运行流畅；
3、处理大量数据时间较长（显示一个进度条给界面）或不需要马上得到结果反馈给软件界面时；

### （1）Thread.Start开辟线程

https://blog.csdn.net/weixin_34364071/article/details/85998726?spm=1035.2023.3001.6557&utm_medium=distribute.pc_relevant_bbs_down_v2.none-task-blog-2~default~ESQUERY~Rate-2-85998726-bbs-392024152.pc_relevant_bbs_down_v2_default&depth_1-utm_source=distribute.pc_relevant_bbs_down_v2.none-task-blog-2~default~ESQUERY~Rate-2-85998726-bbs-392024152.pc_relevant_bbs_down_v2_default

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

#### —如何优雅的终止线程：

Thread.Abort是在调用线程内抛出一个ThreadAbortException从而导致线程终止。但是只有目标线程在调用**托管代码**时Abort()才会立即返回终止线程，若正在调用非托管代码（如外部C++代码）则不会立即返回。通过Thread.ThreadState获取状态可知，调用Abort抛出异常后，状态为ThreadState.AbortRequested，真正终止后状态为ThreadState.Aborted。

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

### （2）异步委托开辟线程

为了降低创建、销毁线程的成本，CLR为每一个进程维护了一个线程池。开始，线程池是空的，如果进程使用的线程被创建并执行完毕后，不会被销毁而会被放入线程池。之后，进程需要一个线程，就会从线程池中还原一个线程，节省很多时间。

UI主线程要能及时相应用户操作，若程序后台需要执行很长时间就需要放到另一个线程。

**异步执行的三种方式：阻塞主线程等待子线程、主线程轮询子线程执行状态、子线程执行完调用回调函数。**

* AsyncResult对象和IAsyncResult接口对象

  ![image-20230331123955719](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230331123955719.png)

* BeginInvoke和EndInvoke

  ```c#
  void func_callback(IAsyncResult iar){...}
  IAsyncResult BeginInvoke(1, 2, new AsyncCallback(func_callback), null)
  ```

  ```c#
  T result = dlg.EndInvoke(out p, IAsyncResult iar);
  ```

* 轮询

  ```c#
  ```

  

https://www.cnblogs.com/zhili/archive/2013/05/15/Csharp5asyncandawait.html

https://learn.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/calling-synchronous-methods-asynchronously

https://learn.microsoft.com/zh-cn/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-using-delegates



### （3）Thread.Start和BeginInvoke开辟的线程有什么区别？

我们知道线程池有工作线程和IO线程，你写的多线程和并发只能操控到工作线程，而异步恰恰可以把线程池里的IO线程调动起来处理关于一切涉及IO方面的工作，取网络IO，文件IO



thread开启的线程和begininvoke有什么区别？https://www.cnblogs.com/zjoch/archive/2012/04/12/2443714.html

Control.Invoke 方法 (Delegate) :在拥有此控件的基础窗口句柄的线程上执行指定的委托。
Control.BeginInvoke 方法 (Delegate) :在创建控件的基础句柄所在线程上异步执行指定委托。
Control的Invoke和BeginInvoke的参数为delegate，委托的方法是在Control的线程上执行的，也就是我们平时所说的UI线程。

### （4）锁



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

## 31. typeof/GetType()

```c#
Type t = typeof(myClass);
FieldsInfo[] fi =t.GetFields();
MethodsInfo[] mi = t.GetMethods();
```

## 32. using语句

using statement分配资源，using语句会将statement隐式的放入try catch语句中，并在finally中加一个Dispose释放资源。所以使用using语句可以自动释放使用的资源。

## 33. 装箱拆箱

装箱：将值类型转成引用类型。

拆箱：将引用类型转成值类型。

## 34. 泛型（generic）

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

## 35. 定时器





## 36. 反射（reflection）与特性（attribute）

https://blog.csdn.net/ananlele_/article/details/107979117



## 37. 索引器（indexer）

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

# 四、数据结构

集合分为泛型集合和非泛型集合。

前者位于System.Collection.Generic命名空间，后者位于System.Collection。

### （1）泛型集合和非泛型集合

非泛型集合内部存储的是Object类型：ArrayList，Stack，Queue，HashTable

泛型集合：`List<T>、Stack<T>、Queue<T>、Dictionary<TKey,TValue>`

# 五、LinQ



# 六、 设计模式

**关注使用场景**

### （1）单例模式

单例模式(https://www.runoob.com/design-pattern/singleton-pattern.html)：某个类只创建一个对象。适用于某个全局使用的类，需要频繁的创建对象，为了防止对象的频繁创建、销毁浪费资源，令某个类只能产生一个对象。

线程安全：https://blog.csdn.net/xiaochenXIHUA/article/details/108508258

```c#
private static readonly object _Synchronized = new object();
//创建一个只读对象作为lock
```

```c#
using System;
using System.Collections.Generic;
using System.Text;
 
namespace SingleModel
{
    //懒汉式单例
    public class Single3
    {
        //定义一个保存实例的静态变量
        private static Single3 _Instance;
        //定义一个保证线程同步标识
        private static readonly object _Synchronized = new object();
        private Single3() { }
 
        public static Single3 GeInstance()
        {
            if (_Instance==null)
            {
                lock (_Synchronized)
                {
                    //双重锁定
                    if (_Instance==null)
                    {
                        _Instance = new Single3();
                    }
                }
            }
            return _Instance;
        }
 
    }//Class_end
}
```

### （2）工厂模式

工厂模式：当若干子类实现了同一个接口，明确希望在不同条件下产生不同的子类对象，并用同一个接口对象去“接”。简化对象创建和去接的接口的选择。

### （3）观察者模式

观察者模式：当publisher状态发生改变时，所有的subscriber要能够被通知并做出反应。

# 七、EntityFramework

https://zhuanlan.zhihu.com/p/161107452

# 八、三层架构

https://www.jianshu.com/p/7f628015a243

# 九、 Socket

https://www.cnblogs.com/zhili/archive/2012/09/23/QQ_P2P.html

向MES上传数据

# 十、串口通信

RS232

RS485/422





