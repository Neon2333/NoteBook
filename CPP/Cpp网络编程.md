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
struct sockaddr{
    sa_family_t	sin_family;	//地址族
    char		sa_data[14];	//IP+port
}

struct sockaddr_in{
    sa_family_t		sin_family;	//地址族IPv4/IPv6
    struct in_addr	sin_addr;	//32位IP地址
    uint16_t	    sin_port;	//16位端口号
    char	sin_zero[8];	//为了和sockaddr保持一致所设置的成员，必须全填充0
}
```

### C函数

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
#include<sys/socket.h>
int bind(int sock, struct sockaddr* server_addr, socklen_t addrlen);	//成功返回0，失败返回-1
```

```cpp
#include<sys/socket.h>
int listen(int sock, int backlog);	//成功返回0，失败返回-1

//backlog——服务器接受的客户端connect请求队列的长度。当有多个客户端同时请求时，服务端无法对所有进行处理，设置队列将请求放入其中，backlog设置队列长度。backlog=10，若来20个请求，则后面10个会被server拒绝不会给对应的client发送rst，这10个client将继续发送TCP SYN，带队列中有请求通过三次握手建立了TCP连接将会从队列中移到另一个队列中。空出位置才能供后续请求移入。
```

```cpp
//accept从请求队列里取，取不到时会阻塞
#include<sys/socket.h>
int accept(int sock, struct sockaddr* client_addr, socklen_t* addrlen);	//成功返回文件描述符fd，失败返回-1
```

```cpp
int connect(int sock, struct sockaddr* server_addr,socklen_t addrlen);	//成功返回0，失败返回-1
```

```cpp
recv();
send();
```

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

```cpp
//将str写入buffer，返回写入的字节数
int sprintf(char* buffer, const char* str, ...);

char buffer[1024];
int a = 0;
sprintf(buffer, "这是一个test=%d", a);
```

```cpp
//给连续内存buffer赋值value，返回指向该连续内存的指针
<string.h>	//C
#include<cstring>	//C++
void* memset(void* buffer, int value, size_t len);
```

```cpp
#include<string.h>	//C
#include<cstring>	//C++
void *memcpy(void *str1, const void *str2, size_t n);	//从存储区 str2 复制 n 个字节到存储区 str1
//该函数返回一个指向目标存储区 str1 的指针
```

```cpp
<stdio.h>

void perror(const char *str);
//它的作用是将当前的错误消息打印到标准错误输出（stderr）。这个函数会先打印一个错误消息的前缀，通常是 "Error" 或 "perror: "，然后是程序提供的字符串，最后是当前errno变量的值对应的错误描述。
```

```cpp
<unistd.h>
sleep(int sec);
```

### 网络字节序

* CPU向**内存**中写入数据/解析内存中数据存在2种方式：小端序、大端序

  如：0x1234，高字节是12，低字节是34。大端序存储于内存中时，在从低到高字节内存中分别存储的是12、34；在小端序中存储时为34、12

* 主机**发送数据**和**接收数据**，都是从内存中的低字节向高字节。

  因此，大端序主机发送0x1234时从高字节开始发，先发送12后发送34。小端序主机接收后从低字节开始存，将12放到低字节内存，34放到高字节内存。所以小端序主机，读数据最终会变为0x3412。

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

### 字符串点分十进制IP转成uint32_t类型

点分十进制IP是字符串形式，需要函数将字符串形式的IP转换为32位整型数据才能绑定地址。

```cpp
//字符串转整型
#include <stdlib.h>
int atoi(const char* str);	//成功时返回整型，失败返回0

//示例：
char buff[]={'2','4','5','+'};
int num = atoi(buff);
p num	//245
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
in_addr_t inet_addr(const char* str);	//成功时返回整型网络字节序IP，失败返回INADDR_NONE
* 将字符串形式的点分十进制IP，转换成32位整型形式，且同时转换成网络字节序
* 具有自动检测str是否是合法IP地址的功能
* 只能转IPv4
```

```cpp
inet_pton——点分十进制字符串转为二进制网络字节序IP
int inet_pton(int af_family, const char *str, void *addr);	//成功返回1，出错返回-1，af不是有效表达式返回0

inet_ntop——二进制网络字节序IP转成点分十进制字符串IP
const char* inet_ntop(int af_family， const void* addr, char* str, size_t len_str)	//成功返回str，失败返回NULL
   
inet_aton——将点分十进制字符串IP转成网络字节序二进制存储在struct in_addr变量中
int inet_aton(const char* str, struct in_addr* addr);	//成功返回true，失败返回false

inet_ntoa——将 二进制网络字节序转成点分十进制字符串IP
char* inet_ntoa(struct in_addr addr);	//成功返回字符串的地址，失败返回-1
```

### 流程

* server通过bind()将所有网卡的地址INNADR_ANY绑定到监听套接字listenfd
* 通过listen()设置**服务端等待队列大小**，并监听
* client调用connect，取自身IP和**随机取端口号**作为client_addr，将连接套接字sockfd注册到server的等待队列后connect返回（servre调用listen后，客户端才能调用connect）
* server调用accept从等待队列中抽取一个client套接字clientfd，此时server和client的TCP连接建立
* servre和client通过clientfd读写数据

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
	server_addr.sin_addr.s_addr=htonl(INADDR_ANY);	//INADDR_ANY表示所有网卡都监听（服务器一般不止一块网卡）
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
	socklen_t client_addr_size;
	client_addr_size=sizeof(client_addr);  

    //接收client连接，获取连接fd
    int clientfd;
	clientfd = accept(listenfd, (struct sockaddr*)&client_addr,&client_addr_size);
	if(clientfd==-1)
    {
        perror("accept");
        close(listenfd);
		return -1;
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
        if((msgCount = send(clientfd, buffer, sizeof(buffer), 0)) <= 0)	//发送响应ok
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


int main(int argc, char *argv[])
{
    //指定要连接的server的ip、port
	struct sockaddr_in server_addr;
	memset(&server_addr,0,sizeof(server_addr));

	server_addr.sin_family=AF_INET;
    //由域名解析出服务器IP
	struct hostent* h;
	if((h = gethostbyname(argv[1])) == 0)
	{
    	perror("gethostbyname..");
	    close(sockdf);	//client
    	return -1；
	}
	memcpy(server_addr.sin_addr, h->h_addr, h->h_length);
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
        if((msgCount = send(sockfd, buffer, sizeof(buffer), 0)) <= 0)	
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

### 细节

* listenfd（server）或sockfd（client)运行从3开始，012分别是标准输入输出错误。

## （2）数据收发

> read/write/recv/send

### strlen()和sizeof()

***strlen获取字符数组实际字节数size。sizeof获取字符数组开辟的空间capacity。***

### send

![image-20211012151257433](https://i.loli.net/2021/10/12/7suYTyHDxAcWG5I.png)

### recv

![image-20211012151209115](https://i.loli.net/2021/10/12/8FLvI5lgmdJCscQ.png)

### 粘包

由于TCP协议传输数据无边界，即write的数据长度若不够一个报文段，则会等若干次write的数据后一起发送。若write的数据长度超出一个报文段，则会将数据分割为若干报文段，多次发送。

所以使用裸TCP时，要解决粘包问题，常用方法有：

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
> 为了实现上述情景，需要服务端断开连接后虽然不再发送数据，但是还要能接收数据。

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
 char** h_addr_list;	//IP地址列表
}
```

```cpp
//由域名获取域信息
gethostbyname#include<netdb.h>
struct hostent* gethostbyname(const char* host);
//失败时返回NULL
//host——主机域名
//hostent——存储域相关信息的结构体
struct hostent* h;
if((h = gethostbyname(argv[1])) == 0)
{
    perror("gethostbyname..");
    close(sockdf);	//client
    return -1；
}
memcpy(server_addr.sin_addr, h->h_addr, h->h_length);
```

## （5）缓冲区
