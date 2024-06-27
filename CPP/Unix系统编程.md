# 1. 信号

---

> 后台服务程序要脱离shell运行，可用2种方法：
>
> * 后台运行：`demo &`
> * fork一个子进程，再终止父进程，从而让子进程被系统托管（ppid=1）
>
> 终止进程时，在shell下可以`ctrl-c`、`kill pid`、`killall demo`。
>
> 这三个都是给系统给demo程序发送了**信号**，ctrl-c是`SIGINT`信号，kill pid和killall是`SIGTERM`信号。
>
> demo对这2个信号的默认处理是程序终止。

在程序中，可以把信号关联到新的handler，那么收到信号后的处理将改变。

```cpp
void handler(int sig);	//信号处理函数

signal(SIGINT, handler);	//关联信号SIGINT和handler
```

## （1）概念

* 信号是宏定义，一共64个信号。SIGINT值是2，SIGTERM值是15。用信号名、信号值都行。

* 信号，也叫软中断，用来通知进程发生了事件。中断，是说明进程哪怕在休眠sleep()也能中断其休眠唤醒。

* 但只能给进程通知发生了某个事件，不能给进程传递数据。

* 进程之间可以通过**kill库函数**互相发送信号。Linux内核也可给进程发送信号，通知进程发生了某个事件。

  ```c
  int kill(pid_t pid, int sig);
  
  
  pid>0，将信号sig发送给进程号为pid的进程
  pid=0，信号传给和调用kill的进程同进程组中所有进程
  pid=-1，将信号广播给系统所有进程。如：系统关机时，给所有登录窗口广播关机信息。
  返回值：0表示成功，-1表示失败，errno设定值
  ```


* 信号递达（Delivery)、信号未决（Pending）

  信号递达——信号产生后到达进程并执行了信号处理函数的过程

  信号未决——信号产生但没有递达

  

## （2）进程对信号的处理，有3种方式：

* **忽略**——对信号不做任何处理，**不产生中断**

  ```c
  signal(SIGINT, SIG_IGN);	//忽略SIGINT信号
  ```

  程序中设置中断处理函数，那么程序收到信号时，中断处理函数将被调用


* Linux系统默认的信号处理

* **`signal`或`sigaction`函数重新设定处理函数**

  ```c
  //用这个
  //功能更强大。多线程中只能用这个，没有signal()函数
  #include<signal.h>
  sigaction(int sigNum, const struct sigaction* act, struct sigaction* oldact);	
  
  struct sigaction
  {
      void (*sa_handler)(int);	//处理函数
      void (*sa_sigaction)(int, siginfo_t*, void*);	
      sigset_t sa_mask;	//信号集合
      int sa_flags;	//标志位   
  }
  /******************************************************/
  sa_flags标志位关注：SA_RESTART
      标志位设为SA_RESTART后，被信号中断的系统调用（如scanf这种）会在信号处理后被重新自动调用
      不设置的话，被中断的系统调用不会重新调用
      
  
  /******************************************************/
  struct sigaction stact;
  memset(&stact, 0, sizeof(stact));	//置0初始化
  stact.sa_handler=hdfunc;	//指定处理函数
  sigaddset(&stact.sa_mask, 15);	//信号15设定阻塞，没有手动解除阻塞，等2处理完毕紧接着处理15。要放在sigaction之前
  sigaction(2, &stact, NULL);	
  sigaction(15, &stact, NULL);	
  ```

  ```c
  #include<signal.h>
  signal(int sigNum, sighandler);
  
  //返回值意义不大			
  //sighandler——信号处理方式，可设为：
      SIG_IGN忽略
      void handler(int)处理
      SIG_DFL恢复为默认处理  
   
  /******************************************************/
  signal(2, hdfunc);	//信号处理函数
  ```


## （3）常用信号

| 信号名  |   信号值   |    发出信号    |                          默认处理                           |
| :-----: | :--------: | :------------: | :---------------------------------------------------------: |
| SIGINT  |     2      | 键盘中断ctrl-c |                          终止进程                           |
| SIGKILL |     9      |    kill -9     |          终止进程、信号不能被捕获、信号不能被忽略           |
| SIGSEGV |     11     | 无效的内存引用 | 内存越界时，Linux内核给进程发core dump，程序就报错core dump |
| SIGTERM |     15     | kill和killall  |                          终止进程                           |
| SIGCHLD | 20，17，18 |   子进程结束   |                            忽略                             |

## （4）实际用法

信号常用，但工作中用法不多。

* **服务程序体面、安全的退出**

  服务程序运行在后台，强行kill不是好办法，因为kill不会释放系统资源，会影响系统稳定。而且，对端也不知道你被kill掉了，还在等待通讯等情况。

  体面而安全的做法：编写信号处理函数，在里面进行资源释放（文件IO关闭、数据库连接关闭、通讯连接关闭等）、通知对端结束通讯等处理。程序就可以有计划的退出。

  ```c
  //demo.cpp
  #include<stdio.h>
  #include<stdlib.h>
  #include<unistd.h>
  #include<signal.h>
  
  FILE* fp=0;
  void EXIT(int sig)
  {
      printf("get signal:%d\n",sig);
     	if(fp!=0)
          fclose(fp);	//释放资源
      exit(0);
  }
  
  int main()
  {
      signal(SIGINT, EXIT);
      signal(SIGTERM, EXIT);
  
      if(fork() > 0)
          exit(0);
  
      while(100)
      {
          printf("execute..\n");
          sleep(1);
      }
      return 0;
  }
  ```

* 网络服务程序抓包

## （5）可靠信号、不可靠信号

1~32信号为不可靠信号，34~64为可靠信号。

* 不可靠信号：

  存在信号丢失问题——进程收到信号不做队列处理，而是相同信号会合并成一个。

  （早期版本Linux内核的signal函数会在对不可靠信号进行一次处理后，handler会恢复成默认处理。现在的Linux内核版本。现在已经没这个情况了。）

  ```c
  #include<stdlib.h>
  #include<unistd.h>
  #include<signal.h>
  
  void handler(int sig)
  {
      printf("sig=%d",sig);
  	for(int j=0;j<10;j++)
      {
      	printf("j(%d)=%d",sig,j);
          sleep(1);
      }
  }
  
  int main()
  {
      //signal(2, handler);
      struct sigaction stact;
      memset(&stact,0,sizeof(stact));
      stact.sa_handler=handler;
      sigaction(2, stact, NULL);
      
      for(int i=0;i<100;i++)
      {
          printf("i=%d",i);
          sleep(1);
      }
      return 0;
  }
  
  //./demo运行
  //多次killall -2 demo发送信号2
  发现最后handler执行次数少于信号2的发送次数
  ```

* 可靠信号

  不会丢失

## （6）信号处理函数被中断

一个信号的处理函数正在执行时，若来了其他信号，会中断正在执行的处理函数，转而执行新信号的处理函数。待新的处理函数执行完，再继续执行之前的处理。

若来的是相同的信号，则会排队阻塞等待当前处理函数执行完。（可靠信号会出现合并现象）

```c
#include<stdlib.h>
#include<unistd.h>
#include<signal.h>

void handler(int sig)
{
    printf("sig=%d",sig);
	for(int j=0;j<10;j++)
    {
    	printf("j(%d)=%d",sig,j);
        sleep(1);
    }
}

int main()
{
    //signal(2, handler);
    //signal(15, handler);
    struct sigaction stact;
    memset(&stact,0,sizeof(stact));
    stact.sa_handler=handler;
    sigaction(2, stact, NULL);
    sigaction(15, stact, NULL);
    
    for(int i=0;i<100;i++)
    {
        printf("i=%d",i);
        sleep(1);
    }
    return 0;
}

//demo运行
//killall -2 demo发送信号2
//在2的handler还未执行完之前，killall -15 demo发送信号15
可见信号2的handler正在执行时，被信号15的handler中断
    
//多次killall -2 demo发送信号2
信号2的handler不会被打断，而是排队，由于2不可靠信号出现合并
```

## （7）信号忽略和阻塞

* 忽略

  进程把递达的信号直接丢弃

  ```cpp
  signal(sig, SIG_IGN);
  ```

* 阻塞

  场景：为了让信号不被丢弃，同时也不会中断当前正在执行的处理函数，将其阻塞，待当前中断处理执行完，再手动结束阻塞`sigprocmask(SIG_BLOCK, &set, NULL)`会立即执行被阻塞的信号的处理函数。

  信号被阻塞在未决状态，不是被丢弃。

  ```c
  sigset_t set;	//创建信号集合
  sigemptyset(&set);	//将信号集合set置空
  sigfillset(&set);	//把全部信号加到信号集合
  sigaddset(&set, 15);	//向集合中添加信号15
  sigdelset(&set, 15);	//从集合中删除信号15
  int sigismember(&set, 15);	//判断信号15是否在信号集合
  sigprocmask(SIG_BLOCK, &set, NULL);	//将信号集合set中所有信号设置为阻塞SIG_BLOCK
  sigprocmask(SIG_UNBLOCK, &set, NULL);	//解除阻塞
  ```

  ```c
  //demo
  #include<stdio.h>
  #include<stdlib.h>
  #include<signal.h>
  #include<stdlib.h>
  #include<unistd.h>
  #include<signal.h>
  
  void handler(int sig)
  {
      printf("sig=%d\n",sig);
  	for(int j=0;j<10;j++)
      {
      	printf("j(%d)=%d\n",sig,j);
          sleep(1);
      }
  }
  
  int main()
  {
      /*
      signal(2, handler);
      signal(15, handler);
      sigset_t set;	
  	sigemptyset(&set);	
  	sigaddset(&set, 15);	
  	sigprocmask(SIG_BLOCK, &set, NULL);	//信号设置为阻塞SIG_BLOCK
     */
      
      struct sigaction stact;
      memset(&stact,0,sizeof(stact));
      stact.sa_handler=handler;
      sigaddset(&stact.sa_mask, 15);	//信号15设定阻塞，没有手动解除阻塞，等2处理完毕紧接着处理15。要放在sigaction之前
      stact.sa_flags=SA_RESTART;
      sigaction(2, &stact, NULL);
      sigaction(15, &stact, NULL);
      
      int a;
      scanf("%d", &a);	//在用户不输入的情况下，若不设定stact.sa_flags=SA_RESTART，信号递达被处理后，scanf等待输入就跳过了
      
      for(int i=0;i<100;i++)
      {
          printf("i=%d\n",i);	
          fflush(stdout);	//printf 调用可能因为缓冲问题而不立即显示输出，而是循环完之后全部一起输出
          sleep(1);
          /*
          if(i>20)	
              sigprocmask(SIG_UNBLOCK, &set, NULL);
           */
      }
      return 0;
  }
  
  //demo运行
  //killall -2 demo立即运行信号2的handler
  //killall -9 demo时不会立即运行信号15的handler，而是当i==21时才运行
  ```

  









# 2. 多线程

---

> 在32位系统中，每个进程对应1个虚拟地址空间，从0开始到4G结束。用户区占3G，1G属于内核。
>
> 程序无法访问物理内存，程序中打印的变量的地址都是虚拟地址空间中的地址，而不是实际在内存的位置。
>
> ![image-20240427134742787](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240427134742787.png)
>
> ![image-20240427135051567](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240427135051567.png)
>
> 多线程调试复杂，线程数够用就行，不要创建太多。

