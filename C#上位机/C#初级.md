lambda表达式和匿名方法、字段和属性、委托和事件、多线程、vs使用高级

ienumerable

from/where/select

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

### （1）double和decimal

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

#### ——自动转换（隐式转换）

**没有精度丢失。**低精度转高精度。如：int转double。

```c#
int a = 1;
double b = a;	//将低精度转高精度
```

#### ——强制转换（显式转换）

**发生了精度丢失。**高精度转低精度，如：double转int。

```C#
double a = 1.1;
int b = (int)a;
```

#### ——int类型加减乘除还是int。表达式中只要有double结果就是double

```c#
int a =10;
int b = 3;
double c = a / b;	//c=3
double d = a*1.0 / b;	//d=3.0		将int提升为double类型方法：乘以1.0
```

#### ——Convert类型转换

类型不兼容时，用Convert转换。

```c#
int Convert.toInt32(object value);
```

```c#
string str = "123";
double a = Convert.toDouble(str);
int b = Convert.toInt32(str);
```

#### ——Parse()和TryParse()

```c#
int int.Parse(string);
bool int.TryParse(string, out int);
```

#### ——所有类型都可以转成string

```c#
.ToString()
```

## 4. 异常

### （1）try catch finally

https://blog.csdn.net/ananlele_/article/details/109056883

* 异常

  .NET框架内置了常见的异常，所有异常的父类是Exception。像DivideByZeroExcpetion等异常类是Exception的子类（派生类）。

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

  执行顺序：先try，若未发生异常，则执行finally；若发生了异常，则执行catch，再执行finally。

* try中若有return，则会先执行return，后执行finally。

* try后必须跟catch或finally中的至少一个。

  可以跟多个catch，用来捕获不同的异常进行不同的处理。但是需要catch更专业的错误放在前面。

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

  可以在catch代码块中将异常向更高的上下文重新抛出，由更高的上下文进行处理。

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
              throw new CustomException("RAJESH");
          }
          catch (Exception e)				//多态
          {
              Console.WriteLine("此处捕获异常" + e.ToString());
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

* readonly

  ```c#
  ref readonly int aConstant; // aConstant can't be value-reassigned.
  readonly ref int Storage; // Storage can't be ref-reassigned.
  readonly ref readonly int CantChange; // CantChange can't be value-reassigned or ref-reassigned.
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

  string、自定义类

* 值类型

  int、double、float、decimal、enum、struct

### （2）存储位置不同

* 值类型：在栈上直接存变量值

* 引用类型：变量值存在堆上，在栈上也会开辟空间，存的是堆上的地址

  ```c#
  string a = "1234";
  // "1234"存在堆上，栈上存的地址
  ```

### （3）传递方式不同



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

#### —s.Remove、s.Insert、s.ToCharArray

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

#### —StringBuilder-对字符串进行大量的拼接、赋值操作

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

#### —s.Length

#### —s.ToUpper、s.ToLower

大小写转换

#### —s.Equals

```c#
s.Equals(string value, StringComparision comparisonType)
// StringComparision是枚举类型，指定比较方式。
//StringComparision.OridinalIgnoreCase为忽略大小写比较
```

#### —Split

返回值是string数组

```c#
string[] Split(params char[] chs, StringSplitOptions.RemoveEmptyEntries)
//以ch字符数组中的字符为分割符
//StringSplitOptions是枚举类型。RemoveEmptyEntries表示分割后返回的string数组中不包括空字符串""。
//None表示不去除空字符串""。这样出现chs中字符的地方将会是""    
```

#### —Replace

将字符串中的oldvalue替换为newvalue

```c#
string Replace(string oldValue, string newValue)
```

#### —Substring

取子串

```c#
string Substring(int startIndex, int len)
```

#### ——Contains

```c#
bool Contains(string val);
```

#### ——IndexOf

返回子串第一次出现的index

```c#
int IndexOf(string value, int startIndex)
```

#### ——LastIndexOf

返回子串最后一次出现的index

#### ——StartsWith

判断是否以子串value开始

```c#
bool StartsWith(string value)
```

#### ——EndsWith

判断是否以子串value结束

```c#
bool EndsWith(string value)
```

#### ——str.Trim

去掉字符串左侧和右侧的空字符

```c#
str.Trim()
str.TrimStart()
str.TrimEnd()
```

#### ——string.IsNullOrEmpty()

判断字符串是否为空

```C#
bool string.IsNullOrEmpty(string str)
```

#### ——Join

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

属性控制属性的读写权限。

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

```C#
public static class Global{
    public static int a;
    public static void deal(){}
}
```

* 只能有静态成员

## 11.var

类型推断，仅队函数内部的**局部变量**起作用。

初始化时必须赋初值。

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

* try catch final

  指捕获到异常后一定会执行的代码块。

* 用于修饰变量、字段、函数、类

  用于变量时，该变量只能赋值一次，不可修改；

  用于方法时，该方法不能被重写（就是参数列表不变。重载是参数列表不同。）或隐藏；

  用于类时，该类不能被继承。



# 二、OOP

## 12. 构造函数

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

## 13. this

在类中代指当前类的对象。

在类中显式调用**全参构造函数。**

```c
public Student(int age, string name):this(age, name, "male")	//this会显示调用全参构造，减少冗余代码
```

## 14. 析构函数

C#中有gc（garbage collection）机制，自动回收垃圾。

析构方法的调用时机和GC一样是完全不可预测的，也不应当依赖于它被调用。析构函数是确保已分配的非托管资源总能被释放的一个补救措施。如果可能就不应当被调用，譬如说手动释放了非托管资源，此时应当通知GC取消对对象的析构函数的调用。

**最后，如果你一定要手动分配非托管资源，那么记住析构函数是保险丝，是最后的保障，不是常规的做法。**

## 15. 命名空间

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

## 16. 继承

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

* 当子类中有和父类**同名的成员**（函数、变量）时，**将覆盖父类的成员**，子类对象在调用时只能调用子类的调用不到父类的。但是不要这样写，违反LSP原则。正确的写法是：子类中可以对父类的非抽象方法进行重载，但不能重写（即参数列表相同，函数体不同，可用final修饰父类的非抽象方法从语法层面避免子类的重写。）

  **需要用new关键字声明成员。**

  LSP中规定不覆写

  ```c#
  Coder cc = new Coder();
  cc.sayHello();	//Coder。而不是Person。
  ```

## 17. 里氏替换原则（LSP）

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

  







## 18. is和as

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

* as转换成功返回转换后对象，失败时返回null

  ```c#
  Student t = p as Student;
  t.sayHello();	//student
  ```

  

  











# 数据结构



# Socket













