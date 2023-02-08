# C#多线程

https://www.jianshu.com/p/36a65838fe46

https://zhuanlan.zhihu.com/p/41284787

https://www.cnblogs.com/vaevvaev/p/7127600.html

## 1. 概念

线程是进程的一个实体,是CPU调度和分派的基本单位,它是比进程更小的能独立运行的基本单位.线程自己基本上不拥有系统资源,只拥有一点在运行中必不可少的资源(如程序计数器,一组寄存器和栈),但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源。一个线程可以创建和撤销另一个线程;同一个进程中的多个线程之间可以并发执行。

CLR可以区分两种线程：前台前程、后台线程。主线程是一个前台线程。只要有前台线程未执行完，进程就不会结束，只有当所有前台线程都执行完或异常退出，进程才会结束。而后台线程的执行完或异常退出与与否无法影响进程是否结束，进程一旦结束后台线程也会结束。

所以前台线程一般用来执行需要较长时间执行或需要长时间等待的任务，后台线程一般用来处理执行时间很短的小任务。



## 2. 创建和使用线程

### （1）常用——Thread类

* 使用Thread类通过**ThreadStart**（**不给子线程传参数**，无返回值）或**ParameterizedThreadStart**（**给子线程传一个输入参数**，无返回值）类型的委托创建一个Thread对象，开启一个新线程，执行委托传递的回调函数。

  不同的是，前者不给子线程执行的回调传参，后者传参。

* 创建好的线程还未启动，需要Start()启动线程（**并且传参，若有参数的话**）。调用join()的线程可以阻塞当前线程，直至调用**join**的子线程执行完毕。

* ThreadStart是一个委托，这个委托的定义为void ThreadStart()，没有参数与返回值。

* Thread创建的线程是**前台线程**，可通过**IsBackGround**设置是前台线程还是后台线程。通过**Priority**设置优先级。

* Thread.Name——设定线程名，这意味着每个线程可以设定一个Name属性来标志它们。线程的Name属性默认情况下是null。**该值只能被赋值一次**, 如果已经赋值一次的情况下再次赋值, 将抛出InvalidOperationException异常。这种设计应该是为了保证线程的名字不被改变而安排的。

* 如需中止线程，调用**Abort()**方法，在调用该方法的线程上抛出**ThreadAbortException**异常，以结束该线程。**线程内部**可以通过try catch捕获该异常，在catch模块中进行一些必要的处理，如释放持有的锁和文件资源等。但是通常来说，应当慎重使用Abort()方法，如果在当前线程中抛出该异常，其结果是可预测的，但是对于其他线程，它会中断任何正在执行的代码，有可能中断静态对象的生成，造成不可预测的结果。

* 线程太多，未控制好导致线程无法回收，随着时间流逝积累的线程越来越多

  > ```C#
  > //获取当前进程的所有线程，并查看某一指定线程的状态或运行时间
  > Process current = Process.GetCurrentProcess();
  > ProcessThreadCollection allThreads = current.Threads;
  > //通过创建线程时的Name获得线程的信息，因此需要用ID获取Name
  > string threadID = Thread.CurrentThread.ManagedThreadId.ToString();
  > ```
  >
  > **方案：**
  >
  > 定义一个全局的map，其中以线程i的为key，线程名称为value，每次新线程启动就保存进去，线程结束就删除，这样就可以根据名称确认是不是你的目标线程了。

  ```C#
   public static void CallToChildThread1(){
  	try{
                	Console.WriteLine("Child thread starts");
                  // 计数到 10
                  for (int counter = 0; counter <= 10; counter++){
                      Thread.Sleep(500);
                      Console.WriteLine(counter);
                  }
                  Console.WriteLine("Child Thread Completed");
  
  	catch (ThreadAbortException e){
                  Console.WriteLine("Thread Abort Exception");
  	}
  	finally{
                  Console.WriteLine("finish something");
  	}
  }
  
   public static void CallToChildThread2(object arg){
  	try{
                	Console.WriteLine("Child thread starts");
                  Console.WriteLine(arg);
                  Console.WriteLine("Child Thread Completed");
      }
  	catch (ThreadAbortException e){
                  Console.WriteLine("Thread Abort Exception");
  	}
  	finally{
                  Console.WriteLine("finish something");
  	}
  }
       
  //使用ThreadStart创建
  Thread thread1 = new Thread(new ThreadStart(CallToChildThread1));
  thread1.IsBackGround=true;	//设未后台线程
  thread1.Name = "thread1";	//设置线程名
  thread1.Start();	//启动线程，无参数传入回调
  thread1.join();	//阻塞主线程直到thread1执行完
  thread1.sleep(1000);	//thread1休眠1000ms
  thread1.Abort();	//thread1中止。thread1内部执行结果为starts->abort exception->finish sth.
       
  
  //使用parameterizedThead创建
  Thread parameterizedThread = new Thread(new ParameterizedThreadStart(CallToChildThread2));
  parameterizedThread.Start("test");	//将参数传递给CallToChildThread2
  ```

  * 属性

    > CurrentThread：获取当前正在运行的线程
    >
    > **ThreadState**：获取一个值，该值包含当前线程的状态
    >
    > **IsAlive**：获取一个值，该值指示当前线程的执行状态
    >
    > IsBackground：获取或设置一个值，该值指示某个线程是否为后台线程。
    >
    > Name：获取或设置线程的名称
    >
    > Priority：获取或设置一个值，该值指示线程的调度优先级
    >
    > ManagedThreadId：获取当前托管线程的唯一标识符
    
  * 成员方法

    > **public void Abort()**：调用此方法的线程上引发 ThreadAbortException，以开始终止此线程的过程
    >
    > **public void Join()**：阻塞调用线程，直到某个线程终止为止。有不同的重载形式
    >
    > **public void Start()**：开始一个线程
    >
    > **public static void Sleep( int millisecondsTimeout )**：让线程休眠一段时间
    >
    > **public static bool Yield()**：导致调用线程执行准备好在当前处理器上运行的另一个线程。由操作系统选择要执行的线程

### （2）常用——委托的BeginInvoke和EndInvoke异步调用

* 用委托(Delegate)的BeginInvoke和EndInvoke方法操作线程。在创建了委托对象的线程上异步执行委托指向的函数。

  BeginInvoke方法可以使用线程异步地执行委托所指向的方法。

  **由于使用BeginInvoke创建的线程都是后台线程**

  然后通过EndInvoke方法获得方法的返回值（EndInvoke方法的返回值就是被调用方法的返回值），或是确定方法已经被成功调用。

  使用BeginInvoke异步调用方法时，如果方法未执行完，EndInvoke方法就会**一直阻塞**，直到被调用的方法执行完毕。

```C#
using System.Threading; 
namespace MyThread 
{ 
  class Program 
  { 
	private static int newTask(int ms) 
	{ 
		Console.WriteLine("任务开始"); 
		Thread.Sleep(ms); 
		Random random = new Random(); 
		int n = random.Next(10000); //返回小于指定maxVal的非负整数
		Console.WriteLine("任务完成"); 
		return n; 
	} 

		private delegate int NewTaskDelegate(int ms); 
		static void Main(string[] args) 
	{ 
		NewTaskDelegate task = newTask; //声明委托对象指向函数newTask
		IAsyncResult asyncResult = task.BeginInvoke(2000, null, null); // EndInvoke方法将被阻塞2秒 
		int result = task.EndInvoke(asyncResult); 
		Console.WriteLine(result);
 	}
   }
 }
```

* 使用IAsyncResult asyncResult属性来判断异步调用是否完成   

  虽然上面的方法可以很好地实现异步调用，但是当调用EndInvoke方法获得调用结果时，若异步调用未完成则EndInvoke就会一直阻塞，整个程序就象死了一样，这样做用户的感觉并不会太好，因此，我们可以使用asyncResult来判断异步调用是否完成，并显示一些提示信息。这样做可以增加用户体验

  ```C#
  static void Main(string[] args) 
  	{ 
  		NewTaskDelegate task = newTask; //声明委托对象指向函数newTask
  		IAsyncResult asyncResult = task.BeginInvoke(2000, null, null); // EndInvoke方法将被阻塞2秒 
  		while(!asyncResult.IsCompleted){
              Console.Write("*");
              Thread.sleep(100);	//主线程阻塞1000ms，不然判断过于频繁消耗资源
          }
      	int result = task.EndInvoke(asyncResult); //主线程执行到这里表明异步调用已完成
  		Console.WriteLine(result);
   	}
  ```

  

### （3）常用——适用需要传递多个参数到子线程中

* 将子线程需要执行的回调函数，封装成类，通过构造函数船体多个参数。然后传递到ThreadStart()

```c#
class Program
{
static void Main(string[] args)
{
MyThread mt = new MyThread(100,200);	//传递了两个参数
ThreadStart threadStart = new ThreadStart(mt.Calculate);
Thread thread = new Thread(threadStart);
thread.Start();
   //等待线程结束
while (thread.ThreadState != ThreadState.Stopped)
{
Thread.Sleep(10);
}
Console.WriteLine(mt.Result);//打印返回值
Console.Read();
}
}

public class MyThread//线程类
{
public int Parame { set; get; }//参数
public int Result { set; get; }//返回值
//构造函数
public MyThread(int param1,int param2)
{
this.Parame = param1;
this.Result = param2;
}
//线程执行方法
public void Calculate()
{
Random ra = new Random();//随机数对象
Thread.Sleep(ra.Next(10, 100));//随机休眠一段时间
Console.WriteLine(this.Parame);
this.Result = this.Parame * ra.Next(10, 100);
}
}
```

* 使用匿名函数替代将回调函数封装成类

  ```C#
  class Program
  {
  static void Main(string[] args)
  {
  int Parame = 100;//当做参数
  int Result = 0;//当做返回值
  //匿名方法
  ThreadStart threadStart = new ThreadStart(delegate()
  {
  Random ra = new Random();//随机数对象
  Thread.Sleep(ra.Next(10, 100));//随机休眠一段时间
  Console.WriteLine(Parame);//输出参数
  Result = Parame * ra.Next(10, 100);//计算返回值
  });
  Thread thread = new Thread(threadStart);
  thread.Start();//多线程启动匿名方法
  //等待线程结束
  while (thread.ThreadState != ThreadState.Stopped)
  {
  Thread.Sleep(10);
  }
  Console.WriteLine(Result);//打印返回值
  Console.Read();
  }
  }
  ```

  

### （4）线程池

* ThreadPool类维护一个线程的列表，提供给用户以执行不同的小任务，减少频繁创建线程的开销。ThreadPool的使用比较简单，只需调用ThreadPool.QueueUserWorkItem()方法，传递一个WaitCallback类型的委托，线程池即从池中选择一个线程执行该任务。

* 线程池中的线程均为**后台线程**，并且不能修改为前台线程

* 能给入池的线程设置优先级或名称

* **入池的线程只适合时间较短的任务，如果线程需要长时间运行，应使用Thread类创建线程或使用Task的LongRunning选项**

* ThreadPool.QueueUserWorkItem:

  > ThreadPool.QueueUserWorkItem(WaitCallBack)：将方法排入队列以执行，此方法在线程池中有线程时可执行。
  >
  > ThreadPool.QueueUserWorkItem(WaitCallBack, Object)：同上，Object为传入回调函数的参数。

  ```C#
  static void Main(string[] args){
      Console.WriteLine("main thread ID={0}",Thread.CurrentThread.ManagedId);
      ThreadPool.QueueUserWorkItem(callbackFunc);
      ThreadPool.QueueUserWorkItem(callbackFunc);
      Thread.sleep(1000);
      Console.WriteLine("main thread is abort");
  }
  
  public static void callbackFunc(object state){
      Console.WriteLine("child thread is running");
      if(state==null){
          Console.WriteLine("thread ID={0}:{1}",Thread.CureentThread.ManagedId,state.toString());
          
      }else{
          Console.WriteLine("thread ID={0}",Thread.CureentThread.ManagedId);
      }
  }
  ```





### （5）Task类

https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks?view=net-5.0

*  Task的属性IsCompleted, IsCanceled表示它是否完成和是否取消 

  Async: 当一个方法由async关键字标识，表明这个方法是异步方法，当它被调用时，会创建一个线程来执行

  Async 只能修饰void，Task，Task<>



## 3. 线程同步

https://blog.csdn.net/u010476739/article/details/104937749/

https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/lock-statement

### 1. 概念

当一个线程对一块内存进行操作时，其他线程不能对该线程进行操作，直到该线程完成操作。各个线程对临界区资源进行互斥操作。

**把多线程共同操作的资源放在临界区中锁起来**

### 2. 临界区资源操作方法

调用系统API或者利用语言的多线程操作。

### 3. 常用 ——C#语法的线程同步方法

#### （1）lock

lock语句获取给定对象的互斥 lock，执行语句块，然后释放 lock。 持有 lock 时，持有 lock 的线程可以再次获取并释放 lock。 阻止任何其他线程获取 lock 并等待释放 lock。

但是lock语法只能限制某一个时刻只有一个线程访问上锁的代码块，但是一个锁（引用对象）可以给多个代码块上锁（其实也就是lock语句的时候用的是同一个引用对象）。因此，使用lock时有几个准则：

> 当同步对共享资源的线程访问时，请锁定专用对象实例（例如，**private readonly object balanceLock = new object();**）或另一个不太可能被代码无关部分用作 lock 对象的实例。 避免对不同的共享资源使用相同的 lock 对象实例，因为这可能导致死锁或锁争用。 具体而言，避免将以下对象用作 lock 对象：
>
> - `this`（调用方可能将其用作 lock）。
> - [Type](https://docs.microsoft.com/zh-cn/dotnet/api/system.type) 实例（可以通过 [typeof](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/type-testing-and-cast#typeof-operator) 运算符或反射获取）。
> - 字符串实例，包括字符串文本，（这些可能是[暂存的](https://docs.microsoft.com/zh-cn/dotnet/api/system.string.intern#remarks)）。
>
> 尽可能缩短持有锁的时间，以减少锁争用。

 常见的结构 lock (this)、lock (typeof (MyType)) 和 lock ("myLock") 违反此准则： 　　

1.如果实例可以被公共访问，将出现 lock (this) 问题。 　　

2.如果 MyType 可以被公共访问，将出现 lock (typeof (MyType)) 问题。 　　

3.由于进程中使用同一字符串的任何其他代码将共享同一个锁，所以出现 lock("myLock") 问题。 　　

4.最佳做法是定义 **private 对象**来锁定, 或 **private static** 对象变量来保护所有实例所共有的数据。 比如可用**ArrayList,HashTable，Queue，Stack，包括新增的支持泛型的List** 。

* 用法

```C#
lock (x){
    // do something
    
}
//x是一个引用对象
```

```C#
using System;
using System.Threading.Tasks;

public class Account
{
    private readonly object balanceLock = new object();
    private decimal balance;

    public Account(decimal initialBalance) => balance = initialBalance;

    public decimal Debit(decimal amount)
    {
        if (amount < 0)
        {
            throw new ArgumentOutOfRangeException(nameof(amount), "The debit amount cannot be negative.");
        }

        decimal appliedAmount = 0;
        lock (balanceLock)
        {
            if (balance >= amount)
            {
                balance -= amount;
                appliedAmount = amount;
            }
        }
        return appliedAmount;
    }

    public void Credit(decimal amount)
    {
        if (amount < 0)
        {
            throw new ArgumentOutOfRangeException(nameof(amount), "The credit amount cannot be negative.");
        }

        lock (balanceLock)
        {
            balance += amount;
        }
    }

    public decimal GetBalance()
    {
        lock (balanceLock)
        {
            return balance;
        }
    }
}

class AccountTest
{
    static async Task Main()	//async修饰函数
    {
        var account = new Account(1000);
        var tasks = new Task[100];
        for (int i = 0; i < tasks.Length; i++)
        {
            tasks[i] = Task.Run(() => Update(account));
        }
        await Task.WhenAll(tasks);
        Console.WriteLine($"Account's balance is {account.GetBalance()}");
        // Output:
        // Account's balance is 2000
    }

    static void Update(Account account)
    {
        decimal[] amounts = { 0, 2, -3, 6, -2, -1, 8, -5, 11, -6 };
        foreach (var amount in amounts)
        {
            if (amount >= 0)
            {
                account.Credit(amount);
            }
            else
            {
                account.Debit(Math.Abs(amount));
            }
        }
    }
}
```



#### （2）Monitor

Monitor功能比lock丰富。

lock实质上是Monitor的语法糖， 简而言之，lock 的写法是 Monitor 类的一种简写 。lock(x){}等价于：

```C#
object __lockObj = x;
bool __lockWasTaken = false;
try
{
    System.Threading.Monitor.Enter(__lockObj, ref __lockWasTaken);
    // Your code...
}
finally
{
    if (__lockWasTaken) System.Threading.Monitor.Exit(__lockObj);//由于该代码使用 try...finally 块，即使在 lock 语句的正文中引发异常，也会释放 lock。
}
```

* object obj=new object();

  Monitor在锁对象obj上会维持**两个线程队列R和W**以及**一个引用T** :

  T是对当前获得了obj锁的**线程的引用**（设此线程为CurrThread）; 

  R为**就绪队列**，其上的线程已经准备好获取obj锁。当obj锁被CurrThread释放后（CurrThread可通过Monitor.Exit(obj)或Monitor.Wait(obj)来释放其所获的obj锁）这些线程就会去竞争obj锁，获得obj锁的线程将被T引用; 线程调用Monitor.Enter(obj)或Monitor.TryEnter(obj)将会使该线程直接进入R队列。

  W为**等待队列**，其上的线程是因为调用了Monitor.Wait(obj)而进入W队列的；**W上的线程不会被OS直接调度执行，也就是说它们没有准备好获取obj锁，就是说在等待队列上的线程不能去获得obj锁。**当前获得obj锁的线程CurrThread调用Monitor.Pulse(obj)或Monitor.PulseAll(obj)后会使W队列中的第一个等待线程或所有等待线程被**移至R队列**，这时被移至R队列的这些线程就有机会被OS直接调度执行，也就是有可以去竞争obj锁。

* Monitor的成员方法：

  **Monitor.Enter(obj)**：线程会进入R队列以等待获取obj锁，返回值void

  **Monitor.TryEnter(obj)** : 返回值bool，若获得锁则返回true

  **Monitor.TryEnter(obj, Timespan timeout)**：timeout单位毫秒，timeout=-1时同TryEnter，无限期获取锁。

  timeout>0时表示在timeout时间内获取锁，若不成功则返回false

  **Monitor.Exit(obj)** : 线程释放obj锁（只有获取了obj锁的线程才能执行Monitor.Exit(obj)）
  
  **Monitor.Wait(obj):** 线程释放当前获得的obj锁，然后进入**W队列**并阻塞，只有该方法将线程放入等待队列
  
  **Monitor.Wait(obj,int ms)**：**阻塞，且ms毫秒后自动唤醒。返回值为bool**，若是被Pulse唤醒则返回true，若自己醒则返回false
  
  **Monitor.Pulse(obj) :** 将W队列中的第一个等待线程移至R队列中以使第一个线程有机会获取obj锁
  
  **Monitor.PulseAll(obj):** 将W队列中的所有等待线程移至R队列以使得这些线程有机会获得obj锁

```C#
//线程1执行完毕，再执行线程2 
private object lockObj = new object();
        private void Execute()
        {
            Monitor.Enter(lockObj);	//获取对象的锁，其他线程不能进入临界区
            for (int i = 0; i < 5;i++ )
            {
                Console.WriteLine("Thread Name:" + Thread.CurrentThread.Name + ", Count:" + i);
                Thread.Sleep(new Random().Next(5000));
            }
            Monitor.Exit(lockObj);	//释放对象的锁
        }
        public void Test()
        {
            Thread thread1 = new Thread(new ThreadStart(Execute));
            thread1.Name = "Thread1";
            thread1.Start();	//thread1获取锁
            Thread thread2 = new Thread(new ThreadStart(Execute));
            thread2.Name = "Thread2";
            thread2.Start();	//thread2获取锁
            
            thread1.Join();
            thread2.Join();
        }

/**
Thread Name:Thread1, Count:0
Thread Name:Thread1, Count:1
Thread Name:Thread1, Count:2
Thread Name:Thread1, Count:3
Thread Name:Thread1, Count:4
Thread Name:Thread2, Count:0
Thread Name:Thread2, Count:1
Thread Name:Thread2, Count:2
Thread Name:Thread2, Count:3
Thread Name:Thread2, Count:4
*/
//Thread2之所以会执行是因为在Enter中无限期等待
```

```C#
//线程2timeout未取得锁
private void Execute1()
        {
            if (Monitor.TryEnter(lockObj,1000))	//设置试图获取锁的时间为1000ms，timeout的话则未能成功获取锁，返回false
            {
                for (int i = 0; i < 5; i++)
                {
                    Console.WriteLine("Thread Name:" + Thread.CurrentThread.Name + ", Count:" + i);
                    Thread.Sleep(new Random().Next(5000));
                }
                Monitor.Exit(lockObj);
            }
        }
        static void Main(string[] args)
        {
            Thread thread1 = new Thread(new ThreadStart(Execute1));
            thread1.Name = "Thread1";
            thread1.Start();
            Thread thread2 = new Thread(new ThreadStart(Execute1));
            thread2.Name = "Thread2";
            thread2.Start();

            thread1.Join();
            thread2.Join();
        }
/**
Thread Name:Thread1, Count:0
Thread Name:Thread1, Count:1
Thread Name:Thread1, Count:2
Thread Name:Thread1, Count:3
Thread Name:Thread1, Count:4

因为在1000ms内Thread2尝试获取锁失败，只有Thread1执行完后Thread2才能视图获取锁。所以没有执行锁内部的代码块。
*/

//要想Thread2也可获得锁得以执行，可将TryEnter的timeout时间设置-1或设置长于Thread1执行的时间
 public static void Execute1()
        {
            if (Monitor.TryEnter(Program.lockObj, -1))
            {
                for (int i = 0; i < 5; i++)
                {
                    Console.WriteLine("Thread Name:" + Thread.CurrentThread.Name + ", Count:" + i);
                    Thread.Sleep(new Random().Next(5000));
                }
                Monitor.Exit(Program.lockObj);
            }
        }
```

```C#
//线程1和2轮流执行
private Queue queue = new Queue();
        public void SendThread()
        {
            int counter = 0;
            lock (queue)
            {
                while (counter < 5)
                {
                    //Wait, if the queue is busy.
                    Monitor.Wait(queue);
                    //Push one element.
                    queue.Enqueue(counter);
                    //Release the waiting thread.
                    Monitor.Pulse(queue);
                    counter++;
                }
            }
        }
        public void ConsumeThread()
        {
            lock (queue)
            {
                //Release the waiting thread.
                Monitor.Pulse(queue);
                //Wait in the loop, while the queue is busy.
                //Exit on the time-out when the first thread stops.
                while (Monitor.Wait(queue, 1000))
                {
                    //Pop the first element.
                    int counter = (int)queue.Dequeue();
                    //Print the first element.
                    Console.WriteLine("Queue Value:" + counter.ToString());
                    //Release the waiting thread.
                    Monitor.Pulse(queue);
                }
            }
        }
        public void Test2()
        {
            Thread thread1 = new Thread(new ThreadStart(SendThread));
            thread1.Name = "Thread1";
            thread1.Start();
            Thread thread2 = new Thread(new ThreadStart(ConsumeThread));
            thread2.Name = "Thread2";
            thread2.Start();
            thread1.Join();
            thread2.Join();
            Console.WriteLine("Queue Size:" + queue.Count);
        }

/**
输出结果:

Queue Value:0
Queue Value:1
Queue Value:2
Queue Value:3
Queue Value:4
Queue Size:0
*/
```

#### （3）demo

```C#
 private void button_insert_Click(object sender, EventArgs e)
        {
            //int ms = 0;//给tiny int赋值字符串？

            int total = 1000000;  //记录总条数，至少是4
            //string cmd1 = "CAMERA1 (BRAND,RESULT,RIQI) VALUES (@brand,@result,datetime('now','localtime'));";
            //string cmd2 = "CAMERA2 (BRAND,RESULT,RIQI) VALUES (@brand,@result,datetime('now','localtime'));";
            string cmd1 = "CAMERA1 (BRAND,RESULT,RIQI) VALUES (@brand,@result,STRFTIME('%Y-%m-%d %H:%M:%f', 'NOW'));";  //时间戳精确到ms
            string cmd2 = "CAMERA2 (BRAND,RESULT,RIQI) VALUES (@brand,@result,STRFTIME('%Y-%m-%d %H:%M:%f', 'NOW'));";

            SQLiteParameter[] param =
            {
                new SQLiteParameter("@brand",DbType.String),   //DbType？MSDN上写的是SqliteType，但是找不到这个Enum
                new SQLiteParameter("@result", DbType.Int16),
            };
            param[0].Value = "brand";
            param[1].Value = 1;

            ThreadInsert.setTotal(total);   //调用静态函数给类变量赋值
            ThreadInsert mt1 = new ThreadInsert(cmd1,param);
            ThreadInsert mt2 = new ThreadInsert(cmd2, param);
            Thread thread1 = new Thread(new ThreadStart(mt1.threadInsert));
            thread1.Name = "thread1";
            Thread thread2 = new Thread(new ThreadStart(mt2.threadInsert));
            thread2.Name = "thread2";

            //插入开始
            st.Start(); //计时开始

            param[0].Value = "brand";
            param[1].Value = 1;
            sqlite1.SQLite_insert(cmd1, param, "BEGIN TRANSACTION;\n", ""); //第一条插入和最后一条插入加上事务指令
            sqlite1.SQLite_insert(cmd2, param, "", ""); //第一条插入和最后一条插入加上事务指令

            thread1.Start();
            thread2.Start();
            thread1.Join();
            thread2.Join();

            sqlite1.SQLite_insert(cmd1, param, "", "");
            sqlite1.SQLite_insert(cmd2, param, "", "\nEND TRANSACTION;");

            st.Stop();  //计时结束
            MessageBox.Show("插入耗时：" + st.ElapsedMilliseconds.ToString() + "ms");
        }

        //线程类，封装子线程函数
        public class ThreadInsert
        {
            public static int total = 0;
            //要传递给线程函数的参数
            public string cmd { set; get; }
            public SQLiteParameter[] param { set; get; }

            public ThreadInsert(string param2, SQLiteParameter[] param3)
            {
                this.cmd = param2;
                this.param = param3;
            }

            //给total赋值（静态函数给类静态变量赋值）
            public static void setTotal(int t)
            {
                ThreadInsert.total = t;
            }

            //线程执行函数
            public void threadInsert(){
                while ((ThreadInsert.total - 4) > 0)
                {
                    this.param[0].Value = "brand";
                    this.param[1].Value = ThreadInsert.total % 2;
                    //Thread.Sleep(400);
                    Monitor.Enter(Form1.lockObj); //加锁
                    sqlite1.SQLite_insert(this.cmd, this.param, "", "");
                    ThreadInsert.total--;
                    Monitor.Exit(lockObj);  //释放锁

                }
            }

        }
```





### 4. 系统调用

* Mutex



* Semaphore

  

### 5. 原子操作

https://blog.csdn.net/weixin_44228698/article/details/108524804

* 类Interlocked

# 4. 这么多种创建线程的方法该选哪种？

# 5. 子线程里访问主线程创建的控件

微软不让你在子线程里访问主线程创建的控件，他认为这个是不安全的。所以就开了一个委托来异步操作一下

```C#
Invoke((EventHandler)delegate {progressBar.Value = val});	//子线程里修改progressBar的进度值
```



