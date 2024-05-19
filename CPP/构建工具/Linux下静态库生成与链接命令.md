# 头文件和宏定义

## 1. 源码

```c++
	//test.h
	#ifndef _TEST_H_
	
	#ifndef _TEST_C_
	#define FUN_EXTERN extern     //test.c中有add的定义，若test.c未被引用，宏变量_TEST_C未被定义，就在add声明前加上extern通知编译器函数定义在其他								 //文件中定义
	#else
	#define FUN_EXTERN 			//test.c文件若已被引用，宏_TEST_C_就已被定义，则执行else后语句，FUN_EXTERN定义为空
	#endif
	
	FUN_EXTERN int add(int, int);
	
	#endif
```

```c++
	//test.c
	#ifndef _TEST_C_		//作用同头文件，也是为了避免test.c被多次引用
	#define _TEST_C_
	
	#include <stdio.h>
	#include "test.h"         //引用头文件用户定义头文件需要
	
	int add(int a, int b)
	{
		return a + b;
	}
	
	#endif
```

```c++
#include<iostream>
#include"test.h"

int main(int argc, char** argv){
    int a = 1;
    int b = 2;
    int c = add(a, b);
    cout<<c<<endl;
}
```



## 2. 头文件中的宏定义

https://blog.csdn.net/abc_12366/article/details/79155540

![image-20211017163141631](https://i.loli.net/2021/10/17/uJ7j1qSdGaIC2z8.png)

![image-20211017164125349](https://i.loli.net/2021/10/17/5qIG3bUNOcZMw4V.png)

## 3. 文件中的#ifndef和函数原型前的extern

### 1.# ifndef

https://www.cnblogs.com/zhizhiyu/p/10134044.html

在头文件和非main源文件中加入#ifndef或#ifdef等宏定义，是为了防止重复声明。

我们知道，头文件一般是用来声明的，会被其他的源文件和头文件include，当被多个文件include的时候就会造成重复声明。 为了使得只声明一次，引入前面所说的宏定义，一般以文件名大写和_来当宏的名字。

**这个宏是给预处理器用的，通常预处理变量用大写，所以这些宏定义都用大写**。为了方便，一般将大写的文件名作为预处理器变量，其实在#ifndef后面加什么都都可以，用文件名只是为了好区分。它只是用来定义一个预处理器变量。

举个例子：

在example.h头文件中定义

```html
#ifndef WHAT
#define WHAT

...


#endif
```

这样，就保证example.h在给定的源文件中只处理一次。**我们首先检测 #ifndef第一次处理头文件时，测试会成功，因为 WHAT 还未定义，就会向下执行，下一条语句定义了 WHAT。那样的话，如果我们编译的文件恰好又一次包含了该头文件。#ifndef 指示会发现 WHAT 已经定义，就会不再执行ifndef下面的语句，从而避免头文件被二次引用。**

其中WHAT可以随意写，一般按文件名写成_EXAMPLE_H_

### 2. extern 

extern修饰函数声明时，表示函数定义不在本文件中，需要去其他文件找定义。函数声明默认是extern的，也即不显示加extern编译器也会加上，所以没有必要刻意加上extern。

### 4. extern "C"

```C++
//test.h
#pragma once
#include <iostream>

extern "C" __declspec(dllexport) void SayHello();
```

**其中`extern "C"`的作用是告诉编译器将被它修饰的代码按C语言的方式进行编译**

---

# 静态库生成与链接

## 1. 将源文件编译为目标文件/汇编文件.o

```shell
g++ -c -o test.o test.c
```

## 2. 生成静态库

![image-20211017171521462](https://i.loli.net/2021/10/17/r3Swh9U271BvFHO.png)

```shell
ar -cr libtest.a test.o			//生成静态库文件libtest.a
```

***注意：静态库文件名是libtest.a，但是静态库名是test***

```shell
ar -t libtest.a		//显示test.o
```

## 3. 链接静态库文件

gcc/g++默认会去`/lib`和`usr/lib`和`usr/local/lib`中去寻找库文件。可以库文件cp到`/lib`再执行，但是这样会污染系统目录。因此需要指定库文件路径。

```shell
g++ -g -o main.out main.cpp -L./ -ltest.a 	
```

> -L——静态库的路径
>
> -l——后跟静态库名，去掉lib的部分
>
> **路径一定要在静态库名之前**

生成.out文件后，将静态库文件删除，.out文件仍可以运行，因为编译器将静态库的内容拷贝到可执行文件中。目标程序中将有静态库的多个副本，十分占用空间。

因此，当静态库改变时，需要将生成的新的静态库和目标程序.out文件重新连接，比较繁琐，因此使用共享库/动态库比较好。



























