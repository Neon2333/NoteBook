https://blog.csdn.net/weixin_43702146/article/details/125759979?utm_medium=distribute.pc_relevant.none-task-blog-2

https://www.jb51.net/article/237968.htm

https://www.cnblogs.com/yilezhu/p/12045018.html

https://www.bilibili.com/video/BV1vz4y1a7Vm/?spm_id_from=333.999.0.0&vd_source=3b08e97e50222fa2ec22737f6dcb2202

https://blog.csdn.net/S1lenceAAA/article/details/108386578

https://www.bilibili.com/video/BV16G4y1c72R/?spm_id_from=333.999.0.0&vd_source=3b08e97e50222fa2ec22737f6dcb2202

http://www.mobange.com/nav/net/99575.html

```c#
using System;
using System.Threading.Tasks;
using System.Threading;
using System.Text;
using System.Linq;
```

## 1. 前言  

* Task是微软在.Net 4.0时代推出来的，Task看起来像一个Thread，实际上，它是在ThreadPool的基础上进行的封装，Task的控制和扩展性很强，在线程的延续、阻塞、取消、超时等方面远胜于Thread和ThreadPool，所以一经问世，基本ThreadPool就被取代了。
  任务通常运行以异步方式在线程池线程上
* ThreadPool相比Thread来说具备了很多优势，但是ThreadPool却又存在一些使用上的不方便。比如：
  ThreadPool不支持线程的取消、完成、失败通知等交互性操作；
  ThreadPool不支持线程执行的先后次序；
  以往，如果开发者要实现上述功能，需要完成很多额外的工作，现在，FCL中提供了一个功能更强大的概念：Task。Task在线程池的基础上进行了优化，并提供了更多的API。在FCL4.0中，如果我们要编写多线程程序，Task显然已经优于传统的方式。
* 执行工作 Task 对象通常以异步方式执行在线程池线程上而不是以同步方式在主应用程序线程，您可以使用 Status 属性，以及 IsCanceled, ，IsCompleted, ，和 IsFaulted 属性，以确定任务的状态。 大多数情况下，lambda 表达式用于指定的任务是执行的工作。
* 任务Task和线程Thread的区别：
  任务是架构在线程之上的，也就是说任务最终还是要抛给线程去执行。
  任务跟线程不是一对一的关系，比如开10个任务并不是说会开10个线程，这一点任务有点类似线程池，但是任务相比线程池有很小的开销和精确的控制。
* **综上，多线程：Thread->ThreadPool->delegate委托异步调用->Task**。Task是微软建议使用的最新的多线程方法。是对ThreadPool的封装，增加了更多好用的功能。Task默认在线程池中创建线程，他会自行调度和回收不用的线程，减少每次重新创建线程的开销，而且创建的是背景线程。

## 2. 创建Task

### (1)无返回值

```c#
//异步
//实例化Task对象，手动Start开启
Task t1 = new Task(()=>{});
t1.Start();	
```

```c#
//异步
//Task类的工厂类的Start New方法，创建直接启动
var t2 = Task.Factory.StartNew(()=>{});
```

```c#
//异步
Task.Run(()=>{});
```

>  Task.Run 和 Task.Factory.StartNew 区别，所以我也就不需要展开告诉大家。
>  只需要知道 Task.Run 是在 dotnet framework 4.5 之后才可以使用，但是 Task.Factory.StartNew 可以使用比 Task.Run 更多的参数，可以做到更多的定制。
>  实际上 Task.Run(foo) 可以认为是对 Task.Factory.StartNew 封装，使用简单的默认的参数。如果需要自己定义很多参数，就请使用 Task.Factory.StartNew 定义参数。
>  可以认为 Task.Run 是简化的 Task.Factory.StartNew 的使用，除了需要指定一个线程是长时间占用的，否则就使用 Task.Run。

```c#
 //同步的方式
 task = new Task(() =>
 {
      Console.WriteLine("工作线程" + Thread.CurrentThread.ManagedThreadId);
  });
  //阻塞执行
  task.RunSynchronously();
```

### （2）有返回值

```c#
Task<T> t = Task.Run(()=>{
	//do something
	return T;
});
```

### （3）有参数

```c#
void test(string str){...}

Task tt = Task.Run(()=>test("123"));

public Task StartNew(
	Action<object> action,
	object state
)

Task.Factory.StartNew(new Action(test), "123");		//StartNew第二个参数为传入函数的参数
```

## 3.Task API

* Wait
  就是等待任务执行（task1）完成，task1的状态变为Completed，才继续往下执行。
  可用task1.Result获取执行结果（阻塞的，若task1未执行完会阻塞在Result）

```c#
//等待task1执行完
task1.Wait();	
//等待task1执行。超时时间millseconds，若在时间内未执行完，则返回false。执行完则返回Task。可用canceltoken取消。
task1.Wait(int millseconds, CancellationToken canceltoken);	
```

**注意：Wait(int)若在timeout内未执行完，返回false后仍然会继续执行，线程不会结束。**

```c#
if(task1.Wait(3000))
{
  cancelSource.Cancel();
}
```

* WaitAll
  等待task1,task2,task3都完成后在继续向下执行。

```c#
Task.WaitAll(task1, task2, task3...);
```

* WaitAny
  等待task1,task2,task3任一完成后在继续向下执行。

```c#
Task.WaitAny(task1, task2, task3...);
```

* Task.ContinueWith
  执行完task1后紧接着执行设定的任务

```c#
//执行完task1,task2,task3后会紧接着执行ContinueWith的内容
string result = Task.WhenAll(task1, task2, task3).ContinueWith<string>(()=>return "this is task result..");
```

* task.IsCompleted

```c#
        static void Main()
        {
            Console.WriteLine("准备洗衣服");

            // 创建一个洗衣服的任务
            Task<int> task = new Task<int>(() =>
            {
                // 模拟洗衣服的时间
                int time = new Random().Next(2, 6);
                Thread.Sleep(TimeSpan.FromSeconds(time));
                return time;
            });

            Console.WriteLine("开始洗衣服");

            // 让洗衣机洗衣服
            task.Start();

            Console.WriteLine("我去打王者，让洗衣机洗衣服");
            // 打王者
            Thread.Sleep(TimeSpan.FromSeconds(4));
            Console.WriteLine("打完王者了，衣服洗完了嘛？");

            Console.WriteLine(task.IsCompleted);
            if (task.IsCompleted)
                Console.WriteLine("洗衣服花的时间：" + task.Result);
            else
            {
                Console.WriteLine("在等洗衣机洗完衣服");
                task.Wait();
                Console.WriteLine("洗衣服花的时间：" + task.Result);
            }
            Console.WriteLine("洗完了，捞出衣服，晒衣服，继续打王者去");
        }
```

## task的暂停、继续、终止、延时执行

### 终止线程

```c#
//定义CancellationTokenSource对象
CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
```

```c#
//定义token对象
var token = cancellationTokenSource.Token;
```

**线程还未被调度执行，可通过token.Cancel()取消：**

```c#
//线程外调用tokensource.Cancel()方法
cancellationTokenSource.Cancel();
```

**线程已经被调度正在运行过程中时，Cancel()已经无法取消了，这时就需要在线程中自己检测了：**

```c#
//Token的IsCancellationRequested属性来确定是否需要取消任务，任务定期轮询它，检查Cancel()是否被调用
if(token.IsCancellationRequested){
	Console.WriteLine("Cancel success..");
}
//调用tokensource.Cancel()会触发在tokensource上注册的委托。
cancellationTokenSource.Register(()=>func));
```

#### Cancel()函数的重载

```C#
    //立刻取消
    public void Cancel();
    //立刻取消
    public void Cancel(bool throwOnFirstException);
    //延迟指定时间后取消
    public void CancelAfter(int millisecondsDelay);
    //延迟指定时间后取消
    public void CancelAfter(TimeSpan delay);
```

#### 方法-1

轮询token.IsCancellationRequested判断是否在线程外执行了token.Cancel()

```c#
//eg.
  class Program
    {
        static void Main(string[] args)
        {
			//定义source
            CancellationTokenSource tokenSource = new CancellationTokenSource();
			//定义token
            CancellationToken token = tokenSource.Token;

            //新建任务
            Task t = Task.Run(() =>
            {
				//循环执行任务，判断任务是否取消，若取消则终止循环
                while(!tokenSource.IsCancellationRequested)
                {
                    //任务开始
                    Console.WriteLine("Task start!");

                    //模拟耗时的操作
                    Thread.Sleep(1000);

                    //任务结束
                    Console.WriteLine("Task finished!");
                }
            }, tokenSource);

            Thread.Sleep(10);

            //用户控制是否取消任务
            while(true)
            {
                Console.Write("请切换到英文输入法");
                Console.WriteLine("取消任务请按Y");
                if((Console.ReadKey()).Key == ConsoleKey.Y)
                {
                    //任务取消
                    tokenSource.Cancel();
			tokenSource.Register(()=>Console.WriteLine("这是task被取消时自动执行的"));
                }
            }
        }
    }
```

#### 方法-2

在task内注册线程的Abort委托

```c#
 class Program
    {
        static void Main(string[] args)
        {
			//定义source
            CancellationTokenSource tokenSource = new CancellationTokenSource();
			//定义token
            CancellationToken token = tokenSource.Token;

            //新建任务
            Task t = Task.Run(() =>
            {
			//给tokensource注册一个委托：Abort在线程内抛出ThradAbortException异常终止线程
		using(tokenSource.Register((Thread.CurrentThread.Abort))
		{
			while(true)	
                	{
                    		//任务开始
                    		Console.WriteLine("Task start!");

                    		//模拟耗时的操作
                    		Thread.Sleep(1000);

                    		//任务结束
                    		Console.WriteLine("Task finished!");
               		}
            });
		}	
            Thread.Sleep(10);

            //用户控制是否取消任务
            while(true)
            {
                Console.Write("请切换到英文输入法");
                Console.WriteLine("取消任务请按Y");
                if((Console.ReadKey()).Key == ConsoleKey.Y)
                {
                    //任务取消
                    tokenSource.Cancel();
                }
            }
        }
    }
```

#### 方法3-TokenSource传入Task.Run作为参数

但这种方法只能在task.Start()但还未被调度执行前进行取消。若线程已开始运行直接Cancel()是取消不掉的。线程还是会执行完返回。
因此当线程中有while(true)循环时，用Cancel是不行的。

### 线程取消回调

通过Register方法注册的服务只会执行一次！
**这里放线程被取消后要执行的代码。**

```c#
tokenSource.Token.Register(() =>
            {
                IsStart = false;
            });
```

### 暂停、继续

由ManualResetEvent的Set()和Reset()实现：

```c#
  public partial class Form1 : Form
    {

        private ManualResetEvent ma;	//Set()后不会自动Reset
        private CancellationTokenSource tokenSource;
        private CancellationToken token;

        static readonly object objLock = new object();

        private bool IsStart = false;
        private bool IsManualResetEvent = true;
        public Form1()
        {
            ma = new ManualResetEvent(IsManualResetEvent);	//初始设为信号状态，线程不阻塞
            tokenSource = new CancellationTokenSource();
            token = tokenSource.Token;
            InitializeComponent();
        }

        /// <summary>
        /// 开始
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void button1_Click(object sender, EventArgs e)
        {
            if (IsStart)
            {
                MessageBox.Show("程序已经启动！");
                return;
            }
            else
            {

                tokenSource = new CancellationTokenSource();
                token = tokenSource.Token;
                IsStart = true;
                Task.Run(() => Start01(), tokenSource.Token);
                Task.Run(() => Start02(), tokenSource.Token);
                Task.Run(() => Start03(), tokenSource.Token);
                Task.Run(() => Start04(), tokenSource.Token);
            }
        }

        public void Start01()
        {
            for (int i = 0; i < 9999; i++)
            {
                if (token.IsCancellationRequested)
                {
                    return;
                }

                string str = "计数器：" + i;
                Invoke(new Action(() => { this.richTextBox1.Text = str; }));
                ma.WaitOne();
                Thread.Sleep(100);
            }
        }
        public void Start02()
        {
            for (int i = 0; i < 9999; i++)
            {
                if (token.IsCancellationRequested)
                {
                    return;
                }

                string str = "计数器：" + i;
                Invoke(new Action(() => { this.richTextBox2.Text = str; }));
                ma.WaitOne();
                Thread.Sleep(100);
            }
        }
        public void Start03()
        {
            for (int i = 0; i < 9999; i++)
            {
                if (token.IsCancellationRequested)
                {
                    return;
                }

                string str = "计数器：" + i;
                Invoke(new Action(() => { this.richTextBox3.Text = str; }));
                ma.WaitOne();
                Thread.Sleep(100);
            }
        }
        public void Start04()
        {
            for (int i = 0; i < 9999; i++)
            {
                if (token.IsCancellationRequested)
                {
                    return;
                }

                string str = "计数器：" + i;
                Invoke(new Action(() => { this.richTextBox4.Text = str; }));
                ma.WaitOne();
                Thread.Sleep(100);
            }
        }


        /// <summary>
        /// 暂停
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void button3_Click(object sender, EventArgs e)
        {

            if (IsStart)
            {
                ma.Reset();		//设为非信号状态，所有线程阻塞在WaitOne()
            }

        }

        /// <summary>
        /// 继续
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void button4_Click(object sender, EventArgs e)
        {
            if (IsStart)
            {
                ma.Set();	//设为信号状态，阻塞在WaitOne()的线程继续执行
            }

        }

        /// <summary>
        /// 停止
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void button2_Click(object sender, EventArgs e)
        {
            //线程取消 + 加上取消回调
            tokenSource.Cancel();
            tokenSource.Token.Register(() =>
            {
                IsStart = false;
            });
        }

        /// <summary>
        /// 窗体关闭事件
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void Form1_FormClosing(object sender, FormClosingEventArgs e)
        {
            // 弹出提示框
            DialogResult result = MessageBox.Show("确定要关闭窗体吗?", "提示", MessageBoxButtons.YesNo, MessageBoxIcon.Information);
            if (result == DialogResult.Yes)
            {
                // 关闭窗体
                e.Cancel = false;
            }
            else
            {
                // 不关闭窗体
                e.Cancel = true;
            }
        }

        private void Form1_Load(object sender, EventArgs e)
        {

        }

        /// <summary>
        /// 计数清零
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void button5_Click(object sender, EventArgs e)
        {
            if (IsStart)
            {
                MessageBox.Show("程序已经启动！");
                return;
            }
            this.richTextBox1.Text = "计数器：0";
            this.richTextBox2.Text = "计数器：0";
            this.richTextBox3.Text = "计数器：0";
            this.richTextBox4.Text = "计数器：0";
        }
    }

```

### 延时执行

可以在延迟 Xms 后执行一个 task 的需求，并要求能够实现在 xms 之前取消 task 的执行：

* .NET 4.5 以上或者 .netcore 的话，可以使用 Task.Delay() 来实现

```c#
static void Main(string[] args)
        {
            var tokenSource = new CancellationTokenSource();
            var token = tokenSource.Token;
 
            tokenSource.Cancel();
 			
			//task延时1000ms后执行ContinueWith中的语句。在延时期间可以通过Cancel终止执行
            Task.Delay(1000, token).ContinueWith(t =>
                {
                    Console.WriteLine("hello world!");
                },token);
 
            Console.ReadLine();
        }
```

* WaitOne(int milliseconds)
  线程阻塞直到milliseconds毫秒或收到信号

```C#
var tokenSource = new CancellationTokenSource();
    var token = tokenSource.Token;
 
    var task = Task.Factory.StartNew(() =>
    {
        // wait for 5 seconds or user hit Enter key cancel the task
        token.WaitHandle.WaitOne(5000);
        token.ThrowIfCancellationRequested();
        Console.WriteLine("Task started its work");
    });
```

## 4. 异步方法

使用 async 修饰符可将方法、lambda 表达式或匿名方法指定为异步。
使用 async 修饰的方法，称为异步方法。
**await必须使用在异步方法中。async和await必须搭配使用。**
说白了await/async就是一个语法糖，单纯使用await/async并不会创建一个线程，创建线程的这个操作由是线程池决定的。识别性更高：
**以async声明异步方法，但仍然需要在方法内部创建线程或任务以异步执行想要的语句，如Task。**

### (1)异步方法无返回值

无返回值的异步方法不需要获得返回值，因此无需阻塞主线程获取返回值。

```c#
CalculateSum3(1, 2);
public static async void CalculateSum3(int a, int b)
    {
        int c = 0;
        await Task.Run(() =>
        {
            Console.WriteLine(Thread.CurrentThread.ManagedThreadId + ":start");
            Thread.Sleep(1000 * 3);
            Console.WriteLine(Thread.CurrentThread.ManagedThreadId + ":end");
            c = a + b;
        });
        Console.WriteLine(c);
    }


Task calculateSum2 = CalculateSum2(1, 2);
public static async Task CalculateSum2(int a, int b)
    {
        int c = 0;
        await Task.Run(() =>
        {
            Console.WriteLine(Thread.CurrentThread.ManagedThreadId + ":start");
            Thread.Sleep(1000 * 3);
            Console.WriteLine(Thread.CurrentThread.ManagedThreadId + ":end");
            c = a + b;
        });
        Console.WriteLine(c);
    }
```

### (2)异步方法有返回值

**异步方法返回值只能是void，Task，或者Task< T >,我们添加返回值Task< int >**

```c#
//int res = CalculateSum(1, 2);
Task<int> tt = CalculateSum(1, 2);
int res = tt.Result;

 public static async Task<int> CalculateSum(int a, int b)
    {
        int c = 0;
        await Task.Run(() =>
        {
            Console.WriteLine(Thread.CurrentThread.ManagedThreadId + ":start");
            Thread.Sleep(1000 * 3);
            Console.WriteLine(Thread.CurrentThread.ManagedThreadId + ":end");
            c = a + b;
        });
        Console.WriteLine("计算结果：" + c);
        return c;
    }
```

### （3）阻塞主线程获取异步方法返回值

* Task.Result
  子线程本身不会被阻塞，主线程只有调用Task.Result处会被阻塞。

```c#
Console.WriteLine("start");
Task<int> task = CalculateSum(1, 2);	//4个task异步执行，主线程不阻塞
Task<int> task2 = CalculateSum(1, 2);
Task<int> task3 = CalculateSum(1, 2);
Task<int> task4 = CalculateSum(1, 2);
Console.WriteLine("end");
Console.WriteLine("开始等待返回结果");
Console.WriteLine(task.Result);		//主线程在这里阻塞，获取该task的返回值Result后才会向下执行
Console.WriteLine(task2.Result);
Console.WriteLine(task3.Result);
Console.WriteLine(task4.Result);
Console.WriteLine("等待返回结果完毕，程序结束");
```

* await
  主线程被阻塞在await处，当调用的子线程返回值时才会继续向下执行。

```c#
static void Main(string[] args)
    {
        test();
    }

 public static async void test()
 {
	int res = await CalculateSum(1, 2);		//主线程在这里开始阻塞。四个任务依次执行（同步）
	int res2 = await CalculateSum(1, 2);
	int res3 = await CalculateSum(1, 2);
	int res4 = await CalculateSum(1, 2);
}
```

## 5. 坑

### （1）在UI线程中调用async方法，依靠异步方法返回值task.Result修改控件值，会卡死。

https://www.cnblogs.com/guanglin/p/11677105.html

* 原因 
  Task.Result会直接阻塞当前调用线程，而aysnc、await机制是使用状态机切换上下文，
  当调用.Result时，当前线程阻塞，async/await状态机无法切换回线程上下文，导致死锁。
* 解决
  不用async/await异步方法。直接在普通函数里Task.Run

### （2）CancellationTokenSource调用Cancel()后要重新new对象传入Task.Run否则Task不会启动