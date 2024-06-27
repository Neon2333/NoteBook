# 1. 零长数组-变长结构体

---

> https://zhuanlan.zhihu.com/p/654747081
>
> GNU在标准C的基础上做了实用性扩展，**零长数组**就是其中一个有名扩展。
>
> - 作用 : 长度为0的数组的主要用途是为了满足需要变长度的结构体
>
> - 原理 :
>
>    在一个结构体的最后, 申明一个长度为0的数组, 就可以使得这个结构体是可变长的. 对于编译器来说, 此时长度为0的数组并不占用空间, 因为数组名本身不占空间, 它只是一个偏移量, 数组名这个符号本身代表了一个不可修改的地址常量。
>
>   C编译器计算结构体内数据成员的地址的方法是：用结构体的地址作为基址，而成员的长度是知道的，作为偏移量，基址+偏移量访问各个成员。
>
>   通过这种灵活的动态内存申请方式，这个 buffer 结构体表示的一片内存缓冲区，就可以随时调整，可大可小。这个特性，在一些场合非常有用。
>
>   比起在最后这个成员处使用指针的好处是：不用单独再给这个指针开辟内存。也不用通过struct成员的这个指针再去访问另一块内存，这是2次访存。
>
> - 常用场景：
>
>   协议栈中定义数据包，数据包有个固定长度的报头，而后面跟随的数据段长度可根据需要开辟内存大小。
>
>   在线视频网站，都支持多种格式的视频播放：标清、高清、超清、蓝光甚至4K。如果我们本地程序需要在内存中申请一个 buffer 用来缓存解码后的视频数据，那么，不同的播放格式，需要的 buffer 大小是不一样的。 如果我们按照 4K 的标准去申请内存，那么当播放标清视频时，就用不了这么大的缓冲区，白白浪费内存。 而使用变长结构体，我们就可以根据用户的播放格式设置，灵活地申请不同大小的 buffer，大大节省了内存空间。

```c
struct Packet
{
    int state;
    int len;
    char arr[0]; // 这里的0长结构体就为变长结构体提供了非常好的支持
};

int lenArr = 10;
Packet* pack = (Packet*)malloc(sizeof(Packet) + lenArr*sizeof(char));	
//这样pack指向的内存总长就是18个字节，后面跟着的arr[]就可以是变长的
//sizeof(Packet)==8;
//arr[0]是个长度为0的char数组，所以arr不占用内存。
```

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct student
{
    int num;       // 学号
    char name[20]; // 姓名
    char sex;      // 性别
} student;

typedef struct class
{
    char *teacher;
    int class_id;
    student students[];
} class;

int main()
{
    class* classA = NULL;
    classA = (class*)malloc(sizeof(class) + 5 * sizeof(student));

    // 遍历学生赋值
    for (int i = 0; i < 5; i++)
    {
        classA->students[i].num = 230580 + i;
        printf("请输入学号为%d的姓名:\n", classA->students[i].num);
        scanf("%s", classA->students[i].name);
    }
    // 遍历学生输出
    for (int i = 0; i < 5; i++)
    {
        printf("学号%d的姓名是%s:\n", classA->students[i].num, classA->students[i].name);
    }
    // 释放资源
    free(classA);
    classA = NULL; // 置空

    return 0;
}
```

# 2. `__attribute__((packed)`

这个在C语言中，用来配置struct内存的**编译器预处理指令**

```c
struct example1
{
		char c;
		char d;
		int e;    
};
int main(int argc, char* argv[]) 
{
		printf("size of example1 is %d.",sizeof(example1));
		return 0;
 }
//size of example1 is 12.
```

因为默认情况下，struct的内存分配会以最大内存为标准，把所有类型都赋予那么大的内存，比如上例中char类型，默认也被分配为了int类型的4个字节。
那么，如果希望每种类型都按本身的内存大小分配空间该怎么办呢？
在C语言中，可以采用__attribute__((packed))

# 3. 常用函数

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
printf();

若出现无法打印，可能是因为缓冲区造成无法立即输出，使用fflush刷新输出缓冲即可：

fflush(stdout);

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

value是将要被写入内存的值。由于memset操作的是字节，所以这个值实际上会被转换成一个无符号字符（通常是8位），然后复制到指定的内存区域中。如果你传递一个大于255的值给value，它将只取这个值的最低8位，因为无符号字符的最大值是255。
len是要写入的字节数
```

```c
#include<string.h>	//C
#include<cstring>	//C++

//：用于复制内存块。它可以复制任意类型的数据，不仅限于字符数组。
void* memcpy(void *str1, const void *str2, size_t n);	//从存储区 str2 复制 n 个字节到存储区 str1
//该函数返回一个指向目标存储区 str1 的指针
```

```c
#include<string.h>	//C
#include<cstring>	//C++

//用于复制字符串。它会从源字符串 src 复制包括空字符（'\0'）在内的所有字符到目标字符串 dest。
char *strcpy(char *dest, const char *src);
//返回指向目标字符串 dest 的指针
```

```c
#include<string.h>	//C
#include<cstring>	//C++

//这里的 dest 是目标字符串，src 是要连接的源字符串。strcat 函数会将 src 字符串连接到 dest 字符串的末尾，并返回 dest 的指针。
char *strcat(char *dest, const char *src);
```



```cpp
<stdio.h>

void perror(const char *str);
//它的作用是将当前的错误消息打印到标准错误输出（stderr）。这个函数会先打印一个错误消息的前缀，通常是 "Error" 或 "perror: "，然后是程序提供的字符串，最后是当前errno变量的值对应的错误描述。
```

```cpp
<unistd.h>
sleep(int sec);
usleep(int millsec);
```

# 4. 数组初始化

```c
int arr[10] = {0};
这样的初始化语句，会将数组arr的前10个元素初始化为0。这种初始化方式使用了C语言的默认初始化特性，即数组的剩余未显式初始化的元素将被自动初始化为0。
使用int arr[10] = {-1};
这样的初始化语句，会将数组arr的第一个元素arr[0]初始化为-1。根据C语言的初始化规则，如果初始化列表中的元素数量少于数组的大小，剩余的元素将自动初始化为0。
```

# 







