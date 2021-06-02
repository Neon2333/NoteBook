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
              dlg += sayGoodBye;  //将sayGoodBye方法与dlg实例关联。同时关联多个方法时，按照方法定义顺序调用方法。
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