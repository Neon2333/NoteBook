# 动态库制作

**制作动态库时，一定要注意是x86还是x64，调用的程序不论是C++还是C#都也一定要是对应的CPU编译。**

[博客1](https://blog.csdn.net/qq_30139555/article/details/103621955)

[博客2](https://blog.csdn.net/w_x_myself/article/details/82252646)
[博客3](https://blog.csdn.net/u011555996/article/details/80293812)
[博客4](https://www.jb51.net/article/90111.htm)

## 1. 宏定义

https://blog.csdn.net/a55684471/article/details/102366415

```c++
//用宏定义替代__declspec在头文件中导出dll所需要变量
#define __declspec(dllexport) DLLExport
//导出宏定义
#define __declspec(dllimport) DLLImport
```

DLL.h中在需要暴露的接口声明前加上`extern "C" DLLExport`

导出的接口前除了DLLExport还要加上extern "C"，extern "C"让比编译器以C方式编译该函数，避免C++方式在编译代码时考虑重载而将函数名与参数名组合的方式改变函数名，导致调用DLL的程序找不到接口函数。

```c++
extern "C" DLLExport int g_num;
extern "C" DLLExport int add(int, int);
```

### 引申extern用法：

> * test.c文件中的函数，若main.cpp想要调用，则需要在test.h中对函数声明为extern "C"
>
>   ```c++
>   //test.h
>   extern "C" int add(int, int);
>   ```
>
>   ```c
>   //test.c
>   int add(int a, int b){
>       return a + b;
>   }
>   ```
>
>   ```c++
>   //main.cpp
>   #include "test.h"
>   cout<<add(1, 2)<<endl;
>   ```
>
> * 表明这里的是变量声明，定义在其他文件
>
>   ```c++
>   //test.h
>   extern int g_num;
>   ```
>
>   ```c++
>   //test.c
>   int g_num = 0;
>   ```
>
>   ```c++
>   //main.cpp
>   #include "test.h"
>   func(g_num);
>   ```
>
> * 如果是要定义全局变量，那么在头文件中用extern关键字声明，然后在另一个.cpp文件中定义；
>
> * 如果是要声明一个不想被其他文件使用、只能被本文件使用的变量，可以用static关键字在头文件中进行定义；
>
> * 如果所要定义的变量为局部变量，并且其值在编译时就已经可以确定，就可以用const关键词在头文件中进行定义。

## 2. DLL.h头文件

https://blog.csdn.net/Andy710660541/article/details/111196740

### （1）.h防止重复包含宏定义

```c++
#ifndef _DLL_h_
#define _DLL_h_
...
#endif
```

### （2）该DLLExport哪些东西

* **只有main.cpp中要用的函数、变量等才用DLLExport和DLLImport导入导出。调用程序用不到的东西不要导出。如DLL.h中的getVec()。**

* **DLL本质上是函数库，暴露的是函数接口，全局变量、类等数据类型最好不要直接使用DLLExport的方法暴露。因为C#调用时无法访问导出的全局变量、类等，且C#使用CppDLL存在数据类型不一致的问题。**

* **不论是普通函数还是类的成员函数，想要暴露出作为接口被其他程序调用都要用DLLExport和DLLImport。成员函数想要能够被调用，仅仅导出导入类是不行的（不导出类，仅仅导出类的成员函数也可以访问public的成员函数）。但是全局变量的话必须要导出，才能被调用的程序访问到。**

### （3）注意事项

* **定义的全局变量前需要加上extern，不然的话在.cpp文件中定义全局变量会显示重定义**

  [博客1](https://blog.csdn.net/m0_49448331/article/details/109435277)

### （4）code

```c++
//DLL.h
#pragma once
#include<iostream>
#include<vector>
#include<string>
using namespace std;
#define DLLExport  _declspec(dllexport) 
#define DLLImport  _declspec(dllimport) 

DLLExport extern int numVec;	//不能省略DLLExport，否则numVec无法访问。extern表明此处为声明，定义在别的文件处（DLL.cpp处，否则DLL.cpp中的定义会                                 //被看作重定义）

class Person {					//类可以不DLLExport，printName()仍然可以被访问
public:
	Person() {}
	Person(string n) {
		this->name = n;
	}
	string name;
	~Person() {}
public:
	DLLExport void printName();		//Person被DLLExport，若printName不DLLExport，也是不可调用的。PrintName必须要DLLExport
};

extern "C" DLLExport int add(int a, int b);		//extern "C"防止c++的重载机制修改函数名
extern "C" DLLExport int multip(int, int);

extern "C" DLLExport void setNumVec(int val);

extern "C" DLLExport int getNumVec();

//extern "C" DLLExport vector<int> getVec(int);
vector<int> getVec(int);						//getVec不暴露为接口，调用程序中无法访问
extern "C" DLLExport void printVec();
```

```c++
//DLL.cpp
#include"pch.h"
#include"DLL.h"

int numVec = 0;

void Person::printName() {
	cout << "name: " << this->name << endl;
}


int add(int a, int b) {
	return a + b;
}

int multip(int a, int b) {
	return a * b;
}

void setNumVec(int val) {
	numVec = val;
}

int getNumVec() {
	return numVec;
}

vector<int> getVec(int n) {
	vector<int> v;
	v.reserve(n);
	for (int i = 0; i < n; i++) {
		v.push_back(i);
	}
	return v;
}

void printVec() {
	vector<int> tempv = getVec(numVec);
	for (auto it = tempv.begin(); it != tempv.end(); it++) {
		cout << *it << endl;
	}
}
```

### （5）VS工具查看DLL中暴露的接口：

运用的工具：单击Windows图标------>所有程序------>找到相应的Visual Studio文件夹------->选择Visual Studio tool(会打开文件夹)-------->寻找本机工具命令提示。切换到dll文件目录下，运行命令：`dumpbin /EXPORTS 库名`（例：dumpbin /EXPORTS Project2.dll）



---

# 动态库使用

## 1.  C++ VS2019使用

### （1）将DLL.h、DLL.lib、DLL.dll拷贝到C++项目根目录下

VS2019在头文件文件夹中`添加现有项`，添加DLL.h

对DLL.h做如下修改：

![image-20211018001220538](https://i.loli.net/2021/10/19/hGZcuvVCJLeP8SF.png)

```c++
//main工程中的DLL.h
//调用程序将DLL.h添加到自己项目的头文件文件夹中，将DLLExport改为DLLImport表明将接口从库文件中导入
#pragma once
#pragma comment(lib, "Dlltest.lib")		//导入库文件

#include<iostream>
#include<vector>
#include<string>
using namespace std;
#define DLLExport  _declspec(dllexport) 
#define DLLImport  _declspec(dllimport) 	//将所有的DLLExport替换为DLLImport

DLLImport extern int numVec;	//导入全局变量

class Person {
public:
	Person() {}
	Person(string n) {
		this->name = n;
	}
	string name;
	~Person() {}
public:
	DLLImport void printName();	//导入成员函数
};

extern "C" DLLImport int add(int a, int b);
extern "C" DLLImport int multip(int, int);

extern "C" DLLImport void setNumVec(int val);

extern "C" DLLImport int getNumVec();

//extern "C" DLLExport vector<int> getVec(int);
vector<int> getVec(int);
extern "C" DLLImport void printVec();
```

```c++
//main.cpp
#include"DLL.h"

int main(int argc, char** argv) {
	cout << "sum=	" << add(2, 3);
	cout << "multi=   " << multip(2, 3);
	numVec = 10;
	printVec();
	
	Person p("wang");
	p.printName();
	return 0;
}
```

#### 



## 2. C# VS2019使用CppDLL

### （1）C#调用CppDLL是否有必要

#### C#调用CppDLL若非必要，不要做跨语言调用的事。

#### C++/CLI方式：沟通C#和CppDLL

#### 改用进程通信方式——读写共享临时文本文件

> **不要做A语言代码修改为B语言代码的无用功。 也不要做用A语言代码直接调用B语言代码库这样复杂、这样容易出错的傻事。 只需让A、B语言代码的输入输出重定向到文本文件，或修改A、B语言代码让其通过文本文件输入输出。 即可很方便地让A、B两种语言之间协调工作。 比如： A将请求数据写到文件a.txt，写完后改名为aa.txt B发现aa.txt存在时，读取其内容，调用相应功能，将结果写到文件b.txt，写完后删除aa.txt，改名为bb.txt A发现bb.txt存在时，读取其内容，读完后删除bb.txt 以上A可以替换为任何一种开发语言或开发环境，B可以替换为任何一种与A不同的开发语言或开发环境。** 除非A或B不支持判断文件是否存在、文件读写和文件更名。 但是谁又能举出不支持判断文件是否存在、文件读写和文件更名的开发语言或开发环境呢？**可以将临时文件放在RamDisk上提高效率减少磨损磁盘。 数据的结构很复杂的话，文本文件的格式问题可参考json或xml 共享临时文本文件这种进程之间的通讯方法**相比其它方法的优点有很多，下面仅列出我现在能想到的： ·进程之间松耦合 ·进程可在同一台机器上，也可跨机，跨操作系统，跨硬件平台，甚至跨国。 ·方便调试和监视，只需让第三方或人工查看该临时文本文件即可。 ·方便在线开关服务，只需删除或创建该临时文本文件即可。 ·方便实现分布式和负载均衡。 ·方便队列化提供服务，而且几乎不可能发生队列满的情况（除非硬盘空间满） ·…… “跨语言、跨机，跨操作系统，跨硬件平台，跨国，跨*.*的”苦海无边， 回头是“使用共享纯文本文件进行信息交流”的岸！

### （2）C#调用CppDLL方法

* **将DLL.lib和DLL.dll文件拷贝到C#项目根目录下，VS依赖项会自动添加。**

* **把DLL.dll和DLL.lib放到生成.exe所在目录下**

* **C#代码中以[DllImport]导入**

  [C#导入不同参数的C++函数时的DLLImport方法](https://blog.csdn.net/linwenbin1990/article/details/40516059?utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-9.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-9.no_search_link)

> ```C++
> [DllImport(@"Dlltest.dll", EntryPoint = "printVec", CharSet = CharSet.Ansi, CallingConvention = CallingConvention.StdCall)]
> public static extern _printVec();	//_printVec为在C#代码中所用的函数名，可同名，也可自己重新定义一个函数名
> ```
>
> **用DllImport导入后，要立即跟着public static extern 语句，不能分开写。**

### （3）遇到的问题

* C++调用CppDLL时，除了函数接口，还可以直接访问导出的全局变量。但是C#调用CppDLL时，就无法直接访问！！，最好**将对全局变量的访问封装为set/get接口**暴露。[博客](https://bbs.csdn.net/topics/392169702)


* C#调用的Cpp DLL某个函数有cout/printf时，C#程序中不会调用控制台打印。如果需要打印，将数据传到C#，然后调用C#方法打印。

总之，一个原则C#只能调用DLL暴露的函数。若一些需求实在需要访问Cpp中的一些数据结构、全局变量、逻辑代码，可以将所需的所有封装成函数传出，由C#调用。

### （4）c++使用vector时，C#无法接收vector的处理方法

**所以最好不要将vector等C++特有的数据结构传出，而改用数组的方式，或用将vector作为在DLL内部的处理，传出接口处理**。[博客1](https://bbs.csdn.net/topics/250082890?list=1358739) [博客2](https://bbs.csdn.net/topics/70062716)

#### 若一定要将vector传入，需要在C++中将vector转换成对应类型的指针数组，再将指针数组传递到C#中：

> [博客1](https://blog.csdn.net/weixin_45993808/article/details/115483667?spm=1035.2023.3001.6557&utm_medium=distribute.pc_relevant_bbs_down.none-task-blog-2~all~baidu_landing_v2~default-1.nonecase&depth_1-utm_source=distribute.pc_relevant_bbs_down.none-task-blog-2~all~baidu_landing_v2~default-1.nonecase)
>
> ```c++
> //C++
> extern "C" __declspec(dllexport) double* __stdcall ArrTest();
> double* __stdcall ArrTest()
> {
>     vector<double> vec({ 6,2,3,4,5 });
> 
>     double* output = new double[vec.size()];
> 
>     memcpy(output, &vec[0], vec.size() * sizeof(double));
>     
>     return output;
> }
> ```
>
> ```C#
> namespace test
> {
>     class Program
>     {
> 	    [DllImport("libDemo", EntryPoint = "ArrTest")]
> 		public static extern IntPtr ArrTest();
> 		
>         static void Main(string[] args)
>         {
>             IntPtr ptr = ArrTest();
> 
>             long a = ptr.ToInt64();
> 
>             List<double> results = new List<double>();
> 
>             for (int i = 0; i < 5; i++)
>             {
>                 results.Add((double)Marshal.PtrToStructure((IntPtr)((long)(a + i * Marshal.SizeOf(typeof(double)))), typeof(double)));
>             }
>             
> 			foreach (var item in results)
>             {
>                 Console.WriteLine(item);
>             }
> 
>             Console.ReadLine();
>         }
>     }
> }
