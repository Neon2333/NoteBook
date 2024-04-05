# OS概述

### 操作系统解决的几个主要问题：

* **CPU虚拟化**
* **内存虚拟化**
* **并发**
* **持久化**

---

# CPU虚拟化



## 3.分时共享

### （1）CPU虚拟化的概念

在某一时刻在一个CPU上只能运行一个进程，即使现代CPU都有多个核（实际CPU），但核心数依旧是有限的。为了在一个核上运行多个进程。利用**分时共享技术**。

每个进程只能在特定的**时间片**才能占用CPU，CPU上的进程不断快速切换，从而造成多个进程同时在某一个CPU上运行的假象。

### （2）怎样切换进程

依靠**上下文切换**机制

### （3）何时切换进程

由OS的**进程调度程序**决定

## 5. 进程相关API

## 6. 受限执行机制

### （1）受限执行

* OS为了实现CPU虚拟化，应满足：**应用程序不能拥有对CPU的所有控制权，必须要受控，只能访问该访问的资源。**在满足应用程序受控的同时，OS要确保应用程序的执行效率仍然高效。

* 在介绍首先执行机制前，先介绍直接执行。直接执行与间接执行不同，前者直接在CPU上运行应用程序。

  当OS希望运行某个应用程序时，**直接执行**过程：

> * OS在进程表中添加要启动的进程的表项
> * 在内存中为该进程开辟空间，将elf按照对应关系加载到内存。
> * 跳转到跳转到程序入口点执行开始执行。
> * 直接执行的速度更快

* 为了实现应用程序受控且高效的执行，CPU具有两种模式：**内核态、用户态**。当CPU处于内核态时，可以访问所有资源。当CPU处于用户态时，运行于CPU上的应用程序将会收到限制，只能访问有限的资源。

### （2）系统调用

为了向应用程序提供一些操作高权限资源的功能，内核会以系统调用的形式暴露一些高权限的功能。

如C库函数封装了陷阱（陷入内核）指令的汇编代码。

**系统调用执行过程：**



### （3）进程切换

#### ①时钟中断

#### ②上下文切换过程

## 7. 进程调度

### （1）概念

### （2）性能指标

### （3）FIFO

### （4）SJF——最短任务优先算法

### （5）PSJF——抢占式最短任务优先算法

### （6）轮转算法

## 8. 进程调度算法——多级反馈队列（MLFQ）

## 9. 进程调度算法——比例份额调度

### （1）彩票调度算法

### （2）步长调度算法









---

# 内存虚拟化

地址空间（address space）的抽象让每个程序好像拥有自己的内存，而实际上操作系统秘密地让多个地址空间复用物理内存（或者磁盘）。

关键词：***地址空间、分段、分页、页表、TLB***

## 14. C库内存操作函数

### （1）进程的内存空间结构

![](https://raw.githubusercontent.com/WangKun233/ImageHost/main/mmmm.PNG)

图中自下而上地址从低到高，从低到高依次为：代码段、数据段、堆、共享库、栈、内核。

栈空间由编译器隐式管理，堆内存由用户手动申请和释放。

![image-20221002212259023](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221002212259023.png)

![image-20221002212342113](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221002212342113.png)

该空间结构是虚拟内存空间，是进程私有的、连续的一块内存，是OS通过内存管理模拟出的一块空间，实际的物理内存可能是多块的、不连续的。

### （2）申请堆内存

```C++
void* malloc(size_t size);
```

> 返回值——void*指针，需强转为需要类型
>
> size——需要申请的堆内存的大小（字节）
>
> > * 代码中，size不要用具体数值，用sizeof(Typename)替代。
> >
> > * 其中sizeof为编译时操作符，不是函数，前者在编译阶段运行，后者需要在执行阶段进行压栈、出栈。
> >
> > * 注意，为字符串str申请堆内存时：char* pstr = malloc(strlen(str) + 1)。需要多申请1B给字符串末尾的'\0'

```c++
void* calloc(unsigned int num, unsigned int size);
```

> 申请num个连续的长度为size的堆内存，且自动将申请的内存空间初始化为0
>
> 返回值——指针

```C++
void* realloc(void* ptr, size_t size);
```

> 创建一个长度为size的堆内存，将ptr指向的堆内存的内容拷贝到新开辟的长度为size的内存中，并返回指向开辟的新内存的指针
>
> ptr——指向存储待拷贝的内容堆内存的指针
>
> size——重新开辟的堆内存的长度

### （3）释放堆内存

```C++
free()
```

> 传入参数——malloc/calloc/realloc返回的指针
>
> 注意：只能释放一次、只能释放由malloc/calloc/realloc申请的堆内存

### （4）malloc底层相关

#### 概念

* malloc、realloc、calloc、free这些函数都是glibc库函数，底层是Linux内核中的C函数brk()和sbrk()，而brk和sbrk基于系统调用sys_brk()和mmap()。
* glibc库是GNU发布的C运行库。glibc是linux系统中最底层的api，几乎其它任何运行库都会依赖于glibc。glibc除了封装linux操作系统所提供的系统服务外，它本身也提供了许多其它一些必要功能服务的实现。
* 用户对于内存的操作应该使用malloc等函数，而不要试图使用brk()和sbrk()。

#### brk

* brk(programm break)表示程序代码的代码和数据到此结束，后面的部分只在运行时使用。Linux系统中，只存储elf文件中的代码段、数据段内容。

  ![image-20220908090039844](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220908090039844.png)

  Linux系统在创建进程时，每个进程都有一个brk，指向堆的末尾（高地址）。

  在进程在初始还没有申请堆内存时，brk指向数据段data的末尾。

#### brk()和sbrk()

​	brk()和sbrk()改变brk指针的位置

* 调用brk()申请堆内存，brk向高地址移动。调用sbrk()释放堆内存，brk向低地址移动。

  ```c
  #include<unistd.h>
  int brk(void* addr);	//申请堆内存
  void* sbrk(intptr_t increment);		//释放堆内存
  ```

#### sys_brk和mmap

* 两者都是用来申请堆内存的系统调用

  sys_brk将brk向高地址移动，mmap在堆和栈之间的文件映射区申请一块空闲虚拟内存。

  这两种方式申请的都是虚拟内存，没有分配物理内存。在第一次访问已分配的虚拟地址空间的时候，发生缺页中断，OS负责分配物理内存，并建立虚拟内存到物理内存的映射关系。

* malloc小于128k的内存时，调用sys_brk()申请

  malloc大于128k的内存时，调用mmap申请，在堆和栈之间申请一块空闲内存，对应独立内存，而且初始化为0。

  为什么要用两种方式申请内存？

  > brk管理的堆内存只有高地址被释放后才能释放低地址，而mmap分配的内存可以各自互不影响的释放。
  >
  > 例如：brk先后分配了p1和p2两块内存，p1不使用了需要释放，p2还在使用，这种情况下是无法释放p1的。因为只有一个brk无法同时指向两个位置。

---

## 15. 机制——地址转换

### （1）概念

* **地址转换**——将虚拟地址转化为物理地址

* **基址寄存器(base register)**——程序编写、编译时都按照程序拥有私有的一块连续内存进行。运行时，不论是PC内存储的指令的地址还是数据的存储地址都是虚拟地址，经过与基址寄存器内存储的基址求和再得到实际的物理地址，从物理地址取指令或读写数据。存储一块虚拟内存的物理首地址。

  `physicalAddr = virtualAddr + base`

* **界限寄存器(bound register)**——用来判断进程访问的地址是否超出分配的范围，确保进程无法访问其地址空间外的内存（若进程可以随意修改内存，就能够轻松地作出可怕的事情，比如重写陷阱表接管系统）

  > 两种处理方式：
  >
  > * 存储虚拟地址的界限，在用虚拟地址转化为物理地址之前，先用虚拟地址判断是否超出界限
  > * 存储物理地址的界限，在用虚拟地址转化为物理地址之后，用物理地址判断是否超出界限

* 地址转换机制完全由硬件完成，CPU上有负责内存管理的部分**MMU（内存管理单元）**。MMU上有一对基址寄存器和界限寄存器。

### （2）为了实现地址转换，CPU需要支持的功能

* 每个CPU上都要有基址寄存器、界限寄存器

* CPU要能提供特权指令，在内核模式下能够修改基址寄存器和界限寄存器的值。

  以便，可以在CPU处理上下文切换时，可以将基址寄存器和界限寄存器的值修改为待切换的进程的基址寄存器、界限寄存器的值（从进程结构中获取）。

* CPU要能产生异常

  > * 当用户程序试图访问超出进程的地址范围的内存（界限寄存器）进行越界访问时，CPU要能产生异常，调用越界访问异常处理程序进行处理
  > * 当用户程序试图访问基址寄存器或界限寄存器时，CPU要能触发**用户模式尝试执行特权指令**异常，并调用**用户模式尝试执行特权指令异常处理程序**

### （3）当CPU支持了以上功能后，OS需要增加的功能

* 进程创建时，OS要能为进程分配内存（虚拟内存）。通过**空闲列表(free list)**管理内存的分配和回收，将在后面详细讨论。

* 上下文切换时，OS要能保存基址寄存器、边界寄存器的值到进程结构或进程控制块PCB中，或从进程结构或进程控制块PCB中恢复到基址寄存器、边界寄存器中。

* CPU触发异常时，OS要能够调用相应的异常处理程序。如：越界异常处理程序。

  异常处理程序的调用通过异常表，异常表是在OS启动时就加载的，保存各种异常对应的应调用的处理程序。

---

## 16. 分段——虚拟内存向物理内存进行映射的方案一

### （1）为什么要引入分段？

不引入分段的地址转换是在偏移、基址寄存器值、偏移寄存器值的基础上将某个进程的**整个虚拟地址空间**映射到物理地址。

这会产生两个问题：

* 堆和栈之间的空闲区域将直接映射到物理地址，造成内存浪费
* 将某进程的整个虚拟地址空间映射到物理空间，需要物理内存管理程序在物理内存中寻找能够装下整个地址空间的一块连续物理内存，当找不到时就无法分配内存。

### （2）相关概念

* 分段就是将虚拟空间按照逻辑的不同分成若干段：代码段、堆、栈，每个段是地址空间里一个连续定长的区域，各个段分别向物理内存进行映射。每个段都有自己的一对基址寄存器、界限寄存器用来存储该段自己的基址和范围。

* MMU支持分段需要3对基址寄存器、界限寄存器。

* 分段机制可以将不同的段独立地映射到不同的物理区域（通过每个段都有自己的基址寄存器和界限寄存器），只有已用的内存才会按照逻辑被分到某个段中映射到物理内存，避免了虚拟空间中未使用的部分占用物理内存。

* **段物理地址=基址寄存器+段内偏移**

* 硬件在分段机制下的地址转换使用**段寄存器**。一种常见方式，称为**显式方式**

> * 前2位标识段类型，告知硬件：00-代码段，01-堆，10-栈
> * 后12位标识段内偏移
>
> * 硬件计算段物理地址的过程：
>
>   ![image-20220912042054020](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220912042054020.png)

隐式方式，硬件通过地址产生的方式来确定段类型（未详细解释）。

### （3）分段机制支持内存共享

为了节省内存，在地址空间之间共享某些内存段。主要是**共享代码段（共享库/动态库）**，因为代码段是**只读**的，共享只读的部分不会破坏隔离，因为没有修改内存。OS将共享内存块在不同进程间共享，但进程仍认为自己是在独享内存。

为了实现这种内存共享，每个段添加了**保护位**，标识某段的访问权限，如是否能够读写该段或是执行其中代码。保护位标记为只读的段可以被共享。

增加了访问权限以后，除了通过界限寄存器判断虚拟地址是否越界，还要通过保护位判断访问权限，若用户程序试图写入标识为只读的段或试图执行非执行段，CPU会触发异常，让OS来处理错误。

### （4）为了实现分段，OS需要支持的功能

* 和地址转换中上下文切换时需要保存、恢复基址寄存器、界限寄存器的值相同。OS需要在上下文切换时，保存、恢复段寄存器的值。（段类型、段内偏移）

* OS要管理空闲内存

  如果需要被管理（分配、回收）的内存都被划分为等大小的单元，那么管理起来就会方便很多。只要用数据结构维护这些固定大小的单元的列表，若有内存请求，就返回列表的第一个单元。

  但实际中，段的大小是不定的，给段分配空间时，就比较难找到恰好满足段大小要求的一块空间。找到一块大于需求的段的空间，分配给段以后，还剩下一块，这一块由于太小而难以分配给新的段，这就是**外部碎片**。（因为段的大小不一，段与段之间将会产生很多空闲的小洞，因为太小难以分配给新的段，被称为外部碎片。）

* OS将会利用算法，尽量解决外部碎片的问题：

  ①紧凑物理内存：将段与段之间的碎片合并成大的连续的空间

  ②空闲内存管理算法：将空间碎片以合理的算法分配给待分配的段

---

## 17. 空闲内存管理

### （1）为什么要对空闲的物理内存进行管理

#### 外部碎片

外部碎片指从空闲列表中分配给用户请求的空间大小后，空闲列表中剩下的太小的无法满足请求大小的内存块，虽然可能总的空闲空间之和满足请求大小。可通过合并空闲空间或使用分页机制消除外部碎片。

外部碎片不可避免。

#### 内部碎片

当内存分配程序将某块内存分配给程序时，若分配的空间 > 程序占用的空间，则这块分配的空间会多出一部分空闲空间，称为内部碎片。

内部碎片指分配的某块堆内存空间大于实际请求的堆内存大小，多出来的这块内存就是内部碎片。分配后的这块内存在被释放前都不能再被OS重新使用。可通过合理的内存分配算法减少内部碎片。内部碎片一旦产生，除非释放这块内存重新给请求分配空间，否则无法消除。

#### 磁盘碎片

磁盘上的外部碎片（即平时所说的磁盘碎片，磁盘碎片清理工具只能清理磁盘上的外部碎片，清除不了磁盘上的内部碎片，除非改变文件系统的分配单位即簇的大小，使之是一个文件大小的整数倍。）

### （2）空闲列表——管理空闲堆内存的数据结构

空闲列表的概念示意图：

![空闲列表](https://raw.githubusercontent.com/WangKun233/ImageHost/main/%E6%9C%AA%E5%91%BD%E5%90%8D%E5%9B%BE%E7%89%87.png)

### （3）内存分配程序

#### **机制1——分割、合并**

此处仍以空闲列表的概念图展示分割、合并过程。

申请内存时，内存分配程序会执行所谓**分割（splitting）**动作：找到一块大小>=所需大小的空闲空间，将其分割，第一块返回给用户，第二块留在空闲空间中。例如：

> 原始空闲列表如17-(2)中图所表示，用户申请1B空间，假设内存分配程序选择addr范围为20~30的空间进行分割。则malloc()返回addr=20，且空闲列表会变为：
>
> ![image-20220923110836157](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220923110836157.png)
>
> 可见，空闲列表的第二个节点，地址addr=21，长度len变为了9。

> 针对17-(2)中图，假设用户调用free(10)将addr=10,len=10的堆内存归还。若只是简单的将该块内存嵌入空闲列表中，将得到下图的结果：
>
> ![image-20220923112654814](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220923112654814.png)
>
> 问题发生了：尽管现在addr从0~30的空间都空闲，但却被分割成了3个长度为10的块。若此时用户想要malloc申请长度为20的空间，则无法找到满足长度需要的空间而失败。

为此，内存分配程序会在释放一块内存时，**合并**空闲空间：查看要归还的内存的地址和邻近的内存块，若要归还的内存和邻近内存块（一侧或两侧）完全相邻中间没有其他未归还的内存，就将它们合并成一个较大的空闲块。最后如图：

![image-20220923113403623](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220923113403623.png)

通过合并，分配程序能够确保有大块的内存空间满足用户的申请需求。

#### **机制2——头块**

头块紧邻malloc分配的每块内存之前，因此内存分配程序会从空闲列表寻找、分割出一块大小为`申请大小 + sizeof(header_t)`的堆内存。

```C
ptr=malloc(20);
```

![image-20220923171932868](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220923171932868.png)

ptr指向返回给用户程序的堆内存，大小为20B。在这块内存之前跟着一个头块，定义为：

```c
struct header_t{
    int size;
    int magic;
}
```

因此，头块的大小`sizeof(header_t) == 8B`。

free()释放内存时，会首先由ptr计算出hptr的值，然后获取size和magic进行验证，若无误就将头块和用户使用的内存都归还给空闲列表。

```c
void free(void *ptr) {
    header_t *hptr = (header_t *)(ptr - 1);		//hptr_addr == ptr_addr - 8B
    assert（hptr->magic == 1234567);
}
```

#### 机制3——空闲链表

* 空闲列表以链表为实现。链表的节点紧邻在空闲的堆内存之前，将空闲内存串起来，每个节点的大小为8B。

* **节点的结构：**

```c
typedef struct node_t{
    int size;	//当前紧邻的空闲块的大小
    struct node_t* next;	//32位地址空间下，指针4B
}node;
```

* **空闲链表**

  库通过调用mmap（大于128kb调用mmap，小于调用sbrk），从OS申请了一块4kb的堆内存。通过空闲列表管理这块内存，初始时因为这块内存是连续的，所以空闲列表只有一个头结点、一块空闲堆内存。

  > ![image-20220923215805479](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220923215805479.png)
  >
  > mmap申请了4kb内存，由于节点占用了8B，所以可被用户申请的内存为4088B。
  >
  > ![image-20220923220049984](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220923220049984.png)

* **内存分割**

  假设现在想要分配100B的一块空间，通过分割策略，从剩下的4088B空闲空间内分割出108B的一块，将head->size改为4088-108，同时head也移动到4088-108新地址处。分割出来的108B，其中8B作为块头header_t，header_t->size = 100，header_t->magic=***。

  这样分割了3次后，分配了三块内存的堆空间：

  ![image-20220923223810601](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220923223810601.png)

* **内存释放**

  `free(16500)`过程：

  释放上图中sptr=16500位置的内存块，将16500处的头块改为node_t结构，size=100，链表头插法插入节点，所以next=head（当前的head还未移动，head=3764），然后将head移动到新的空间链表的头结点处，即(16500-8)B处。free(16500)后内存结构，如下图所示：

  ![image-20220925161832023](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220925161832023.png)

* **合并紧邻的空闲内存块**

遍历空闲链表，将紧邻的空闲块合并（包括空闲块的节点node_t的8B）。一个空闲块只会有一个node_t，最终又恢复到初始状态的只有一个node_t和一段连续内存的状态：

![image-20220923215805479](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220923215805479.png)

#### 机制4——堆增长

当用户程序在运行过程中发现从OS申请的堆内存不够大时：

大多数传统的内存分配程序会从很小的堆开始，当分配的空间耗尽时，再向OS申请更大的空间。这一过程中内存分配程序进行了某种系统调用令堆内存增长，如UNIX系统中的sbrk()。

OS在执行系统调用sbrk()时，会寻找空闲的物理内存页，将它们映射到申请内存的进程的地址空间中，并返回新的堆的末尾地址（堆内存地址增长方向由低向高）。这样该进程就有了更大的堆，新的内存申请得到满足。

#### 机制5——堆内存分配算法

* **最小匹配**——遍历空闲列表，找到所有>=待分配空间大小的内存块，返回其中最小的那块。并将多余空间返还给空闲列表。

* **最差匹配**——遍历列表，找到最大的块进行分割，一部分作为待分配空间返回给用户，剩下的部分返还给空闲列表。

* **首次匹配**——不用遍历，找到第一个>=待分配空间大小的内存块，分割，将一部分返回给用户，剩下返还给空闲列表。

* **下次匹配**——不用遍历，多维护一个指针，指向上次遍历列表结束的位置，下次从指针指向处开始，而不是一味从头开始遍历列表。避免了对开头频繁的分割。

* **伙伴算法**——对空闲堆内存空间进行二分，分得的2个部分称为伙伴。再取其中一个进行二分，直到所得是能满足要求的最小部分。缺点是分配的块的大小都是2的幂，会导致内部碎片。该算法的好处是在回收内存时，可以判断其伙伴是否空闲，若空闲则合并，如此向上回溯，直到合并成整个块。

  ![image-20220925164008361](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220925164008361.png)

* **分离空闲列表**——若某个程序频繁的请求特定几种大小的内存，则设置一个专用的空闲列表管理这几种特定大小的空闲内存。这种特定大小的内存分配和释放都很快。其他非特定大小的内存的分配则交给通用空闲内存。如：厚块分配算法。

---

## 18. 分页——虚拟内存向物理内存映射的方案二

### （1）分页概念

* 分段是将**虚拟地址空间**按照逻辑/功能（代码、堆、栈）划分为段，再通过基址、段内偏移映射到物理地址。这样的划分方法，由于是按照功能划分，各段的大小不定，容易在分配时造成外部碎片。
* 设想：如果将虚拟地址空间都划分为相同的大小的单元，分配时只要分配个单元即可，将不会因为分配造成外部碎片，同时也提高了分配、释放的效率。因此，将这些相同大小的单元称为**页**，页的大小都是相同的，Linux默认页大小是4KB。这种划分方法称为分页。
* 虚拟地址空间的每个页，映射到物理内存上一个**大小相同**的单元，称为**页帧**。

### （2）页表

* 页表是现代OS管理内存的一个最核心的数据结构，存储**虚拟页号VPN**到**物理内存页帧号PFN**之间的映射关系。

* 页表是每个进程都有的数据结构，OS会为每一个进程都管理一个页表，记录其虚拟地址空间到物理内存的映射。

* 假设虚拟地址有6位，可编码64B。页表大小16B，则虚拟地址空间被划分为4个页表。以虚拟地址的2bit编码4个页表（00/01/10/11），4bit寻址某一个页表的16B（0000~1111）。

  **所以VPN为2bit，偏移量offset为4bit。**

  ![image-20220925212037363](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220925212037363.png)}

* 通过VPN找到页表项，从页表项中读取PFN，偏移量offset在虚拟空间和物理内存中都相同。将PFN和offset组合可得物理地址。

  ![image-20220926150815345](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220926150815345.png)

* 页表项中存储了哪些信息？

  > 物理页帧号PFN
  >
  > 保护位：对应物理页是否允许读取、写入、执行
  >
  > 存在位：对应物理页是在内存上还是磁盘上
  >
  > 脏位：页加载进内存后是否被修改过
  >
  > 参考位：追踪页是否被访问
  >
  > ![image-20220926151051012](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220926151051012.png)

* 从虚拟地址到物理地址的详细过程

  > **PTBR(PageTableBaseRegister)**：页表基址寄存器，存放页表的起始地址
  >
  > **VirtualAddress**：虚拟地址
  >
  > **VPN**：虚拟页号
  >
  > **VPN_MASK**：虚拟页号掩码
  >
  > **SHIFT**：虚拟地址和掩码与后的右移位数
  >
  > **PTEAddr**：页表项物理地址
  >
  > **PTE(Page Table Entry)**：页表项
  >
  > **offset**：偏移量
  >
  > **PFN**：物理页帧号
  >
  > **PFN_SHIFT**：物理页帧号左移位数（要放到偏移量的左侧高位）
  >
  > **PhysAddr**：物理地址

  ![image-20220926163831174](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220926163831174.png)

  > ①VirtualAddress和VPN_MASK相与，将虚拟页号的位保留下来，右移SHIFT位将其从高位移动到低位。得到虚拟页号VPN
  >
  > ②PTE是页表项，sizeof(PTE)是页表项的大小，VPN*sizeof(PTE)+页表起始物理地址PTBR，可得虚拟页号为VPN的页表项的物理地址PTEAddr
  >
  > ③以PTEAddr进行内存访问AccessMemory(PTEAddr)获取该页表项的内容PTE
  >
  > ④VirtualAddress和OFFSET_MASK相与，得到偏移量offset
  >
  > ⑤从PTE中获取物理页帧号PTE.PFN，左移PFN_SHIFT到高位。再与offset或得到完整物理地址PhysAddr
  >
  > ⑥访问物理地址PhysAddr，AccessMemory(PhysAddr)

### （3）到目前为止的分页机制影响了性能

每次访问虚拟内存映射的物理内存，都需要访问页表项中的内容（18-(2)中的③），而页表存储于内存，增加了一次对内存的访问。

---

## 19. TLB——快速地址转换

### （1）为什么要引入TLB？

如18-(3)所述，基本的分页因为每次访问内存都额外增加了一次内存访问（访问内存中的页表），影响了性能。

### （2）概念

增加硬件：地址转换**缓存**TLB

缓存什么：某些页表项中虚拟页VPN到物理页帧PFN的映射

过程：每次内存访问时，不再是直接去访问内存中的页表获取映射。而是先去TLB中查找是否有期望的映射，若有就完成地址转换，而不用去页表中读取，提高了性能。

---

## 20. 如何让页表更小——分页机制的改进

### （1）基本分页机制存在的缺陷

假设地址空间32位，一般的页为4KB占12位，则会有2^20个虚拟页和物理页帧，则页表需要有2^20个页表项记录VPN->PFN的映射，一个页表项假设有4B，则页表大小为4MB。又因为每个进程都会有一个页表，每个页表都是4MB，100个进程仅页表就将占据400MB的内存。

**页表占用的内存过大**，因此，需要减少页表项的数量来让页表所占空间更小。

### （2）解决方案1——增加页的大小

这是一种朴素的方案。通过增大每个页，从而减少页的数量，从而减少页表项的数量。比如，将页的大小从4KB增大到16KB，则页表项的数量变为原先的1/4，每个页表将只占用1MB。

有些操作系统支持多种页大小，如4MB的大型页。大型页在数据库管理系统和高端商业应用程序中常见。但这种大型页的主要目的不是为了缩小页表，而是为了通过减少页表中页表项的数量，减少TLB未命中的几率。

页过大的缺点在于容易导致页内空间的浪费，即内部碎片。

### （3）解决方案2——分段和分页结合

#### ①方案产生思路

地址空间大小固定、页大小固定时，页表项的个数也是固定的。但是，若地址空间的堆、栈、代码段中的使用并不多，那么从VPN到PFN的映射也将不多，页表中有效的页表项也将不多。页表中的大部分页表项都是未使用的、被标记为invalid（页表项除了保存VPN到PFN的映射，也保存了其他信息，如该页表项是否有效）。这是对内存的浪费。

由此，可以得到缩小页表内存占用的另一个方案：去掉页表中的无效区域（未使用的段对应的页表项）。

#### ②方案内容

不再将整个虚拟地址空间到物理地址空间的映射用一个页表进行记录。而是将虚拟地址空间划分成段，每个段都有自己的页表。

与单纯的分段机制不同的是：

> * 段基址寄存器Base不再存储物理段的基址，而是存储该段对应**页表的物理地址**；
> * 段界限寄存器不再存储物理段的长度，而是存储段对应**页表的长度**；
> * 每个段都有自己的界限存储器，存储该段对应的页表中最大有效页的值。

例如，如果代码段使用它的前 3 个页（ 0、 1 和 2），则代码段页表将只有 3个项分配给它，**并且界限寄存器将被设置为 3。内存访问超出段的末尾将产生一个异常，并可能导致进程终止。**以这种方式，与线性页表相比，**杂合方法实现了显著的内存节省。栈和堆之间未分配的页不再占用页表中的空间（仅将其标记为无效）。**

取虚拟地址的前2位作为分段位SN，表征段的类型：00-未使用的段，01-代码段，10-堆段，11-栈段。

TLB命中时，直接从TLB中获取VPN到PFN映射。TLB未命中时，从VPN到PFN算法如下：

> SN=(virtualAddr & SEG_MASK) >> SN_SHIFT	
>
> VPN=(virtualAddr & VPN_MASK) >> VPN_SHIFT
>
> PTEAddr=Base[SN] + (VPN * sizeof(PTE))
>
> PTE=MemoryAccess(PTEAddr)
>
> PhysAddr=MemoryAccess(PTE.PFN)
>
> 其中，
>
> PTE(page table entry)——页表项
>
> PTEAddr——页表项物理地址
>
> Base[SN]——SN段对应的基址寄存器中存储的页表的物理地址

### （4）解决方案3——多级页表

#### ①方案产生思路

 对于单级页表来说，若内存容量较大，按照常规的4KB的page大小的话，page table entry的数目将会很大。因为page table是按照VPN（virtual page number）来索引查找的，如果把单级页表视作一个big array，则VPN就相当于数组下标，因此page table本身需要在内存中是连续分布的，而且即便没有使用到的page，也会占用一个entry。

为了解决这些问题，在现代32/64位处理器中，通常使用的都是多级页表，操作系统的实现中也提供了对多级页表的支持。

#### ②方案内容

* 之前提到的页表都是线性页表，可看做以VPN作为索引的连续数组，数组存储PFN等信息。

* 增加一个中间层页目录PDE，PDE本身也是一个页表，每个页表项记录着一个下级页表的PFN和valid，

  PDE的某项无效——该PDE项中的PFN未定义

  PDE的某项有效——该PDE项指向的下级页中至少有一个PTE有效，即其valid为1。

![image-20220929201302931](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220929201302931.png)

* 由上图中描述的线性页表和多级页表，比较线性页表和多级页表从VPN查找PFN的过程：

  > **线性页表查找过程：**
  >
  > 页表基址寄存器PTBR中存储页表在内存中所在位置的首地址（首个页帧的页帧号，PFN=201）。
  >
  > PFN=201这个页帧中存储了4个页表项，其中第1/2/4个页表项有效，对应的物理页帧PFN分别为12/13/100。
  >
  > PTEAddr = PTBR + VPN*sizeof(PTE)
  >
  > 以第4个页表项为例：
  >
  > PTEAddr = 201 + 4*sizeof(PTE)
  >
  > PFN=AccessMemory(PTEAddr).PFN
  >
  > 得到PFN=100

  > **多级页表（二级页表）查找过程：**
  >
  > - 页表被分为页目录PD和与有效PDE（页目录项）个数相同的页表。
  > - PDBR（页目录基址寄存器）存储着PD首个页帧的号PFN（PD也存储在内存中，以页帧为单位），图中PFN=200的页帧存储了4个PDE。
  >
  > PDE至少拥有有效位valid bit和页帧号PFN。
  >
  > - valid bit==1，表示以该PDE.PFN的页帧作为首地址的页表中，至少有一个PTE是有效的。
  >
  > - valid bit==0，表示该页目录项对应的页表无效，则该PDE的其他部分将没有定义。
  >
  > - 多级页表在TLB未命中的情况下，会需要对内存进行2次访问。
  >
  > - ![image-20220929194305774](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220929194305774.png)
  >
  >   VPN被分为2各部分，分别是**页目录索引PDIndex**和**页表索引PTIndex**。
  >
  >   > PDEAddr = PTBR + PDIndex * sizeof(PDE)
  >   >
  >   > PDE.PFN=AccessMemory(PDEAddr).PFN
  >   >
  >   > PTEAddr=(PDE.PFN << SHIFT) + PTEIndex * sizeof(PTE)
  >   >
  >   > PhysicalAddr = PTE.PFN << SHIFT + 偏移量offset
  >
  > - 假设地址空间为32bit，页表大小4KB，则偏移量为12bit。VPN为20bit，线性页表会有4MB（22bit）个PTE。
  >
  >   但划分成2级页表的话，由于页表大小为4KB，所以PD项个数为1KB。

---

 ## 21. 虚拟内存机制

### （1）为什么需要虚拟地址

为了支持更大的虚拟内存，当物理内存不够时，需要将代码、数据移入移出内存。以硬盘作为物理内存的虚拟，让OS给多个进程提供巨大地址空间的假象。

在硬盘开辟的这部分提供内存页移入移出的存储空间，被称为交换空间，OS可以将内存中的页移入交换空间，在需要时将页移出交换空间。 

交换空间内存储的是虚拟页及其在对应物理页帧上存储的内容。

### （2）需要增加的2个机制

OS需要记录移入交换空间的虚拟页的硬盘地址，存储在页表项PTE中，类似PFN。

在页表项PTE中需要增加一位存在位（present bit)，用来表征该虚拟页是否存在于物理内存中。

即：硬件在页表中查询到PTE后，读取存在位，若为1表明该页存在于物理内存，若为0则不在物理内存而在硬盘上。

若硬件试图访问存在位为0的页（不在物理内存上的页），该错误被称为页错误（page fault)。

发生页错误时，陷入内核，由OS接管，CPU切换到内核模式，调用页错误处理程序，处理页错误。

### （3）页错误处理

#### ①过程

查询TLB，TLB miss。

查询页表。

若硬件通过PTE的存在位发现某个页不存在于物理内存，陷入内核，OS调用页错误处理程序处理页错误，从PTE中读取该页所在的硬盘地址，然后执行IO将该页从硬盘中读取到物理内存中。

IO执行相对于CPU而言是缓慢的，所以发生页错误的进程此时阻塞，经过进程调度程序的安排，CPU将会执行其他进程。

将该页从硬盘中读取到物理内存后，OS将对应PTE的存在位更新为1、更新PFN记录新读取到的页在物理内存中的位置（PFN）。

此时会再次执行TLB，而页表并未更新，会再次TLB miss，并从页表中读取VPN到PFN的映射获取PFN，更新到TLB。第三次查询TLB，TLB hit。

![image-20220929200052024](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220929200052024.png)

上图中L22、L23中对于present bit==0的PAGE_FAULT处理流程如下：

![image-20220929200137276](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20220929200137276.png)

#### ②页交换策略——从硬盘中读取页移入内存时发现空间不够？

若OS在从硬盘中读取页试图移入内存中时发现内存的空间已接近满了或是已满，就需要先将内存中已有的某些页换出到硬盘，从而留存空间给移入的页。选择那些页换出到硬盘的策略，即页交换策略。

* 页交换策略概述：

> OS并不会等到内存完全满以后再去将某些页换出，而是会设置高水位线HW、低水位线LW，来帮助决定移出页的时机。
>
> 当OS发现物理内存中可用的页少于LW时，休眠的交换守护进程（swap deamon）就会被唤醒从内存中移出某些页，直到内存中可用的页达到HW个，随后该守护进程将再次进入休眠。
>
> OS在写入页时，会将要写入的页聚集（cluster）或分组(group)，同时写入硬盘的交换空间，以提高硬盘效率。（这种机制是OS常用的：在内存的缓冲区中保存要写入到硬盘中的数据，随后一次性写入到硬盘，这样可以提高硬盘的效率。）
>
> 这样，在叙述了页交换策略后，图21.3的L3：EvictPage()在执行页交换前，会先简单检测内存中是否有足够的空闲页，若没有则会先唤醒交换守护进程释放内存中的一些页后，再唤醒原进程执行EvicePage过程。

* 页交换算法：FIFO、随机选择、LRU、近似LRU等。

## 22. 补充——内存对齐





---

# 并发

## 26. 并发介绍


