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
>   C编译器计算结构体内数据成员的地址的方法是，用结构体的地址作为基址，而成员的长度是知道的，作为偏移量，基址+偏移量访问各个成员。
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
    class *classA;
    classA = (class *)malloc(sizeof(class) + sizeof(student) * 5);

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
1. struct example1
2. {
3. char c;
4. char d;
5. int e;    
6. };
7. int main(int argc, char* argv[]) 
8. {
9. printf("size of example1 is %d.",sizeof(example1));
10.return 0;
11. }
//size of example1 is 12.
```

因为默认情况下，struct的内存分配会以最大内存为标准，把所有类型都赋予那么大的内存，比如上例中char类型，默认也被分配为了int类型的4个字节。
那么，如果希望每种类型都按本身的内存大小分配空间该怎么办呢？
在C语言中，可以采用__attribute__((packed))









