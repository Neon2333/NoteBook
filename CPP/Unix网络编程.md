# 0. 注意点

---

* 真实项目中，后台程序都是没有交互界面（数据输出：到文件/数据库，程序输出：日志，`tail -f`跟踪），常驻内存中，周期性的或通过事件唤醒的方式执行任务。

  周期性执行任务：每隔几秒扫描某个目录，看有没有要处理的，有的话就读取处理一下，然后将原目录中文件删除或拷贝到专门负责备份的目录。

  ```c
  //demo.cpp
  #include<stdio.h>
  void execTask(){}
  void main()
  {
      while(1)
      {
          execTask();
          sleep(5);
      }
  }
  ```

  为了让demo能够脱离shell在后台运行由系统接管（此时进程的PPID=1），两种方法：

  不脱离shell运行时，ctrl-c就可终止，但后台服务程序能被shell终止是不行的。

  ```shell
  # 方法1
  demo &
  # 方法2
  fork一个子进程，父进程退出，子进程后台继续执行
  
  //demo.cpp
  #include<stdio.h>
  #include<unistd.h>		
  #include<stdlib.h>
  void execTask(){}
  void main()
  {
  	if(fork() > 0) 
  		exit(0);	
      while(1)
      {
          execTask();
          sleep(5);
      }
  }
  
  #	man 2 exit查看头文件
  #	killall demo终止
  ```

  

  事件唤醒：

# 1. socket通信基础

---

## （1）TCP连接

### 头文件

```cpp
sys/types.h：数据类型定义

sys/socket.h：提供socket函数及数据结构

netinet/in.h：定义数据结构sockaddr_in

arpa/inet.h：提供IP地址转换函数

netdb.h：提供设置及获取域名的函数

sys/ioctl.h：提供对I/O控制的函数

sys/poll.h：提供socket等待测试机制的函数
    
unistd.h：提供通用的文件、目录、程序及进程操作的函数
 
errno.h：提供错误号errno的定义，用于错误处理
 
fcntl.h：提供对文件控制的函数
 
time.h：提供有关时间的函数
 
crypt.h：提供使用DES加密算法的加密函数
 
pwd.h：提供对/etc/passwd文件访问的函数
 
shadow.h：提供对/etc/shadow文件访问的函数
 
pthread.h：提供多线程操作的函数
 
signal.h：提供对信号操作的函数
 
sys/wait.h、sys/ipc.h、sys/shm.h：提供进程等待、进程间通讯（IPC）及共享内存的函数
```

### 结构体

```cpp
struct sockaddr
{
    unsigned short sa_family;	//地址类型，AF_XXXX
    char sa_data[14];	//14字节的port和IP
}

//用这个
struct sockaddr_in
{
    short int sin_family;	//地址族IPv4/IPv6（2B）
    unsigned short int sin_port;	//16位端口号
    
    struct in_addr sin_addr;	//封装ip的结构体（重点关注这个成员！）
    
    unsigned char sin_zero[8];	//为了和struct sockaddr保持一样的长度(就可以sockaddr_in和sockaddr互相强转)，全填充0
}

struct in_addr
{
    unsigned long s_addr;	//ip（4B）
}
```

### socket函数

> * 服务端调用listen监听客户端的连接请求，在调用listen之前，客户端不能向服务端发送请求
>
> * 客户端调用connect向服务端发起连接请求
>
> * TCP协议层，客户端通过connect与服务端握手建立连接，将连接放入设置好长度的连接队列
>
> * 服务端调用accept从队列中取一个已建立的连接，并返回一个新的socket即clientfd，用于和客户端收发数据。listenfd只负责监听客户端的连接
>
>   ![image-20240619221253845](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240619221253845.png)
>
>   * 服务端会维护2个队列，一个存放半连接状态的连接（收到客户端发的SYN，进入到SYN_RECV状态的socket），一个存放建立TCP连接的socket，也就是到ESTABLISHED的socket。
>
>   * 第二个队列的长度由listen()的第二个参数指定，指定能够ESTABLISHED的连接个数。
>
>     ```bash
>     netstat -na | grep 5000	#查看5000端口连接
>     ```
>
>     第一个队列长度由`/proc/sys/net/ipv4/tcp_max_syn_backlog`配置文件指定
>
>     ```bash
>     cat /proc/sys/net/ipv4/tcp_max_syn_backlog
>     ```
>
>     

```cpp
//向系统申请创建socket。成功返回描述符fd，失败返回-1（除非系统资源耗尽，一般不会失败），错误原因存于errno
#include<stdlib.h>
#include<stdio.h>
#include<sys/socket.h>
int socket(domain, tpye, 0);	
//PF_INET——IPv4,PF_INET——IPv6
//SOCK_STREAM-TCP,SOCK_DGRAM-UDP
```

```cpp
//bash：man 2 bind
#include<sys/socket.h>
int bind(int sock, struct sockaddr* server_addr, socklen_t addrlen);	//成功返回0，失败返回-1

//注意第2个参数是struct sockaddr*，保存ip+port的结构体是sockaddr_in，要强转一下
//同一个地址只能bind一次
//用户可用port范围：不超过65535（short int)
//服务端kill掉立即重启，端口释放后可能处在TIME_WAIT，要等2Min才能被使用。要想释放后立即就能使用，要设置SO_REUSEADDR
int opt=1;
int len=sizeof(opt);
setsockopt(listenfd, SOL_SOCKET, SO_REUSEADDR, &opt, len);
```

```cpp
#include<sys/socket.h>
int listen(int sock, int backlog);	//成功返回0，失败返回-1

//backlog——服务器接受的客户端connect请求队列的长度。当有多个客户端同时请求时，服务端无法对所有进行处理，设置队列将请求放入其中，backlog设置队列长度。backlog=10，若来20个请求，则后面10个会被server拒绝不会给对应的client发送rst，这10个client将继续发送TCP SYN，带队列中有请求通过三次握手建立了TCP连接将会从队列中移到另一个队列中。空出位置才能供后续请求移入。
```

```cpp
//accept从请求队列里取clientfd，队列为空时会阻塞等待
#include<sys/socket.h>
int accept(int sock, struct sockaddr* client_addr, socklen_t* addrlen);	//成功返回文件描述符fd，失败返回-1
```

```cpp
int connect(int sock, struct sockaddr* server_addr,socklen_t addrlen);	//成功返回0，失败返回-1
```

```cpp
int send(int sockfd, const void* buffer, size_t len, 0);
//buffer是要发送数据内存的地址，可以是基本类型的地址，也可以是数组、结构体、字符串，内存里有什么就发什么。
//结构体中成员如果有大于1字节的都要转成网络字节序。
//len-实际要发送的字节数，不是buffer的长度，不能超过buffer长度
//返回实际已发送的字节数（假如len==100，你要发100字节，但只发了50，就返回50），失败返回-1，错误信息被errno标记
//
//send的缓冲区满了，也会阻塞
```

```cpp
int recv(int sockfd, const void* buffer, size_t len, 0); 
//len-想接收的字节数
//对端没有发送数据，阻塞。
//对端发送了数据，则返回接收到的字节数。
//出错返回-1
//socket被对端关闭则返回0。
```

### C函数

```cpp
//打开文件（Unix系统API）
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
int open(const char* filePath, int flag);	//成功返回文件描述符fd，失败返回-1

int fd = open("C:\\Users\\25224\\Desktop\\test.dat",O_RDONLY);
```

![image-20210821220713441](https://i.loli.net/2021/08/21/Ehwz9ZHdvrbUeDa.png)

```cpp
//打开文件，返回流
#include <stdio.h>
FILE* fopen(const char *filename, const char *mode);

FILE* fstream = fopen("a.txt","t+");	//允许对文本文件读写

r(read): 只读，文件必须存在
w(write): 只写，创建空文件，若同名文件已存在则原文件被删除
a(append): 追加，若文件不存在则创建
t(text): 文本文件，可省略不写
b(binary): [二进制文件](https://baike.baidu.com/item/二进制文件)
+: 读和写
返回值——该函数返回一个 FILE 指针。否则返回 NULL，且设置全局变量 errno 来标识错误。
```

```cpp
//从输入流中读取数据到内存buf中
#include <stdio.h>
size_t fread(void *buf, size_t size, size_t nmemb, FILE *fstream);

* **buf**——一块内存的地址，有nmemb个元素，每个元素的长度为size（字节）
* **size**——内存块中元素的长度（B为单位）
* **nmemb**——内存块中元素的个数
* **fstream**——文件指针，指定了一个输入流（[详见C语言基于流式文件和IO文件的操作](https://blog.csdn.net/qq_15821725/article/details/78929344)）
* **返回值**——成功读取的***元素总数***会以 size_t 对象返回，size_t 对象是一个整型数据类型。如果总数与 nmemb 参数不同，则可能发生了一个**错误**或者***到达了文件末尾（文件实际大小小于nmemb）***


FILE* fp = fopen("server.cpp","rb");
char buf[BUF_SIZE];
fread(buf, 1, BUF_SIZE, fp);	//将server.cpp文件以二进制流读取至buf中
```

```cpp
//将内存buf中的数据写入到输出流中
size_t fwrite(const void *buf, size_t size, size_t nmemb, FILE *stream);

- **buf——**这是指向要被写入的元素数组的指针。
- **size——**这是要被写入的每个元素的大小，以字节为单位。
- **返回值——**如果成功，该函数返回一个 size_t 对象，表示**实际写入输出流中元素的总数**，该对象是一个整型数据类型。如果该数字与 nmemb 参数不同，则会显示一个错误
```

```cpp
//关闭流 stream，刷新所有的缓冲区。在刷新缓冲区之前，所有读写的数据都存放于缓冲区，调用fclose后才写入硬盘*
#include <stdio.h>
int fclose(FILE *stream);

返回值——成功关闭，则该方法返回零。如果失败，则返回 EOF
```

```cpp
write时将数据写入输出缓冲区，随后由内核通过TCP等协议将输出缓冲区中数据发送到对端的输入缓冲区
write并不是数据发送到对端后才返回值，而是将数据写入到输出缓冲区后就返回
    
//写入
#include<unistd.h>
ssize_t write(int fd, const void* buffer, size_t nbytes);	//成功返回写入的字节数，失败返回-1
//nbytes-把参数buf所指的内存写入nbytes个字节到参数fd所指的文件内。设为buffer的实际大小strlen()
//如果顺利write()会返回实际写入的字节数（len）。当有错误发生时则返回-1，错误代码存入全局变量errno中。
```

```cpp
read从输入缓冲区中读取数据，数据如何通过网络环境从对端到达本地的输入缓冲区是由内核通过TCP等协议控制的
    
//读取
#include<unistd.h>
ssize_t read(int fd, void* buf, size_t nbytes);	
//nbytes-最多读取nbytes个字节。设为buffer的capacity。
//顺利 read()会返回实际读到的字节数, 最好能将返回值与参数count 作比较。当有错误发生时则返回-1, 错误代码存入errno 中, 而文件读写位置则无法预期
```

```c
//将str写入buffer，返回写入的字节数
int sprintf(char* buffer, const char* str, ...);

char buffer[1024];
int a = 0;
sprintf(buffer, "这是一个test=%d", a);
```

```c
//向str写入size个字符
int snprintf(char *str, size_t size, const char *format, ...);
//snprintf 返回成功写入的字节数，不包括最后的空字符（\0）
```

```cpp
printf();

//%d：十进制整数
//%f：浮点数（默认6位小数）
//%s：字符串
//%c：字符
//长整型（%ld）
//双精度浮点数（%lf）
```

```cpp
//给连续内存buffer赋值value，返回指向该连续内存的指针
<string.h>	//C
#include<cstring>	//C++
void* memset(void* buffer, int value, size_t len);

memset的第二个参数是要填充的字节值，而不是整数。

```

```cpp
#include<string.h>	//C
#include<cstring>	//C++
void* memcpy(void* str1, const void* str2, size_t n);	//从存储区 str2 复制 n 个字节到存储区 str1
//该函数返回一个指向目标存储区 str1 的指针
```

```cpp
<stdio.h>

void perror(const char* str);
//它的作用是将当前的错误消息打印到标准错误输出（stderr）。这个函数会先打印一个错误消息的前缀，通常是 "Error" 或 "perror: "，然后是程序提供的字符串，最后是当前errno变量的值对应的错误描述。
```

```cpp
<unistd.h>
sleep(int sec);
usleep(int millsec);
```

### 常用指令

```bash
netstat -anp	#查看进程网络状态和PID
gdb:
p/x 二进制打印
x/4tb num	#x访问内存，4t表示4字节，b表示二进制显示 
layout src  可视化
ctrl-p 上一条命令
ctrl-p 下一条命令
```



### 网络字节序

> 字节顺序，是长度大于1个字节的数据的每个字节在内存中的存放顺序。
>
> **网络字节序采用大端序。**
>
> 1个字节的数据不存在字节序问题。
>
> 不同主机之间可能存在字节序不同的问题。同一个主机上的不同程序之间的通信也可能采用的字节序不同。因此网络通信最好统一为网络字节序。

* CPU向**内存**中写入数据/解析内存中数据存在2种方式：小端序(little-endian)、大端序(big-endian)

  **小端序从起始地址按照先存低字节再存高字节的顺序。大端序将高字节数据存放于起始地址。**

  如：0x1234这是2字节数据，高字节是12，低字节是34（书写：从左到右，高位到低位）。大端序存储于内存中时，在从低到高字节内存中分别存储的是12、34；在小端序中存储时为34、12

* 主机**发送数据**和**接收数据**，都是从内存中的低字节向高字节。

  因此，大端序主机发送0x1234时从高字节开始发，先发送12后发送34。小端序主机接收后从低字节开始存，将12放到低字节内存，34放到高字节内存。所以小端序主机，读数据从低地址到高地址读，读出12作为低字节，读出34作为高字节，最终会变为0x3412。

* 字节序转换

  理论上，大端序的主机不需要经过转换，但实际中Intel和AMD的CPU的主机字节序都是小端序，故都需要经过主机字节序到网络字节序的转换。

  h-主机序，n-网络序

  ```cpp
  //将4字节无符长整形数据从主机字节序转换为网络字节序
  #include <apra/inet.h>
  uint32_t htonl(uint32_t hostlong);
  
  //2字节无符短整型，转成网络序
  uint16_t htons(uint16_t hostshort);
  
  //将4字节数据从网络字节序转化为主机字节序    
  ntohl();
  
  //2字节网络序转成主机序
  ntohs
  ```

### 字符串IP与整型互转

点分十进制IP是字符串形式，需要函数将字符串形式的IP转换为32位整型数据才能绑定地址。

```cpp
//字符串转整型
#include <stdlib.h>
int atoi(const char* str);	//成功时返回整型，失败返回0

//示例：
server_addr.sin_addr.s_addr = htonl(atoi("127.0.0.1"));
```

```cpp
//整型转字符串
char* itoa (int value, char* str, int base );	
//value-待转整型数
//str-存储转换后的字符数组
//base-转换后的进制
//成功时返回指向str的指针，无错误返回


//示例：十进制转二进制
#include<cstdlib>
#include<cstdio>
int main()
{
	int num = 10;
	char str[100];
	int n = atoi(itoa(num, str, 2));	//先把num转换为二进制的字符串，再把该字符串转换为整数
	printf("%d\n",n);
	return 0;
}
```

```cpp
struct in_addr inet_addr(const char* str);	//成功时返回整型网络字节序IP，失败返回INADDR_NONE
* 将字符串形式的点分十进制IP，转换成32位整型形式，且同时转换成网络字节序
* 具有自动检测str是否是合法IP地址的功能
* 只能转IPv4
    
inet_aton——将点分十进制字符串IP转成网络字节序二进制存储在struct in_addr变量中
int inet_aton(const char* str, struct in_addr* addr);	//成功返回true，失败返回false

inet_ntoa——将 二进制网络字节序转成点分十进制字符串IP
char* inet_ntoa(struct in_addr addr);	//成功返回字符串的地址，失败返回-1

printf("客户端%s已连接..",inet_ntoa(clientfd.sin_addr));
```

```cpp
inet_pton——点分十进制字符串转为二进制网络字节序IP
int inet_pton(int af_family, const char *str, void *addr);	//成功返回1，出错返回-1，af不是有效表达式返回0

inet_ntop——二进制网络字节序IP转成点分十进制字符串IP
const char* inet_ntop(int af_family， const void* addr, char* str, size_t len_str)	//成功返回str，失败返回NULL
```

### demo

* server通过bind()将所有网卡的地址INNADR_ANY绑定到监听套接字listenfd
* 通过listen()设置**服务端等待队列大小**，并监听
* client调用connect，取自身IP和**随机取端口号**作为client_addr，将连接套接字sockfd注册到server的等待队列后connect返回（servre调用listen后，客户端才能调用connect）
* server调用accept从等待队列中抽取一个client套接字clientfd，此时server和client的TCP连接建立
* servre和client通过clientfd读写数据

```cpp
//gdb调试
g++ -g -o server server.cpp
gdb server
set args 5000
b 50
run
```

```cpp
/*
	server
	socket->bind->listen->accept->read/write->close
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <errno.h>

int main(int argc, char *argv[])
{
    //参数ip port
    if(argc!=2){
		printf("Usage : %s <port>\n", argv[0]);
		exit(1);
	}
    
    //创建监听socket
    int listenfd;
	listenfd = socket(PF_INET, SOCK_STREAM, 0);
	if(listenfd == -1)	 
    {
        perror("socket");
	    return -1;
    }
	
    //指定协议、ip、端口
    struct sockaddr_in server_addr;
	memset(&server_addr, 0, sizeof(server_addr));
	server_addr.sin_family=AF_INET;
	//server_addr.sin_addr.s_addr=htonl(INADDR_ANY);	//INADDR_ANY表示所有网卡都监听（服务器一般不止一块网卡）
   	server_addr.sin_addr.s_addr = inet_addr("127.0.0.1");	//inet_addr将字符ip转整型，且转网络字节序
	server_addr.sin_port=htons(atoi(argv[1]));
	
    //bind
	if(bind(listenfd, (struct sockaddr*) &server_addr, sizeof(server_addr))==-1 )
    {
        perror("bind");
        close(listenfd);
        return -1;
    }
	
    //监听，最多允许5个client连接
	if(listen(listenfd, 5)==-1)
    {
        perror("listen");
        close(listenfd);
        return -1;
    }
	
    struct sockaddr_in client_addr;
	int client_addr_size;
	client_addr_size=sizeof(client_addr);  

    while(1)
    {
        //接收client连接，获取连接fd
        int clientfd;
		if((clientfd = accept(listenfd, (struct sockaddr*)&client_addr,(socklen_t*)&client_addr_size)) < 0)
            continue;
         printf("客户端(%s)已连接socket=%d..\n",inet_ntoa(client_addr.sin_addr),clientfd);
    }
	
    //数据收发
    char buffer[1024];	//缓冲区
    while(1)
    {
   		memset(buffer,0,sizeof(buffer));
    	int msgCount=0;		//接受报文字节数
    	if((msgCount = recv(clientfd, buffer, sizeof(buffer), 0))<=0)	//接收client报文
    	{
        	printf("msgCount=%d\n",msgCount);
        	break;
    	}
        printf("msgCount=%d,接收：%s\n", msgCount, buffer);	//打印接收报文
        
        strcpy(buffer, "ok");	
        if((msgCount = send(clientfd, buffer, strlen(buffer), 0)) <= 0)	//发送响应ok
        {
            perror("send");
            break;
        }
        printf("发送：%s\n", buffer);
    }
  
	close(clientfd);	
	close(listenfd);
	return 0;
}
```

```cpp
/*
	client
	socket->connect->read/write->close
*/

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <errno.h>
#include <netdb.h>

int main(int argc, char *argv[])
{
    //指定要连接的server的ip、port
	struct sockaddr_in server_addr;
	memset(&server_addr,0,sizeof(server_addr));
	server_addr.sin_family=AF_INET;
    //由域名解析出服务器IP
	struct hostent* h;
	if((h = gethostbyname(argv[1])) == 0)	//由域名获得ip
	{
    	perror("gethostbyname..");
	    close(sockfd);	//client
    	return -1；
	}
	//memcpy(&server_addr.sin_addr, &h->h_addr, h->h_length);	//h_addr宏定义为ip列表第一个元素
    server_addr.sin_addr.s_addr=inet_addr(argv[1]);
	server_addr.sin_port=htons(atoi(argv[2]));
	
	//创建socket
    int sockfd;
    sockfd = socket(PF_INET, SOCK_STREAM, 0);
    if(sockfd==-1)
    {
        perror("socket");
        return -1;
    }
   	//连接server
	if(connect(sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr)) == -1)
    {
        perror("connect");
        close(sockfd);
    }
    
    //数据收发
    //向server发送一个字符串，等待回复并打印，再发送下一个
    char buffer[1024];
    
    for(int i=0;i<5;i++)
    {
        //发送给server
        int msgCount=0;
        memset(buffer,0,sizeof(buffer));
        sprintf(buffer, "这是来自client的第%d个消息", i+1);
        if((msgCount = send(sockfd, buffer, strlen(buffer), 0)) <= 0)	
        {
		   perror("send");
            break;
        }
        printf("发送了：%s\n", buffer);
        
        //接收server的回应
        memset(buffer, 0 ,sizeof(buffer));
        if((msgCount = recv(sockfd, buffer, sizeof(buffer), 0)) <= 0)	
        {
            printf("msgCount=%d", msgCount);
            break;
        }
        printf("接收了%s\n", buffer);
        
    }
   
    sleep(3);
}
```

### 细节问题

* listenfd（server）或sockfd（client)运行从3开始，012分别是标准输入输出错误。

* 一个程序里可创建的socket数：1024， Unix限制打开文件数量1024。

  用`ulimit -a`命令查看

* 服务端kill掉立即重启，端口释放后可能处在TIME_WAIT，要等2Min才能被使用。要想释放后立即就能使用，要设置SO_REUSEADDR
  ```c
  int opt=1;
  int len=sizeof(opt);
  setsockopt(listenfd, SOL_SOCKET, SO_REUSEADDR, &opt, len);
  ```


## （2）数据收发

> read/write/recv/send

### 字符数组

* 初始化

  ```c
  memset(buffer,0,sizeof(buffer));
  ```

* 拷贝内存数据

  ```c
  memcpy(buffer, source);
  ```

* 赋值字符串

  ```c
  sprintf(buffer, "这是第%d个字符串",i+1);
  snprintf(buffer,10,"这是第%d个字符串",i+1);	//向buffer中拷贝10个字符串
  ```

* 拼接字符串

  ```c
  strcat(buffer, "ok");	//在buffer后加一个字符串ok
  ```

  



### strlen()和sizeof()

***strlen获取字符数组实际字节数size。sizeof获取字符数组开辟的空间capacity。***

### send

![image-20211012151257433](https://i.loli.net/2021/10/12/7suYTyHDxAcWG5I.png)

### recv

![image-20211012151209115](https://i.loli.net/2021/10/12/8FLvI5lgmdJCscQ.png)

### int与char强转的底层

指针：存储一块连续内存区域的首地址的变量

内存实质上是字节数组，所谓类型是程序中规定的读取内存的方式

变量的类型不同实际上是读取内存的长度不同，是从不同的角度看内存内的数据。变量=内存地址+数据。指针变量=内存地址+偏移。

对指针类型的强转，实际上是将内存使用的位数改变了

```c++
int a;
int* p1 = &a;	//p1指向32位连续内存
*p1 = 1;	//00000000 00000000 00000000 00000001
int* p2 = (char*)&a;	//p2指向8位连续内存
*p2 = 1;	//00000001
```

> 首先，要知道一件事情：你在代码里写的事情，计算机未必会干。
>
> 比如：
>
> ```text
> char var = 'a';
> var = (int) var;
> ```
>
> 没准人家编译器看你没有产生实际效用，就直接给砍了。
>
> 更严格来讲，你应当写：
>
> ```text
> char var1 = 'a';
> int var2 = (int) var1;
> ```
>
> 这就有点实际意义了。
>
> > 一直对基本类型的cast疑惑不解
>
> C的基本类型，大都能对应到CPU能处理的标准数字类型。char基本都是一个8位有符号整数，最高位是符号位。int是不低于16位的有符号整数（所有的常见平台上都是32位），最高位是符号位。
>
> 那么进行这种转换，基本上就是编译器把这件事情翻译成一个或者几个CPU指令，大概是：
>
> - 从地址var1读内容到寄存器；
> - 执行8位到32位的有符号整数转换；
> - 将结果从寄存器写到地址var2，写32位。
>
> 对于ASCII，a的数值是97，用八位的二进制数表示，就是01100001。把它转换到int，实际上就是在求一个32位有符号的值，数值意义和01100001要相等。编译器会把你这个表达式翻译成对应的CPU操作。结果是00000000 00000000 00000000 01100001。
>
> 看起来好像除了填上一堆零，啥都没干？
>
> 实际上填零是重要操作，因为内存空间可以是脏的：在你给丫赋值之前，丫可能还带着上次计算完之后的垃圾。
>
> 而且，如果是负数，就会很不一样。比如：
>
> ```text
> char var1 = -1;
> int var2 = (int) var1;
> ```
>
> 由于现代计算机几乎全都用补码来表示负数，于是负一的8位二进制表示，是11111111；而32位表示，则是11111111 11111111 11111111 11111111。不难看到，对于有符号数的长度转换，并不总是填零的。
>
> 所以CPU在这里面，还是需要做一些微小的工作。
>
> 补充运行时类型的问题：
>
> C这种语言，要的就是运行时不带类型记号的裸奔快感。比如对于类似这样的源代码：
>
> ```text
> int a = some_procedure();
> int b = some_other_procedure();
> int c = a + b;
> ```
>
> 最后的那个相加，在不优化的情况下，通常会产生类似这样的几个指令：
>
> - 从地址a取32位数据到寄存器1；
> - 从地址b取32位数据到寄存器2；
> - 执行32位整形加法；
> - 将32位数据写到地址c；
>
> 可以看到，**这里面没有类型。对数据的操作方式就是类型。（取数据的位数）**

> 数据类型与类型转换：
>
> **数据类型——决定了数据在内存中所占的Bit，和解析方式**
>
> **强转——决定了被强转数据从低位开始截取的Bit位数**
>
> **char和int在内存中都是以二进制存储，char对应的二进制数按照十进制/d解析就是字符在ASCII中序号。如'a'对应97，'9'对应57**
>
> **char强转为int时，将8Bit数据拷贝到32Bit内存的低8Bit。打印int变量时显示的是char字符在ASCII中序号**
>
> **int强转char时，截取最低8Bit数据拷贝到另一内存的8Bit。打印char时是int的最低8Bit对应数字作为序号在ASCII中的字符**
>
> ```c
> int a = 9;
> char ch = a;
> p ch	//9 '\t'
> p/c ch	//'\t'
> //用char变量去接int变量时，直接把9存到char中，实际我们想要的存的是'9'在ASCII中的序号，而现在我们存的是'\t'的序号9。
> 
> char ch1 = 'a';	
> p/t ch1	//00100001
> p ch1 //97
> int i1 = ch1;
> p/t i1	//00000000 00000000 00000000 00100001
> p i1	//97
>     
> char ch = '9';
> char ch_ = ch - '0';
> p ch_	//9，在ASCII中对应字符'\t'
> p/t ch_		//00001001
> int i1 = (int)ch_
> p i1	//9
> p/t i1	//00000000 00000000 00000000 00001001
> ```
>
> '0'~'9'转成int的方法：
>
> ```c
> char ch = '9';
> int num = (int)(ch - '0');	//num=9
> int num_ = (int)ch - 0;		//num_=57 - 0
> ```
>
> ```cpp
> //atoi() C实现：
> #include <ctype.h>
> #include <stdio.h>
> int atoi (char s[]);
> int main(void )
> {
> 	char s[100];
> 	gets(s);
> 	printf("integer=%d\n",atoi(s));
> 	return 0;
> }
> int atoi (char s[])
> {
> 	int i,n,sign;
> 	for(i=0;isspace(s[i]);i++)	//跳过空白符;
>     {
>         sign=(s[i]=='-')?-1:1;		//sign标记正负号
> 		if(s[i]=='+'||s[i]==' -')	//跳过符号
>   			i++;
> 		for(n=0;isdigit(s[i]);i++)
>        		n=10*n+(s[i]-'0');	//将数字字符转换成整形数字
>     }
> 	return sign *n;
> }
> ```

## （3）连接半关闭

> close函数将会完全关闭连接，一旦关闭，在当前端所有读写函数都会无法使用，也就无法再收发数据。
>
> 只“关闭一半的连接”，使得断开连接后还能收或发数据，如：
>
> 服务端将本地文件以二进制形式发送到客户端，发送完毕后，服务端断开连接。断开连接后，客户端接收到EOF后需要向服务端发送一个“data has been received!!”
>
> **为了实现上述情景，需要服务端断开连接后虽然不再发送数据，但是还要能接收数据。**

```cpp
#include<sys/socket.h>
shutdown(int sock, int mode);

//mode——断开方式
SHUT_RD——只断开读连接
SHUT_WR——只断开写连接
返回值——成功返回0；失败返回-1
```

## （4）DNS解析

```cpp
struct hostent{
 char* h_name;		//官方域名
 char** h_aliases;	//除官方域名外的其他域名
 int h_addrtype;		//地址类型：AF_INET/AF_INET6
 int h_length;		//IP地址长度：IPv4——4，IPv6——16
 char** h_addr_list;	//IP地址列表（已按照网络字节序存储）
}
#define h_addr h_addr_list[0];	//h_addr宏定义为ip列表第一个
```

```cpp
//把字符串域名或字符串ip，转成网络字节序的ip
#include<netdb.h>
struct hostent* gethostbyname(const char* host);
//失败时返回NULL
//host——主机域名
//hostent——存储域相关信息的结构体
struct hostent* h;
if((h = gethostbyname(argv[1])) == 0)
{
    perror("gethostbyname..");
    close(sockdf);	//client
    return -1;
}
memcpy(server_addr.sin_addr, h->h_addr, h->h_length);
```

## （5）socket缓冲区

* 协议层收发数据有缓冲区
* socket层通过send()、recv()读写数据也有缓冲区
* recv()在缓冲区中无数据时会阻塞，send()在缓冲区满时也会阻塞，导致发送停止。只有对端通过recv()取走一些数据后，send()才会解除阻塞继续发送。

## （6）TCP报文分包和粘包

粘包：由于TCP协议传输数据无边界，即write的数据长度若不够一个报文段，则会等若干次write的数据后一起发送。发送两个字符串"hello"和"world"，对端却一次性收到"helloworld"

分包：若write的数据长度超出一个报文段，则会将数据分割为若干报文段，多次发送。发送"helloworld"，对端却收到2个字符串"hello"和"world"

但是TCP会保证包的顺序不发生改变，并且分的包中间不会插入其他数据。

实际开发中，为了解决分包和粘包问题，一般是自定义协议，最常用方法是：**报文长度+报文内容**。

报文长度ascii码或二进制整数：

```cpp
0010helloworld	//ascii码表示长度10
最多到9999
    
ascii码好处是直接作为字符写入buffer，二进制整数需要转成网络字节序然后通过memcpy写入
```

其他常用方法还有：

> 1. **固定长度的消息**：如果每个消息的长度都是固定的，那么接收方可以简单地读取固定数量的字节来获取一个完整的消息。
> 2. **消息头和消息体**：在每个消息的开始添加一个消息头，消息头中包含消息的长度信息。接收方首先读取消息头以确定需要读取的消息长度，然后再根据这个长度读取消息体。
> 3. **特殊分隔符**：在消息之间插入一个特殊的分隔符，这样接收方就可以通过查找这个分隔符来确定消息的边界。这种方法适用于文本数据。
> 4. **消息结束标志**：在每个消息的末尾添加一个特定的结束标志，这样接收方就可以通过查找这个标志来确定消息的结束。
> 5. **心跳包**：定期发送心跳包，以确保连接的活跃性，同时心跳包也可以作为消息的边界。
> 6. **应用层协议**：设计一个应用层协议，明确定义消息的开始和结束，以及如何处理粘包问题。
> 7. **使用消息队列**：在接收方使用消息队列来缓存接收到的数据，然后通过应用层逻辑来处理队列中的数据，以确定消息的边界。
> 8. **调整TCP接收缓冲区大小**：适当调整TCP接收缓冲区的大小，以减少粘包的可能性。
> 9. **使用应用层的协议栈**：例如使用HTTP协议，它通过请求和响应的格式来解决粘包问题。
> 10. **使用更高级的通信协议**：比如WebSocket，它在应用层提供了全双工通信能力，并且能够更好地处理消息边界。

# 2. socket函数封装

---

> 为了简化代码编写，将socket的流程和函数进行封装。

### （1）封装recv和write

解决recv和send可能不能接收或发送指定n个字节的数据

```c
//从准备好的sockfd中读取n个字节（不读取到n个不返回true）
//sockfd-准备好的socket连接
//n-本次要接收的数据的字节数
//返回值：成功读取n个字节返回true，socket连接不可用返回false
bool Readn(const int sockfd, char* buffer, const size_t n)
{
    int nleft,nread,idx;
    nleft=n;
    nread=0;
    idx=0;
    while(nleft>0)
    {
        if((nread == recv(sockfd, buffer+idx, nleft, 0) <= 0))
	        return false;
	    nleft-=nread;
    	idx+=nread;
    }
    return true;
}

//向准备好的sockfd中写入n个字节（不写入n个不返回true）
bool Writen(const int sockfd, char* buffer, const size_t n)
{
    int nleft,nwrite,idx;
    nleft=n;
    nwrite=0;
    idx=0;
    while(nleft>0)
    {
        if((nwrite == send(sockfd, buffer+idx, nleft, 0) <= 0))
	        return false;
	    nleft-=nwrite;
    	idx+=nwrite;
    }
    return true;
}
```

### （2）解决分包和粘包

规定协议：**报文长度+报文内容**

长度用二进制4字节整型，整个TCP报文长度：4+报文内容的长度

接收数据需要有个`timeout`参数，因为等待接收不可能无限期等下去。

```cpp
//接收对端发送的数据
//sockfd-获得的客户端连接sock
//ibuflen-本次成功接收到的字节数
//timeout-接收等待超时的时间，单位：秒，默认是0-无限等待	
//返回值：true-成功，false-失败（2种情况：等待超时、socket连接不可用）
bool TcpRead(const int sockfd, char* buffer, int* ibuflen, const int itimeout=0)
{
    if(sockfd==-1)	return false;
    
    //IO复用实现超时机制
    fd_set tmpfd;
    
    FD_ZERO(&tmpfd);
    FD_SET(sockfd,&tmpfd);
    
    struct timeval timeout;
    timeout.tv_sec=itimeout;
    timeout.tv_usec=0;
    
    int i;
    if((i=select(sockfd+1,0,&tmpfd,0,&timeout))<=0)	return false;
    
    (*ibuflen)=0;
    if(Readn(sockfd,(char*)ibuflen,4)==false)	return false;	//读取报头4字节
    (*ibuflen)=ntohl(ibuflen);	//转主机字节序，获得数据长度
    
    if(Readn(sockfd, buffer, (*buflen))==false)	return false;
    return true;
}

//向对端发送数据
//sockfd-获得的客户端连接sock
//ibuflen-想要发送的字节数。若发送的是ascii字符串，ibuflen取0，默认发送字符串。若发送二进制数据，ibuflen设为二进制数据块大小
//返回值：true-成功，false-失败（表示socket连接已不可用）
bool TcpWrite(const int sockfd, const char* buffer, const int ibuflen=0)
{
    if(sockfd==-1)	return false;
    //IO复用实现超时机制
    fd_set tmpfd;
    
    FD_ZERO(&tmpfd);
    FD_SET(sockfd,&tmpfd);
    
    struct timeval timeout;
    timeout.tv_sec=5;
    timeout.tv_usec=0;
    
    if(select(sockfd+1,0,&tmpfd,0,&timeout)<=0)	return false;
    
    int ilen=0;
    if(ibuflen==0)	ilen=strlen(buffer);    //ibuflen==0，发送的是字符串，采用字符串长度
    else ilen=ibuflen;	//发送的是二进制数据
    
    int ilenn=htonl(ilen);	//ilen转为网络字节序
    
    char strBufferTmp[ilen+4];	//存放组装的数据报
    memset(strBufferTmp,0,sizeof(strBufferTmp));
    memcpy(strBufferTmp,&ilenn,4);	//拷入ilenn
    memcpy(strBufferTmp+4,&buffer,ilen);	//拷入buffer中数据
    
    if(Writen(sockfd, strBufferTmp, ilen+4) == false)	return false;
    return true;
}
```

### （3）TcpServer类和TcpClient类

* 构造初始化一些参数，析构释放一些资源。

* 客户端的超时时间以60s~90s，网络管理员设定的交换机相关时间参数为90s。

* 在 C++ 中，函数的参数默认值只能在函数原型声明时设置一次，而不能在函数定义时再次设置

  TcpServer.h:30:10：这是函数原型声明的位置，它位于 TcpServer.h 文件的第 30 行，bool Read(char* buffer, const int timeout=0); 这里为 timeout 参数提供了默认值。

  TcpServer.cpp:1：这是函数定义的位置，编译器在这里检测到了重复的默认参数值。

  为了解决这个问题，你需要确保函数的默认参数值只在一个地方定义，通常是在函数原型声明时。

```cpp
//TcpClient.h
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <errno.h>
#include <sys/select.h>
#include <netdb.h>
class TcpClient
{
private:
    int m_sockfd;
    char m_ip[21]; 
    int m_port;
    bool m_btimeout;    //
    int m_bufferlen;
public:
    TcpClient();
    ~TcpClient();

    bool ConnectServer(const char* ip, int port);
    
    bool Readn(const int sockfd, char* buffer, const size_t n);
    bool Writen(const int sockfd, char* buffer, const size_t n);
    bool TcpRead(int sockfd, char* buffer, int* ibuflen, const int timeout=0);
    bool TcpWrite(int sockfd, char* buffer, int* ibuflen=0);
    
    bool Read(char* buffer, const int timeout);
    bool Write(char* buffer, int ibuflen=0);

    void Close();

};

```

```c
//TcpClient.cpp
#include "TcpClient.h"

TcpClient::TcpClient()
{
    m_sockfd=-1;
    memset(m_ip, 0, sizeof(m_ip));
    m_port=0;
    m_btimeout=false;
    m_bufferlen=0;
}

TcpClient::~TcpClient()
{
    Close();
}


bool TcpClient::ConnectServer(const char* ip, int port)
{
    if(m_sockfd != -1)
    {
        Close();
    }

    //memcpy(m_ip, ip, sizeof(ip));
    strcpy(m_ip,ip);
    m_port=port;

    struct sockaddr_in server_addr;
    memset(&server_addr,0,sizeof(server_addr));
	server_addr.sin_family=AF_INET;

	struct hostent* host;
	if(!(host = gethostbyname(m_ip)))	//
	{
	    Close();
    	return false;
	}

	//memcpy(&server_addr.sin_addr, &host->h_addr, host->h_length);	
	server_addr.sin_addr.s_addr=inet_addr(ip);	
	server_addr.sin_port=htons(m_port);
	
    m_sockfd = socket(PF_INET, SOCK_STREAM, 0);
    if(m_sockfd==-1)
    {
        return false;
    }

 
	if(connect(m_sockfd, (struct sockaddr*)&server_addr, sizeof(server_addr)) == -1)
    {
        Close();
        return false;
    }

    return true;
}

bool TcpClient::Readn(const int sockfd, char* buffer, const size_t n)
{
        if(sockfd==-1 || NULL==buffer) return false;
        int nleft,nread,idx;
        nleft=n;
        nread=0;
        idx=0;
        while(nleft>0)
        {
            if((nread = recv(sockfd, buffer+idx, nleft, 0))<=0)
	        {
		        fprintf(stderr, "Error: %s\n", strerror(errno));
	            return false;
	        }
	        nleft-=nread;
    	    idx+=nread;
        }
        return true;
}

 bool TcpClient::Writen(const int sockfd, char* buffer, const size_t n)
{
        if(sockfd==-1 || NULL==buffer) return false;
        int nleft,nwrite,idx;
        nleft=n;
        nwrite=0;
        idx=0;

    while(nleft>0)
    {
        if((nwrite = send(sockfd, buffer+idx, nleft, 0))<=0)
	   {
		    fprintf(stderr, "Error: %s\n", strerror(errno));
	        return false;
	    }
	        nleft-=nwrite;
    	    idx+=nwrite;
    }
    return true;
}


bool TcpClient::TcpRead(int sockfd, char* buffer, int* ibuflen, const int timeout)
{
        if(sockfd==-1 || NULL==buffer || NULL==ibuflen)	return false;
       
        fd_set tmpfd;
        
        FD_ZERO(&tmpfd);
        FD_SET(sockfd,&tmpfd);
        
        struct timeval ttimeout;
        ttimeout.tv_sec=timeout;
        ttimeout.tv_usec=0;
        
        int i;
        if((i=select(sockfd+1,0,&tmpfd,0,&ttimeout))<=0)	return false;
        (*ibuflen)=0;
        if(Readn(sockfd,(char*)ibuflen,4)==false)	return false;	//¶ÁÈ¡±¨Í·4×Ö½Ú
        (*ibuflen)=ntohl(*ibuflen);	//×ªÖ÷»ú×Ö½ÚÐò£¬»ñµÃÊý¾Ý³¤¶È
        
        if(Readn(sockfd, buffer, (*ibuflen))==false)	return false;
        return true;
}

 bool TcpClient::TcpWrite(int sockfd, char* buffer, int* ibuflen)
 {
        if(sockfd==-1 || NULL==buffer || NULL==ibuflen)	return false;
        
	fd_set tmpfd;
        
        FD_ZERO(&tmpfd);
        FD_SET(sockfd,&tmpfd);
        
        struct timeval timeout;
        timeout.tv_sec=5;
        timeout.tv_usec=0;
        
        if(select(sockfd+1,0,&tmpfd,0,&timeout)<=0)	return false;
        int ilen=1;
        if(ibuflen==0)	ilen=strlen(buffer);    //ibuflen==0£¬·¢ËÍµÄÊÇ×Ö·û´®£¬²ÉÓÃ×Ö·û´®³¤¶È
        else ilen=*ibuflen;	//·¢ËÍµÄÊÇ¶þ½øÖÆÊý¾Ý
        
        int ilenn=htonl(ilen);	//ilen×ªÎªÍøÂç×Ö½ÚÐò
       
       	char strBufferTmp[ilen+4];

        memset(strBufferTmp,0,ilen+4);
        memcpy(strBufferTmp,&ilenn,4);	//¿½Èëilenn
        memcpy(strBufferTmp+4,buffer,ilen);	//¿½ÈëbufferÖÐÊý¾Ý
	
        if(Writen(sockfd, strBufferTmp, ilen+4) == false)	return false;
        return true;
}


bool TcpClient::Read(char* buffer, const int timeout)
{
    if(m_sockfd==-1)	return false;	   
    if(timeout>0)
    {
        fd_set tmpfd;
    
    	FD_ZERO(&tmpfd);
    	FD_SET(m_sockfd,&tmpfd);
    
    	struct timeval _timeout;
    	_timeout.tv_sec=timeout;
    	_timeout.tv_usec=0;
        m_btimeout=false;

        int i; 
        if((i=select(m_sockfd+1,0,&tmpfd,0,&_timeout))<=0)	
    	{
        	if(i==0)
            	m_btimeout=true;
        	return false;
    	}
    }
    m_bufferlen=0;
    return TcpRead(m_sockfd, buffer, &m_bufferlen); 
}


bool TcpClient::Write(char* buffer, int ibuflen)
{
    if(m_sockfd==-1)	return false;
    
    fd_set tmpfd;
    
    FD_ZERO(&tmpfd);
    FD_SET(m_sockfd,&tmpfd);
    
    struct timeval timeout;
    timeout.tv_sec=5;
    timeout.tv_usec=0;
    
    int i;
    if((i=select(m_sockfd+1,0,&tmpfd,0,&timeout))<=0)	
    {
        if(i==0)
            m_btimeout=true;
        return false;
    }
    int ilen=ibuflen;
    if(ilen==0)
        ilen=strlen(buffer);
    
    return TcpWrite(m_sockfd, buffer, &ilen);
}

void TcpClient::Close()
{
    close(m_sockfd);
    m_sockfd=-1;
}

```

```cpp
//TcpServer.h
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <errno.h>
#include <sys/select.h>
#include <errno.h>

class TcpServer
{
private:
    struct sockaddr_in m_server_addr;
    struct sockaddr_in m_client_addr;
    int m_client_addr_size;
public:
    int m_listenBacklog=100;
    unsigned int m_port;   
    int m_listenfd;
    int m_clientfd;
    bool m_btimeout;
    int m_bufferlen;
public:
    TcpServer();
    ~TcpServer();
    bool InitServer(const unsigned int port);
    bool Accept();


    bool Readn(const int sockfd, char* buffer, const size_t n);
    bool Writen(const int sockfd, char* buffer, const size_t n);
    bool TcpRead(int sockfd, char* buffer, int* ibuflen, const int timeout=0);
    bool TcpWrite(int sockfd, char* buffer, int* ibuflen=0);

    bool Read(char* buffer, const int timeout=0);
    bool Write(char* buffer, int ibuflen=0);
    char* GetIP();
    void CloseListen(); 
    void CloseClient();

};

```

```cpp
//TcpServer.cpp
#include "TcpServer.h"


TcpServer::TcpServer()
{
    m_port=-1;
    m_listenfd=-1;
    m_client_addr_size=0;
    m_bufferlen=0;
    m_btimeout=false;
}

TcpServer::~TcpServer()
{
    CloseListen();
    CloseClient();
}

bool TcpServer::InitServer(const unsigned int port)
{
    if(m_listenfd>0)
    {
        close(m_listenfd);
        m_listenfd=-1;
    }
	
    m_listenfd = socket(PF_INET, SOCK_STREAM, 0);
    
    if(m_listenfd == -1) return false;


    int opt=1;
    unsigned int len=sizeof(opt);
    setsockopt(m_listenfd, SOL_SOCKET, SO_REUSEADDR, &opt, len);
	
	
    memset(&m_server_addr, 0, sizeof(m_server_addr));
    m_server_addr.sin_family=AF_INET;
    m_server_addr.sin_addr.s_addr=htonl(INADDR_ANY);	
    m_server_addr.sin_port=htons(port);

    if(bind(m_listenfd, (struct sockaddr*) &m_server_addr, sizeof(m_server_addr)) == -1)
    {
        CloseListen();
        return false;
    }
    
    if(listen(m_listenfd, m_listenBacklog) == -1)
    {
        CloseListen();
        return false;
    }

    m_client_addr_size=sizeof(m_client_addr);
    return true;
}

bool TcpServer::Accept()
{
    if(m_listenfd == -1)    return false;
    if((m_clientfd = accept(m_listenfd, (struct sockaddr*)&m_client_addr,(socklen_t*)&m_client_addr_size)) == -1)	return false;
    return true;
}


bool  TcpServer::Readn(const int sockfd, char* buffer, const size_t n)
{
        if(sockfd==-1 || NULL==buffer) return false;
        int nleft,nread,idx;
        nleft=n;
        nread=0;
        idx=0;
        while(nleft>0)
        {
            if((nread = recv(sockfd, buffer+idx, nleft, 0))<=0)
	        {
		        fprintf(stderr, "Error: %s\n", strerror(errno));
	            return false;
	        }
	        nleft-=nread;
    	    idx+=nread;
        }
        return true;
}

 bool  TcpServer::Writen(const int sockfd, char* buffer, const size_t n)
{
        if(sockfd==-1 || NULL==buffer) return false;
        int nleft,nwrite,idx;
        nleft=n;
        nwrite=0;
        idx=0;
        while(nleft>0)
        {
            if((nwrite = send(sockfd, buffer+idx, nleft, 0))<=0)
	        {
		        fprintf(stderr, "Error: %s\n", strerror(errno));
	            return false;
	        }
	        nleft-=nwrite;
    	    idx+=nwrite;
        }
        return true;
}


bool TcpServer::TcpRead(int sockfd, char* buffer, int* ibuflen, const int timeout)
{
        if(sockfd==-1 || NULL==buffer || NULL==ibuflen)	return false;
       
        fd_set tmpfd;
        
        FD_ZERO(&tmpfd);
        FD_SET(sockfd,&tmpfd);
        
        struct timeval ttimeout;
        ttimeout.tv_sec=timeout;
        ttimeout.tv_usec=0;
        
        int i;
        if((i=select(sockfd+1,0,&tmpfd,0,&ttimeout))<=0)	return false;
        (*ibuflen)=0;
        if(Readn(sockfd,(char*)ibuflen,4)==false)	return false;	//¶ÁÈ¡±¨Í·4×Ö½Ú
        (*ibuflen)=ntohl(*ibuflen);	//×ªÖ÷»ú×Ö½ÚÐò£¬»ñµÃÊý¾Ý³¤¶È
        
        if(Readn(sockfd, buffer, (*ibuflen))==false)	return false;
        return true;
}

 bool TcpServer::TcpWrite(int sockfd, char* buffer, int* ibuflen)
 {
        if(sockfd==-1 || NULL==buffer || NULL==ibuflen)	return false;
        
	fd_set tmpfd;
        
        FD_ZERO(&tmpfd);
        FD_SET(sockfd,&tmpfd);
        
        struct timeval timeout;
        timeout.tv_sec=5;
        timeout.tv_usec=0;
        
        if(select(sockfd+1,0,&tmpfd,0,&timeout)<=0)	return false;
        int ilen=1;
        if(ibuflen==0)	ilen=strlen(buffer);    //ibuflen==0£¬·¢ËÍµÄÊÇ×Ö·û´®£¬²ÉÓÃ×Ö·û´®³¤¶È
        else ilen=*ibuflen;	//·¢ËÍµÄÊÇ¶þ½øÖÆÊý¾Ý
        
        int ilenn=htonl(ilen);	//ilen×ªÎªÍøÂç×Ö½ÚÐò
       
       	char strBufferTmp[ilen+4];	
        memset(strBufferTmp,0,ilen+4);
        memcpy(strBufferTmp,&ilenn,4);	//¿½Èëilenn
        memcpy(strBufferTmp+4,buffer,ilen);	//¿½ÈëbufferÖÐÊý¾Ý
        
        if(Writen(sockfd, strBufferTmp, ilen+4) == false)	return false;
        return true;
}

bool TcpServer::Read(char* buffer, const int timeout)
{
    if(m_clientfd==-1)	return false;	   
    if(timeout>0)
    {
        fd_set tmpfd;
    
    	FD_ZERO(&tmpfd);
    	FD_SET(m_clientfd,&tmpfd);
    
    	struct timeval _timeout;
    	_timeout.tv_sec=timeout;
    	_timeout.tv_usec=0;
        m_btimeout=false;

        int i; 
        if((i=select(m_clientfd+1,0,&tmpfd,0,&_timeout))<=0)	
    	{
        	if(i==0)
            	m_btimeout=true;
        	return false;
    	}
    }
    m_bufferlen=0;
    return TcpRead(m_clientfd, buffer, &m_bufferlen);
}


bool TcpServer::Write(char* buffer, int ibuflen)
{
    if(m_clientfd==-1)	return false;
    
    fd_set tmpfd;
    
    FD_ZERO(&tmpfd);
    FD_SET(m_clientfd,&tmpfd);
    
    struct timeval timeout;
    timeout.tv_sec=5;
    timeout.tv_usec=0;
    
    int i;
    if((i=select(m_clientfd+1,0,&tmpfd,0,&timeout))<=0)	
    {
        if(i==0)
            m_btimeout=true;
        return false;
    }
    int ilen=ibuflen;
    if(ilen==0)
        ilen=strlen(buffer);
    
    return TcpWrite(m_clientfd, buffer, &ilen);
}
    
char* TcpServer::GetIP()
{
    return inet_ntoa(m_client_addr.sin_addr);
}
    
void TcpServer::CloseListen()
{
    close(m_listenfd);
    m_listenfd=-1;
}

void TcpServer::CloseClient()
{
    close(m_clientfd);
    m_clientfd=-1;
}

```

```cpp
//server.cpp
#include "TcpServer.h"

int main(int argc, char** argv)
{
    TcpServer tcpServer;

    if(tcpServer.InitServer(5000)==false)
    {
        printf("server init failed..\n");
        return -1;
    }

    if(tcpServer.Accept()==false)
    {
        printf("server accept failed..\n");
        return -1;
    }

    printf("client(%s) connected..\n", tcpServer.GetIP());

    char strbuffer[1024];

    while(true)
    {
        memset(strbuffer,0,sizeof(strbuffer));
        if(tcpServer.Read(strbuffer, 600)==false)  
	{
		printf("receive msg: %s", strbuffer);
		break;
	}

        strcat(strbuffer, ":ok");
        if(tcpServer.Write(strbuffer)==false)   break;
    }
    
    printf("client(%s) disconnected..\n", tcpServer.GetIP());
}

```

```c
//client.cpp
#include "TcpClient.h"

int main(int argc, char** argv)
{
    TcpClient tcpClient;

    if(tcpClient.ConnectServer("127.0.0.1",5000)==false)
    {
        printf("connect server failed..\n");
        return -1;
    }

    char strBuffer[1024];
    for(int i=0;i<10;i++)
    {
        memset(strBuffer,0,sizeof(strBuffer));
        snprintf(strBuffer,50,"this is msg:(%d)\n",i+1);
        if(tcpClient.Write(strBuffer)==false)   break;
        printf("send msg: %s\n", strBuffer);

        memset(strBuffer,0,sizeof(strBuffer));
        if(tcpClient.Read(strBuffer, 300)==false)    break;
        printf("receive msg: %s\n", strBuffer);
        sleep(1);
    }

}

```

# 3. 多进程CS

---

> Unix多进程基础：
>
> 

```cpp
```





















