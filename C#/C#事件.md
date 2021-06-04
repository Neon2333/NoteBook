# C#事件与委托

## 1.定义

* 委托

  ```C#
  //委托的声明
  //委托的使用方法
  //委托的解释：讲方法以变量的形式传递，并以方法的形式执行（函数指针？）
  namespace demo_event
  {
      public delegate void SayHelloDlg(String name); //声明委托
  
  
      class Program
      {
          static void Main(string[] args)
          {
              SayHelloDlg dlg = sayHello; //创建一个委托的实例，并将sayHello方法和它关联
              dlg += sayGoodBye;  //将sayGoodBye方法绑定在dlg上。同时关联多个方法时，按照方法定义顺序调用方法。
              dlg -= sayGoodBye;  //将sayGoodBye方法与dlg实例解除关联
              dlg("wk");  //调用委托实例。最后显示sayHello
  
  
  
          }
  
          //定义两个回调方法
          //返回值和参数类型要和委托定义相同
          public static void sayHello(string name)
          {
              Console.WriteLine($"{name},hello!!");
          }
  
          public static void sayGoodBye(string name)
          {
              Console.WriteLine($"{name},goodBye!!");
          }
      }
  }
  ```

  前面使用的每个委托都只包含一个方法调用，调用委托的次数与调用方法的次数相同，如果要调用多个方法，就需要多次给委托赋值，然后调用这个委托。

  委托也可以包含多个方法，这时候要向委托对象中添加多个方法，这种委托称为**多播委托**，多播委托有一个方法列表，如果调用**多播委托**，就可以连续调用多个方法，即先执行某一个方法，等该方法执行完成之后再执行另外一个方法，这些方法的参数都是一样的，这些方法的执行是在一个线程中执行的，而不是每个方法都是一个线程，最终将执行完成所有的方法。

  多播委托包含一个逐个调用的委托集合。如果通过委托调用的一个方法抛出了异常，整个迭代就会停止。即如果任一方法引发了异常，而在该方法内未捕获该异常，则该异常将传递给委托的调用方，并且不再对调用列表中后面的方法进行调用。

  在这种情况下，为了避免这个问题，应手动迭代方法列表。Delegate类定义了方法GetInvocationList()，它返回一个Delegate对象数组。现在可以使用这个委托调用与委托直接相关的方法，捕获异常，并继续下一次迭代

  https://blog.csdn.net/chaixinke/article/details/45396269

  

* 事件

  ```c#
  namespace demo_event
  {
      public delegate void SayHelloDlg(String name); //声明委托
  
  
  
      class Program
      {
          //sayHello类型的事件
          public static event SayHelloDlg sayHelloEvent; 
  
          static void Main(string[] args)
          {
              //委托
              SayHelloDlg dlg = sayHello; //创建一个委托的实例，并将sayHello方法和它关联
              dlg += sayGoodBye;  //将sayGoodBye方法与dlg实例关联。同时关联多个方法时，按照方法定义顺序调用方法。
              dlg -= sayGoodBye;  //将sayGoodBye方法与dlg实例解除关联
              dlg("wk");  //调用委托。最后显示sayHello
  
  
  
              //事件:被限制了用法的委托
              sayHelloEvent += Program_sayHelloEvent; //注册事件（事件和方法关联）
              //调用事件
              if (sayHelloEvent != null)
              {
                  sayHelloEvent("wk");
              }
          }
  
          //定义事件的回调方法 
          private static void Program_sayHelloEvent(string name)
          {
              Console.WriteLine($"{name},是事件!!");
  
          }
  
          //定义两个回调方法
          //返回值和参数类型要和委托定义相同
          public static void sayHello(string name)
          {
              Console.WriteLine($"{name},hello!!");
          }
  
          public static void sayGoodBye(string name)
          {
              Console.WriteLine($"{name},goodBye!!");
          }
      }
  }
  ```

  



## 2. 自定义事件步骤

https://www.cnblogs.com/zzh1236/archive/2012/08/19/2646335.html

* 定义委托

  ```c#
  //创建一个委托，一般两个参数：一个Object类型，一个继承于EventArgs的类型
  delegate void EventHandler(object sender, EventArgs e);	//WorkControl，462行
  ```

如：

```c#
public delegate void UserRequest(object sender, EventArgs e);
```

* 将委托和特定事件关联

```C#
//这里特定事件为System_Click
public event EventHandler System_Click;
```

* 事件响应函数

  

## 3.Invoke 与BeginInvoke 的用法

https://www.cnblogs.com/dcz2015/p/11011507.html

https://blog.csdn.net/qq_41145567/article/details/80781475