# 0. cpp效率的影响

c++效率和C相近，但一下常用特性中，以下会影响效率：

* 虚函数

  

* RTTI

## vscode修改多行

alt+左键点击可在多位置出现光标。

ctrl-alt-上下，可在一列上出现多个光标。

# 一、cpp基础

## SourceInsight入门使用

https://blog.csdn.net/mjy520123/article/details/120297021

## 1. C++内存分区

**代码区：**存放程序的代码，即CPU执行的二进制机器指令，并且是只读的。
**常量区：**存放常量(程序在运行的期间不能够被改变的量，例如: 10，字符串常量”abcde”， 数组的名字等)
**静态区（全局区）：**静态变量和全局变量的存储区域是一起的，一旦静态区的内存被分配，静态区的内存直到程序全部结束之后才会被释放
堆区：由程序员调用malloc()函数来主动申请的，需使用free()函数来释放内存，若申请了堆区内存，之后忘记释放内存，很容易造成内存泄漏
栈区：存放函数内的局部变量，形参和函数返回值。栈区之中的数据的作用范围过了之后，系统就会回收自动管理栈区的内存(分配内存 , 回收内存),不需要开发人员来手动管理。

![image-20230828172251820](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230828172251820.png)

## 2. 编译

每个.cpp文件都编译成1个.o目标文件（二进制代码），经过链接器链接形成可执行文件。

```cpp
.cpp ---预处理--->.i---编译器--->汇编文件.s---汇编器--->目标文件.o---连接器--->.elf
```

## 3. 关键字

### noexcept

* 指定函数是否抛出异常。预先知道函数不会抛出异常有助于简化调用该函数的代码，而且编译器确认函数不会抛出异常，它就能执行某些特殊的优化操作。

* 接收bool参数指定是否抛出

  ```cpp
  void func(int x) noexcept(true); //不抛出异常 
  void func(int x) noexcept(false); //抛出异常
  ```

* true可省略

  ```cpp
  void func(int x) noexcept; //不抛出异常 
  ```

### register

用于定义寄存器变量，表示该变量被频繁使用，**可以**（不是一定存在寄存器中）存储在CPU的寄存器中，以提高程序的运行效率。

### decltype

```cpp
decltype(exp)
其中，exp 表示一个表达式（expression）
```

decltype 的推导过程是在编译期完成的，并且不会真正计算表达式的值.

```cpp
int x = 0;
decltype(x) y = 1; // y -> int
decltype(x + y) z = 0; // z -> int
const int& i = x;
decltype(i) j = y; // j -> const int &
const decltype(z) * p = &z; // *p -> const int, p -> const int *
decltype(z) * pi = &z; // *pi -> int , pi -> int *
decltype(pi)* pp = &pi; // *pp -> int * , pp -> int * *
```



## 1. 条件编译

```c++
#if condition 
	code1
#else
    code2
#endif
//condition满足执行code1.不满足执行code2

#if 1
	testcode
#endif
//把测试code写在if 1中。测试结束把1改为0.
```

## 2. enum

```c++
typedef enum _Color{ red, green, black} Color;
Color color = Color.red;
```

## 3. 声明和定义

声明不会为变量开辟内存空间，定义才分配内存。

变量可以在多个文件中声明，但只能定义1次。

```c++
int a = 0;     //定义并声明了变量 a
extern int a;  //只是声明了有一个变量 a 存在
//---------------------------
void fun1();  //函数声明

void fun1(){  //函数定义
    cout<<"fun1"<<endl;
}
```

C/C++ 编译 cpp 文件是从上往下编译，所以 main 函数里面调用其他函数时，如果其他函数在 main 函数的下面，则要在 main 函数上面先声明这个函数。	

## 4. extern

### （1）全局的声明

* 全局变量

  extern修饰全局变量时不能在声明时初始化。

  全局变量的声明以extern修饰。可在不是定义变量的文件中使用。

  ```c++
  //a.cpp
  extern int a;	//声明a
  func(a);		//使用a。但是a的定义在func后面
  int a = 1;		//定义a
  ```

  ```c++
  //b.cpp文件中想要使用a，因为a.cpp不是头文件不能include到b.cpp中，只能用extern。
  //若a声明在a.h中，可以使用include也可以用extern。使用extern后就不用include"a.h"了。
  //在b.cpp中加上extern int a的声明即可使用
  extern int a;		//声明a
  func2(a);		//使用a
  ```

* 函数

  ```c++
  //Test.h：
  extern void Fun();   // 函数声明，extern 用于标识次函数为全局可调用函数（除了Test.cpp之外的其他源文件也可以调用，不用include"Test.h"）
  //Test.cpp：
  void Fun();  // 函数定义
  ```

### （2）cpp链接c函数

cpp为了提供多态，编译函数时会根据函数签名给函数名进行修改，c则不会。所以cpp调用c函数/DLL时需要`extern"C"`让编译器不修改函数名。

* 情形一：C头文件是自己写的

  在.h文件中对.c文件函数进行声明时，加上extern：

  ```c
  #ifdef __cpluscplus  
  extern "C" 
  {  
  #endif  
    
  //函数声明放在这 
    
  #ifdef __cplusplus  
  }  
  #endif  
  ```

​		.c文件编译成.o或者.so都行，cpp源文件include头文件就可以调用.c文件中的函数。

* 情形二：C模块是别人已经写好的，无法修改，.h文件中又没有extern：

  cpp调用时加上extern

  ```cpp
  //extern修饰整个头文件
  extern "C" 
  {  
  	#include "test_extern_c.h"  
  } 
  //如果仅仅只使用1个函数ThisIsTest，就extern修饰该函数
  extern "C"
  {  
  	int ThisIsTest(int, int);            
  } 
  ```

## 5. static

* 修饰类成员，表示属于非实例成员
* 修饰全局变量：将全局变量的作用域仅为当前文件或函数，不可被其他文件或函数访问。

* 修饰局部变量：延长生命周期，在函数结束时不会销毁变量，仍可访问。



## 5. 作用域

局部变量和全局变量的名称可以相同，但是在函数内，局部变量的值会覆盖全局变量的值。当变量间出现重名的情况下，作用域小的屏蔽作用域大的。

当局部变量被定义时，系统不会对其初始化，您必须自行对其初始化。定义全局变量时，系统会自动初始化为下列值：

## 6. 常量

把常量定义为大写字母形式，是一个很好的编程实践。

### （1）**const char\*, char* const的区别**

Bjarne 在他的 The C++ Programming Language 里面给出过一个助记的方法： **把一个声明从右向左读**。

```
char  * const cp; ( * 读成 pointer to ) 
cp is a const pointer to char 

const char * p; 
p is a pointer to const char; 

char const * p;	//同char* const
```

同上因为 C++ 里面没有 const* 的运算符，所以 char const*和const char *等价。



## 7. auto

* 自动类型推断

  声明时就必须要初始化

* 占位符

  **函数返回类型后置**，该方法主要用于**函数模板的返回值推导**。

  和decltype一起，常用在泛型函数。

  https://blog.csdn.net/xp178171640/article/details/104532284

  

  ```cpp
  template <typename T, typename U>
  decltype(t+u) add(T t,U u)           //编译错误，t,u未定义
  {
      return t+u;
  }
  //定义函数add的返回值decltype(t+u)，运行后，编译器会提示错误，告诉我们decltype(t+u)中t和u在此作用域中尚未声明。
  //因为t、u在参数列表中，而C++的返回值是前置语法，在返回值定义的时候参数变量还不存在。
  ```

  因此，c++提出以auto先占位，将返回类型后置。

  ```cpp
  template <typename T, typename U>
  auto add(T t,U u) ->decltype(t+u)      //此处decltype(t+u)中的t和u已定义 
  {
      return t+u;
  }
  ```

  返回值类型后置语法，是**为了解决函数返回值类型依赖于参数而导致难以确定返回值类型的问题**。有了这种语法以后，对返回值类型的推导就可以用清晰的方式描述出来。



## 7. 循环

```
for ( init; condition; increment )
{
   statement(s);
}
```

执行顺序：init->condition->statement->increment

```cpp
for( ; ; ){}	//死循环
```

## 8. lambda

https://zhuanlan.zhihu.com/p/384314474

```
[capture](parameters)->return-type{body}
```

**捕获列表：以传值或传引用的方式，捕获上下文中的变量。可在body中使用。**

```
[]      // 沒有定义任何变量。使用未定义变量会引发错误。
[x, &y] // x以传值方式传入（默认），y以引用方式传入。
[&]     // 任何被使用到的外部变量都隐式地以引用方式加以引用。
[=]     // 任何被使用到的外部变量都隐式地以传值方式加以引用。
[&, x]  // x显式地以传值方式加以引用。其余变量以引用方式加以引用。
[=, &z] // z显式地以引用方式加以引用。其余变量以传值方式加以引用。
[this] 在成员函数中，也可以直接捕获this指针，其实在成员函数中，[=]和[&]也会捕获this指针。
```

parameters：参数列表。

reutrn-type：函数体的返回值类型。

## 9. Rand()随机数

 **Rand()生成的是伪随机数。**

* srand设置随机数种子，seed不变时，rand()生成的随机数也不变。因此一般使用会自动变化的系统时间函数`srand((unsigned) time(NULL))`

  time()头文件`#include <time.h>`

  ```cpp
  void srand(unsigned seed);
  ```

  ```cpp
  #include<time.h>
  #define random(x)(rand()%x)
  
  srand((int)time(0));//部署随机种子
  cout << random(100) << endl;
  ```

* rand()随机数生成范围

  1、rand 随机数产生的范围：在标准的 C 库中函数 rand() 可以生成 0~RAND_MAX 之间的一个随机数，其中 RAND_MAX 是 stdlib.h 中定义的一个整数，它与系统有关，至少为 32767。

  > window下的RAND_MAX为：0x7fff=2^15-1=32767
  >
  > linxu下的RAND_MAX为：2^31-1=2147483647

  2、使用 rand() 和 srand() 产生指定范围内的随机整数的方法：

  ```cpp
  int u = (double)rand() / (RAND_MAX + 1) * (range_max - range_min)+ range_min
  ```

  > 取指定区间的（伪）随机数不建议采用“模除+加法”的方式，
  >
  > 譬如：如果采用此法去0-10000内的随机数，则写法为
  >
  > ```
  > srand( (unsigned)time( NULL ) );
  > int n = rand()%10000;
  > ```
  >
  > 则0-2767之间每个数出现的概率为4/32676,而2768-9999之间的书出现的概率为3/32676，和前者是不同的。不过rand()产生的是伪随机数了这个无关紧要，

**建议使用random库生成真随机数。**

https://blog.csdn.net/henry_23/article/details/113623544

```cpp
#include <random>
#include <iostream>
 
int main()
{
    std::random_device rd;  //如果可用的话，从一个随机数发生器上获得一个真正的随机数
    std::mt19937 gen(rd()); //gen是一个使用rd()作种子初始化的标准梅森旋转算法的随机数发生器
    std::uniform_int_distribution<> distrib(1, 6);
 
    for (int n=0; n<10; ++n)
        //使用`distrib`将`gen`生成的unsigned int转换到[1, 6]之间的int中
        std::cout << distrib(gen) << ' ';
    std::cout << '\n';
————————————————
版权声明：本文为CSDN博主「henry_23」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/henry_23/article/details/113623544
```

```cpp
//下面是一个展示正态分布的例子
#include <iostream>
#include <iomanip>
#include <string>
#include <map>
#include <random>
#include <cmath>
 
int main()
{
    // 如果可用，从一个真实的随机数发生设备获得种子
    std::random_device r;
 
    // 在1和6之间随机的选择一个均值mean，用来生成正态分布
    std::default_random_engine e1(r());
    std::uniform_int_distribution<int> uniform_dist(1, 6);
    int mean = uniform_dist(e1);
    std::cout << "Randomly-chosen mean: " << mean << '\n';
 
    // 围绕刚刚选择到的“均值”mean生成一个正态分布
    std::seed_seq seed2{r(), r(), r(), r(), r(), r(), r(), r()}; 
    std::mt19937 e2(seed2);
    std::normal_distribution<> normal_dist(mean, 2);
 
    std::map<int, int> hist;
    for (int n = 0; n < 10000; ++n) {
        ++hist[std::round(normal_dist(e2))];
    }
    std::cout << "Normal distribution around " << mean << ":\n";
    for (auto p : hist) {
        std::cout << std::fixed << std::setprecision(1) << std::setw(2)
                  << p.first << ' ' << std::string(p.second/200, '*') << '\n';
    }
}
```

## 10. 数组

### （1）多维数组



### （2）指针指向数组

数组名：常量指针，指向数组首元素，保存数组首元素的地址。

```c++
double *p;
double runoobAarray[10];

p = runoobAarray;
```



### （3）数组作为形参	

**概念：**

**了解就行！！！！**

* 数组名：值是数组首元素的地址，类型是指向首元素的指针类型。

  ```cpp
  对于arr[2][3]来说，可以看成有2个元素，元素为int[3]
  arr表示首元素（元素是行）地址，也就是第一行的地址，值为：&arr[0]，类型是int(*)[3]。
     	因此arr+i或arr[i]表示第i行的地址。
      但使用sizeof计算的，却是元素个数：sizeof(arr)/sizeof(int)=数组元素总个数。
  arr[1]或者*(arr+1)表示第2行的首元素的地址，也就是第2行第1个数字的地址，值为：&arr[1][0]，类型是int*。
      因此arr[i]+j或   *(arr+i)+j表示第i行j列元素的地址。*(*(arr+i)+j)表示a[i][j]  
      sizeof(arr[0])/sizeof(int)=行元素个数
      sizeof(arr[0][0])/sizeof(int)=1
  ```

**指针形式1：**

**不要用这个用法。**只是辨析数组名和指针的概念。

`&arr是整个数组的地址，值同&arr[0]，但类型是int(*arr)[8]，&arr[0]的类型是int*`

```cpp
void printArray(int(*arr)[8]) {
    //*arr是取整个数组。所以参数中要指定数组长度
    for (auto i : *arr){
        std::cout << i << std::endl;
    }
}
// 二维数组
void printArray(int(*arr)[2][3]) {
    for(auto& i : *arr) { // must be reference here
        for(auto j : i) {
            std::cout << j << std::endl;
        }
    }
}
int main(int argc, const char *argv[])
{
    int a[8] = {1,2,3,4,5,6,7,8};
    int b[2][3] = {{11,22,33}, {44,55,66}};
    printArray(a);
    printArray(&b);
    return 0;
}
```

#### 指针形式2：用这种别折腾！！！

**二维数组的列数必须要指定，行数无所谓。**

**二维数组列数不定的时候，转成一维数组。printarr5**

```cpp
void printarr1(int arr[], int len)
{
    for(int i=0;i<len;i++)
    {
        std::cout<<arr[i]<<std::endl;
    }
}

void printarr2(int* arr, int len)
{
    for(int i=0;i<len;i++)
    {
        std::cout<<arr[i]<<std::endl;
    }
}

void printarr3(int arr[][3], int rowLen)
{
    for(int i=0;i<rowLen;i++)
    {
        for(int j=0;j<3;j++)
        {
			std::cout<<arr[i][j]<<std::endl;            
        }

    }
}

void printarr4(int(*arr)[3], int rowLen)
{
    for(int i=0;i<rowLen;i++)
    {
        for(int j=0;j<3;j++)
        {
			std::cout<<arr[i][j]<<std::endl;            
        }

    }
}

void printarr5(int* arr, int rowLen, int colLen)
{
    for(int i=0;i<rowLen;i++)
    {
        for(int j=0;j<3;j++)
        {
			std::cout<<*(arr+i*colLen+j)<<std::endl;	//i是0从开始，所以不用(i-1)*colLen    
        }

    }
}

int main(int argc, const char *argv[])
{
    int a[8] = {1,2,3,4,5,6,7,8};
    int b[2][3] = {{11,22,33}, {44,55,66}};
    printarr2(a);
    //b的值为&b[0]，类型是int(*)[3]。sizeof(b)是3*sizeof(int)
    printarr4(b);
    //b[0]值是&b[0][0]，类型是int*。sizeof(b[0])是sizeof(int)
    printarr5(b[0]);	//printarr5(&b[0][0])也行
    return 0;
}
```



**引用形式：**

```cpp
void printArray(int(&arr)[8]) {
    for (auto i : arr){
        std::cout << i << std::endl;
    }
}
// 二维数组
void printArray(int(&arr)[2][3]) {
    for(auto& i : arr) { // must be reference here
        for(auto j : i) {
            std::cout << j << std::endl;
        }
    }
}
int main(int argc, const char *argv[])
{
    int a[8] = {1,2,3,4,5,6,7,8};
    int b[2][3] = {{11,22,33}, {44,55,66}};
    printArray(a);
    printArray(b);
    return 0;
}
```

C风格：

```cpp
void func(int arr[], int len)
{
    for (int i = 0 ; i < length; ++i){
        std::cout << arr[i] << std::endl;
    }
}
void func(int* arr, int len){}
```

#### 用数组指针或数组引用作为函数参数的优点：

1. 参数仍然保留着数组的信息，包括长度，所以我们可以使用基于范围的for循环。
2. 防止参数误传。如果参数是长度为8的数组指针或引用，传递长度为10的数组作为参数，编译是无法通过的。
3. 方便。

```cpp
// 编译期获得数组长度
// see <<Effective Modern C++>>
template<typename T, std::size_t N>
constexpr std::size_t arraySize(T(&)[N]) noexcept {
    return N;
}
// 打印任意长度一维数组，type T is printable
template<typename T, std::size_t N>
void printArray(T(&arr)[N]) noexcept {
    for (auto& i : arr){
        std::cout << i << std::endl;
    }
}
```

### （4）数组作为返回值

C#的函数可以直接把数组作为返回值。

Cpp函数的返回类型没有“数组”这一结构，因此一般情况下我们会采用指针去接受数组的首地址来进行返回。

```cpp
#include <iostream>
using namespace std;

int* createArray(int size) {
    int* arr = new int[size];	//在堆上开辟的内存。若把arr放在栈上，返回arr地址时则会因为系统销毁了局部变量而无法访问。
    for (int i = 0; i < size; i++) {
        arr[i] = i + 1;
    }
    return arr;
}

int main() {
    int* myArray = createArray(5);
    for (int i = 0; i < 5; i++) {		//这里的数组长度5是我们自己加上去的
        cout << myArray[i] << " ";
    }
    cout << endl;
    delete[] myArray; // 释放内存
    return 0;
}
```

返回数组首地址时，不能用`sizeof(arr)/sizeof(type)`的方法计算数组长度了，**因为返回的地址退化成了首元素的地址，不再是数组的地址，sizeof(arr)==4。**

所以，可采用函数**返回数组首地址和数组长度结合**的方法：

```cpp
#include <iostream>
using namespace std;

struct prtArrary
{
	int* p;
	int len;
};

prtArrary getArrary()
{
	int* arr = new int[10];
	for (int i = 0; i < 10; i++)
	{
		int a = rand() % 10;
		arr[i] = a;
	}
	int len = 10;
	prtArrary prtA;
	prtA.len = len;
	prtA.p = arr;
	return prtA;
}

int main()
{
	prtArrary prtA = getArrary();
	for (int i = 0; i < prtA.len; i++)
	{
		cout << prtA.p[i] << " ";
	}
        delete[] prtA.p;
	system("pause");
	return 0;
}
```

总结一下，在处理数组作为返回值时，应当注意一下几点：

1. 采用指针**接收数组首地址**的方式返回。

2. 因为返回的是局部变量的地址，因此数组应开辟在堆区以避免被系统回收。

   new数组或者将局部变量的数组声明为静态static。

3. 返回值除了包含指针，还应该包含数组长度。（因为sizeof(指向首地址的指针)==4，而不是数组的长度）。

### （5）遍历数组

```cpp
for(auto var : arr)
{
    std::cout<<var;
}
```

### （6）数组名与指针

数组名是指向数组首元素的**常量指针**`typename* const`



## 12. 字符串

### （1）C风格的字符串

以`\0`结束的字符数组。

操作API：

| C库函数，头文件\<cstring\>                             | 功能                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| strcpy(s1, s2)                                         | s2拷贝到s1                                                   |
| strcat(s1, s2)                                         |                                                              |
| strlen(s1)                                             |                                                              |
| char *strchr(const char *str, int c)                   | 字符ch在字符串s1中第一次出现的位置。找到字符 c，则函数返回指向该字符的指针，如果未找到该字符则返回 NULL |
| char *strstr(const char *haystack, const char *needle) | 返回一个指针，指向字符串 s1 中字符串 s2 的第一次出现的位置。 |

**获得字符ch在str中的index：**

```cpp
char *find = strchr(str, ch);
if(find != NULL)
{
    int index = find - str + 1;
    chNext = *find;
    chPre = *(find - 1);
}
```

### （2）Cpp标准库提供string类型：

头文件：\<string\>

| 功能       |           |
| ---------- | --------- |
| 拷贝s1到s2 | s2=s1     |
| 拼接s1和s2 | s1+s2     |
| 长度       | s1.size() |

## 13. 引用

引用很容易与指针混淆，它们之间有三个主要的不同：

- 不存在空引用。引用必须连接到一块合法的内存。
- 一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。
- 引用必须在声明时被定义。指针可以先声明但不定义，在后面再定义。

当函数返回一个引用时，则返回一个指向返回值的隐式指针。这样，函数就可以放在赋值语句的左边



## 14. 时间

https://www.runoob.com/cplusplus/cpp-date-time.html



## 16. 结构体

C++定义结构体变量时用不用加struct关键词？

如果定义的**结构体名和变量名不冲突**，那么在定义[结构体变量](https://so.csdn.net/so/search?q=结构体变量&spm=1001.2101.3001.7020)时，**可以省略掉struct关键字：**

```cpp
//编译通过
struct Event {
    int start;
    int len;
    int end;
};
 
int main() {
    Event myEvent;
    myEvent.start = 100;
    myEvent.len = 60;
    myEvent.end = 160;
    return 0;
}

//编译不通过
struct Event {
    int start;
    int len;
    int end;
};
 
int main() {
    Event Event;
    Event.start = 100;
    Event.len = 60;
    Event.end = 160;
    return 0;
}
```

C 语言的 struct 定义了一组变量的集合，C 编译器并不认为这是一种新的类型。

C++ 中的 struct 是一个新类型的定义声明, 所以可以省略 typedef, 定义变量的时候也可以省略 struct, 而不用向c语言那样没用 typedef 取新名字, 就需要用 **struct 结构体名** 这种形式定义变量。

## 17. 类

记得写拷贝构造、重载=、析构函数声明为virtual、需要子类override的函数声明为virtual。

### （1）构造函数的列表初始化：

**用列表初始化成员变量会比赋值初始化快一些。**（赋值初始化是在构造函数当中做赋值的操作，而列表初始化是做纯粹的初始化操作。我们都知道，C++的赋值操作是会产生临时对象的。临时对象的出现会降低程序的效率。）

```cpp
Line::Line( double len): m_length(len)
{
    cout << "Object is being created, length = " << len << endl;
}
//上面的语法等同于如下语法：				
Line::Line( double len)
{
    length = len;
    cout << "Object is being created, length = " << len << endl;
}
```

* C++ 初始化类成员时，是按照声明的顺序初始化的，而不是按照出现在初始化列表中的顺序：

  ```cpp
  class CMyClass {
      CMyClass(int x, int y);
      int m_x;
      int m_y;
  };
  
  CMyClass::CMyClass(int x, int y) : m_y(y), m_x(x)
  {
  };
  //初始化的顺序是先m_x再m_y。
  ```

  所以建议按照声明顺序来写初始化列表。

* 构造函数先执行初始化列表，再执行函数体内容。

### （2）创建对象

**使用new创建对象时，会调用类的无参构造，所以需要确保类中一定要有无参构造。**

```cpp
ClassName t = new ClassName();
```

### （3）继承

* 继承目的是为了复用代码，不要按照自然属性划分父类、子类，以功能划分。

  ```cpp
  class Rectangle: public Shape
  ```

  https://zhuanlan.zhihu.com/p/619347450

* 多继承：一个子类有多个父类。

  **不要用多继承。**

* 可调用父类的构造函数，初始化从父类继承过来的字段

  ```cpp
  // 基类
  class Shape 
  {
     public:
        Shape(int w,int h)
        {
          width=w;
          height=h;
        }
     protected:
        int width;
        int height;
  };
   
  // 派生类
  class Rectangle: public Shape
  {
     public:
        Rectangle(int a,int b):Shape(a,b)
        {
          
        }
  };
  ```

* 构造函数调用顺序：基类 > 成员类 > 子类：

  ```cpp
  #include <iostream>
  using namespace std;
  
  class Shape {   // 基类 Shape
  public:
      Shape() {
          cout << "Shape" << endl;
      }
      ~Shape() {
          cout << "~Shape" << endl;
      }
  };
  
  class PaintCost {   // 基类 PaintCost
  public:
      PaintCost() {
          cout << "PaintCost" << endl;
      }
      ~PaintCost() {
          cout << "~PaintCost" << endl;
      }
  };
  
  // 派生类
  class Rectangle : public Shape, public PaintCost  //基类构造顺序 依照 继承顺序
  {
  public:
      Rectangle() :b(), a(), Shape(), PaintCost(){
          cout << "Rectangle" << endl;
      }
      ~Rectangle() {
          cout << "~Rectangle" << endl;
      }
      PaintCost b;        // 类成员变量构造顺序 依照 变量定义顺序
      Shape a; 
  };
  
  int main(void)
  {
      Rectangle Rect;
      return 0;
  }
  ```

### （4）拷贝构造

**自定义类建议都写上拷贝构造和=操作符重载。**便于赋值、存放于容器中。

=赋值是调用了拷贝构造函数的。

```cpp
class ClassName
{
    template<typename T> ClassName (const ClassName<T>& another) 
    {
   	    this.m_a = another.m_a;	//ClassName的普通成员
        this.p_addr_size = another.p_addr_size;	//容器大小
        this.p_addr = new T[this.p_addr_size];	//指针深拷贝
        for(int i=0;i<this.p_addr_size;++i)
        {
            this.p_addr[i] = another.p_addr[i];	//T是基本类型的话可以直接赋值。若T是复杂类型，需要重载=
        }
	}
}

```

C++ primer p406 ：拷贝构造函数是一种特殊的构造函数，具有单个形参，该形参（常用const修饰）是对该类类型的引用。当定义一个新对象并用一个同类型的对象对它进行初始化时，将显示使用拷贝构造函数。当该类型的对象传递给函数或从函数返回该类型的对象时，将隐式调用拷贝构造函数。

**C++支持两种初始化形式：**

拷贝初始化 **int a = 5;** 和直接初始化 **int a(5);** 对于其他类型没有什么区别，对于类类型直接初始化直接调用实参匹配的构造函数，拷贝初始化总是调用拷贝构造函数，也就是说：

```cpp
A x(2);　　//直接初始化，调用构造函数
A y = x;　　//拷贝初始化，调用拷贝构造函数
```

**必须定义拷贝构造函数的情况：**

只包含类类型成员或内置类型（但不是指针类型）成员的类，无须显式地定义拷贝构造函数也可以拷贝；有的类有一个数据成员是指针，或者是有成员表示在构造函数中分配的其他资源，这两种情况下都必须定义拷贝构造函数。

**什么情况使用拷贝构造函数：**

类的对象需要拷贝时，拷贝构造函数将会被调用。以下情况都会调用拷贝构造函数：

- （1）一个对象以值传递的方式传入函数体
- （2）一个对象以值传递的方式从函数返回
- （3）一个对象需要通过另外一个对象进行初始化。

**浅拷贝和深拷贝：**

```cpp
#include <iostream>
using namespace std;
/*类定义*/
class Box {
    private:
        int *length;
    public:
        Box(int *len);
        Box(const Box &obj);
        int GetBoxLen(void);
        ~Box();
};
/*类构造函数*/
Box::Box(int *len) length(len) {                //将指针参数传入构造函数中，再利用初始化列表
    cout << "Object has created" << endl;
}

/**********类浅拷贝函数*********/
Box::Box(const Box &obj):length(obj.length) {    //直接传入同类型对象存储的内容；
    cout << "Object has copied" << endl;     //如果是指针，则代表指向同一片内存空间；
}

int Box::GetBoxLen(void) {
    return *length;
}
/*类析构函数*/
Box::~Box() {
    cout << "release" << endl;
        delete length;
}

int main() {
    Box box1(10);
    Box box2 = box1;
    cout << "len of box1: " << box1.GetBoxLen() << endl;
    cout << "len of box2: " << box2.GetBoxLen() << endl;
    return 0;
} 
```

深拷贝：类的成员有指向堆内存的指针，需要在拷贝构造里new新的堆内存赋值给拷贝的对象。

防止两个对象的成员指针指向同一块堆内存，在析构函数中delete释放堆内存时对同一块内存释放2次。

### （5）父类引用指向子类

**父类引用/指针指向子类，需要把父类的构造、析构都声明为virtual。**

class Son:public Parent;

Parent parent = new Son();

若不把父类的构造和析构声明为virtual：右侧调用Son()时会只调用父类构造Parent()。析构parent对象时会只调用父类的析构~Parent()。

父类构造、析构声明virtual后：先调用自己的构造、再调用父类构造；先调用父类的析构、再调用自己的析构。

```cpp
class A 
{ 
private: 
	char* p; 
public: 
A() 
{ 
	cout<<"A()…"<<endl;  
 	this->p=new char[64]; 
	memset(this->p,0,64); //数组中元素置为0 
} 
~A() 
{ 
	cout<<"~A()"<<endl; 
	if(this->p!=NULL) 
	{ 
	delete[] p; 
	p=NULL; 
	} 
} 
    
class B:public A 
{
private: 
	char* p;       //B中有自己的一个p，同时B继承A有A的一个p，因此析构B类对象时既要释放A的p也要释放B的p。 
public: 
B()  //默认先调用A() 
{ 
	cout<<"B()…"<<endl; 
	this->p=new char[64];  //这里默认调用的是B自己的p 
	memset(this->p,0,64); 
} 
~B() 
{ 
	cout<<"~B()…"<<endl; 
	if(this->p!=NULL)              { 
	delete[] p; 
	p=NULL; 
} 
} 
```



## 18. 运算符重载（overload）

### （1）运算符重载

函数重载：函数名相同，函数签名不同（参数列表的个数、类型、顺序，返回值不包括）。

运算符重载是一种特殊的函数，函数名即operator加上重载的运算符。

运算符重载函数可以写在类里，那么参数列表只要写1个对象。可以写在类外的非成员，参数列表是2个对象：

```cpp
Box operator+(const Box&);
Box operator+(const Box&, const Box&);
```

### （2）this指针：

this 指针是一个隐含于每一个非静态成员函数中的特殊指针。它指向正在被该成员函数操作的那个对象。当对一个对象调用成员函数时，编译器先将对象的地址赋给 this 指针，然后调用成员函数，每次成员函数存取数据成员时由隐含使用 this 指针。

### （3）重载overload、覆写override、重定义redefine的区别

重载指的是函数具有的不同的参数列表，而函数名相同的函数。重载要求参数列表必须不同，比如参数的类型不同、参数的个数不同、参数的顺序不同。如果仅仅是函数的返回值不同是没办法重载的，因为重载要求参数列表必须不同。（发生在同一个类里）

 覆写是存在类中，子类重写从基类继承过来的函数。被重写的函数不能是static的。必须是virtual的。但是函数名、返回值、参数列表都必须和基类相同（发生在基类和子类）。

```cpp
#include<iostream>
using namespace std;
class Base
{
public:
	Base()
	{
		cout << "Base的构造函数的调用" << endl;
	}

	virtual void func()//多态使用时，如果子类中有属性开辟到堆区，那么父类指针在释放时无法调用到子类的析构代码
	{
		cout << "Base的func的调用" << endl;
	}

	virtual ~Base()
	{
		cout << "Base的析构函数的调用" << endl;
	}
};

class Son : public Base
{
public:
	Son()
	{
		cout << "Son的构造函数的调用" << endl;
	}

	virtual void func() override;

	~Son()
	{
		cout << "Son的析构函数的调用" << endl;
	}
};


void Son::func()
{
	cout << "Son的func函数的调用" << endl;
}

void test()
{
	Base* son = new Son;
	son->func();
	delete son;
}

int main()
{
	test();
	return 0;
}
```



 重定义也叫做隐藏，子类重新定义父类中有相同名称的非虚函数 ( 参数列表可以不同 ) 。（发生在基类和子类）

## 19. virtual、override

父类声明为virtual的函数为虚函数，子类可对该函数进行覆写，子类的该函数也要用virtual修饰（*基类声明的虚函数，在派生类中也是虚函数，即使不再使用virtual关键字*）。

override保留字表示当前函数重写了基类的虚函数。

```cpp
#include <iostream>
 
struct A
{
    virtual void foo();
    void bar();
    virtual ~A();
};
 
// member functions definitions of struct A:
void A::foo() { std::cout << "A::foo();\n"; }
A::~A() { std::cout << "A::~A();\n"; }
 
struct B : A
{
//  void foo() const override; // Error: B::foo does not override A::foo
                               // (signature mismatch)
    void foo() override; // OK: B::foo overrides A::foo
//  void bar() override; // Error: A::bar is not virtual
    ~B() override; // OK: `override` can also be applied to virtual
                   // special member functions, e.g. destructors
    void override(); // OK, member function name, not a reserved keyword
};
 
// member functions definitions of struct B:
void B::foo() { std::cout << "B::foo();\n"; }
B::~B() { std::cout << "B::~B();\n"; }
void B::override() { std::cout << "B::override();\n"; }
 
int main()
{
    B b;
    b.foo();
    b.override(); // OK, invokes the member function `override()`
    int override{42}; // OK, defines an integer variable
    std::cout << "override: " << override << '\n';
}
```



## 19. 虚函数和纯虚函数

**虚函数：** 是在基类中使用关键字 **virtual** 声明的函数。哪怕是父类引用指向子类对象，也会根据所调用的对象类型来选择调用的函数。类似C#中的父类virtual函数、子类override。

```cpp
class Fu 

{ 

public: 

string kongfu; 

fight() 

{…} 

}; 

 

class Zi:public Fu 

{ 

public: 
fight() 

{…} 

}; 

 

void fightPeople(Fu& fighter) 

{ 

fighter.fight(); 

} 

 

int main() 

{ 

Fu fu; 

Zi zi; 

fightPeople(fu);   //调用的是fu的fight() 

fightPeople(zi);   //调用的还是fu的fight() 

} 
```

在这里，编译器做了一个安全的处理，因为调用父类的fight()肯定可以执行，而若调用子类的fight()，因为子类中可能有父类所没有的函数，所以可能会在子类中找不到fight()调用。 

因此，想要实现多态，使传入fu时调用Fu中的fight()，传入zi时调用Zi中的fight()。需要对父类中被子类重写（override）的函数（发生多态的函数）声明为虚函数。**用关键词virtual**（在这里也同void*和public一样，和虚继承仅仅是用词相同，意思不同）。 子类的该函数也要加virtual修饰。

**纯虚函数/接口：** 在基类中定义纯虚函数，没有函数体。如果类中至少有一个函数被声明为纯虚函数，则这个类就是抽象类。类似C#中的抽象类的abstract方法。没有方法体，只能被子类override。

```cpp
class Box
{
   public:
      // 纯虚函数
      virtual double getVolume() = 0;
   private:
      double length;      // 长度
      double breadth;     // 宽度
      double height;      // 高度
};
```

**抽象类：** 有纯虚函数的类，不能实例化，一般用于规定子类有哪些需要实现的接口。

**抽象类的析构写成virtual。**

**总结：**

定义纯虚函数是为了实现一个接口，起到一个规范的作用，规范继承这个类的程序员必须实现这个函数。

定义虚函数是为了允许用基类的指针来调用子类的这个函数。

**使用多态时，把父类、子类的构造和析构都写成virtual。**



## 20. 引用

* 引用相当于变量的别名，声明时必须初始化，只能初始化一次不能修改（存放于常量区）。

* 三目运算符返回变量引用，可作左值。

* 函数返回值引用：

  

## 21. 传值

传值、传址、传引用。

**只读：**

| 类型     | 推荐传址方式      |
| -------- | ----------------- |
| 内置类型 | 传值              |
| 数组     | 传址（const指针） |
| 类       | const引用         |
| 结构体   | const引用         |

**读写：**

| 类型 | 推荐传址方式 |
| ---- | ------------ |
| 内置 | 指针         |
| 数组 | 指针         |
| 类   | 引用         |



## 22. const

### （1）常量

常量不在栈开辟内存存储，存在常量区的符号表，编译时遇到常量直接替换。

* 常量没有地址，初始化后不能修改。

```cpp
const int a = 10;	//a不可修改
```

* const变量的指针、引用必须也是const

  ```cpp
  const int a = 10;
  const int* p = &a;	//必须用const指针
  ```

* 常量的内存

  **const全局常量：**其值不可以修改，也不可以通过指针修改，因为其存储空间分配在常量区。

  **const局部变量：**

  在C语言中，const常量修饰变量名，不能使对应的内存只读，可以通过指针修改相应的内存，改变const的常量值。

  在C++语言中，const常量在符号表中定义，引用该常量时会自动填入其常量值，以保证const的有效。如果对const常量取地址，编译器会为其生成存储空间，这个存储空间的内容可以改变；但是不会影响符号表中的值。

### （2）const变量

```cpp
int  test1(const A& a)  //const a表明不想在test1()修改a 

{ 
	return  a.getA()+1; 
} 
```

### （3）const函数

**函数形参后面加 const，表明这个函数不会对这个类对象的数据成员（准确地说是非静态数据成员）作任何改变。**

即this指针是const的。

*原则：在设计类的时候，一个原则就是对于不改变数据成员的成员函数都要在后面加 const，而对于改变数据成员的成员函数不能加 const。*

**C++中只有被声明为const的成员函数才能被一个const类实例对象调用**

a.getA()，a调用成员方法getA()实质是将指代当前调用成员方法的对象a的指针this传入getA()，即getA(this)，以此实现对象和独属于它的成员之间的绑定。this要修饰为const。所以在getA()后加上const。

```cpp
Class A 
{ 
private: 

	int a; 
public: 
	int getA() const  //这里的const是修饰this指针的，const this 
	{ 
		return this->a; 
	} 
} 


```

## 23. 内联inline

调用函数过程：函数压栈、形参压栈，返回值出栈，耗费资源。

使用内联函数，在编译阶段直接将内联函数的代码替换到调用处，省略函数调用过程。适用于函数较简短，但是调用很多。声明和定义都要在前面加上inline。

## 24. 构造函数

编译器默认添加1个无参构造。	

若显式添加任意一种构造（包括拷贝构造），则默认无参构造消失，需要手动添加。（C#也这样）

拷贝构造：

```cpp
T t1 = T(t2);	//调用T的拷贝构造
T t1 = t2;		//调用T的拷贝构造
//调用=重载，不调用拷贝构造
T t1;
t1 = t2;
```

**不要在构造里写业务。**



## 25. static

* static定义类成员：成员将属于类所有。

* **必须在类外初始化static成员。**

```cpp
Test{ 
private: 
	static int m_c; 
} 
int   Test::m_c=1;      //在类外初始化static变量m_c
```

非static成员在C++11标准后可通过=在声明处直接初始化。

```cpp
Test{ 
private: 
	int m_c = 1; 
} 
```

* static成员函数只能访问static成员（C#也一样）

  成员通过this指针实现和调用对象的绑定。

  因为static成员函数属于类所有，没有指代当前调用成员函数的对象的this指针，所以不能调用非static成员。



## 26. 常用操作符重载

* 自增、自减重载

  ```cpp
   Test & operator++()
  19     {
  20         this->a++;
  21         this->b++;
  22         return *this;
  23     }
  24     //后置++
  25     Test operator++(int)
  26     {
  27         Test temp = *this;
  28         this->a++;
  29         this->b++;
  30         return temp;
  31     }
  ```

* <<重载

  ```cpp
  friend ostream& operator<<(ostream& os, Person p);	//声明友元函数，在类Person中，便于直接访问Person的成员
  
  ostream& operator<<(ostream& os, const Person& p)   //返回值为ostream类的引用
  {
      return os << "name:" << p.name << "  age:" << p.age << endl;
  }
  ```

* 重载=

  ```cpp
  //重载=
  ClassName operator=(ClassName<T>& another)
  {
      //ClassName中有容器的话，需要先清空容器，才能将another容器中的内容放入
      if(this.p_addr != NULL)
      {
          delete[] this.p_addr;
      }
      //重新创建一个容器
      this.p_addr_size = another.p_addr_size;
      this.p_addr = new T[this.p_addr_size];
      //拷贝
      for(int i=0;i<this.p_addr_size;++i)
      {
          this.p_addr[i] = another.p_addr[i];
      }
  }
  ```

* 重载[]

  ```cpp
  //重载[]
  T& operator[](int index)
  {
      return this.p_addr[index];
  }
  ```

* 重载pair的<<

  ```cpp
  friend ostream& operator<<(ostream& os, Person p);	//声明友元函数，在类Person中，便于直接访问Person的成员
  
  ostream& operator<<(ostream& os, const std::pair<T1, T2>& p)   //返回值为ostream类的引用
  {
      return os<<"["<<p.first<<","<<p.second<<"]"<<std::endl;
  }
  ```

  

## 



## 28. 创建对象的几种方式

* **栈中分配  ，由操作系统进行内存的分配和管理**

  ```c++
  ObjectClass obj1;
  ```

* **堆中分配  ，由管理者进行内存的分配和管理，用完必须delete()，否则可能造成内存泄漏**

  ```cpp
  ObjectClass* obj3 = new ObjectClass();
  ```

## 29. 函数指针

### （1）定义

using定义模板时较typedef好用很多！！

注意：虽然给函数指针赋值，可以直接使用函数名，但是在QT中还是需要取地址&。所以，**养成取地址再给函数指针赋值的习惯**。 

```cpp
//方法1：直接定义
int (*p) (int, int) = &func;
p(1, 2);

//方法2：定义函数指针类型p_t
typedef int(*p_t)(int, int);
p_t pp = &func;
pp(1, 2);
//C++使用using关键字
using p_t = int(*)(int, int);
p_t pp = &func;
pp(1, 2);
```

### （2）回调函数

函数指针是C/C++实现回调函数的途径。

将一段代码A像参数一样传递/“注册”给其他代码B，当B中某个信号/行为发生时，会通知回调函数，A代码将会被执行。（如：信号机制、事件机制）

将函数的调用转换为一个**指针变量（函数入口地址）**。这样，一个文件可通过获取这个变量调用另一个文件内的函数。 （C#中的委托也类似，在委托上注册函数，可通过委托在整个程序范围调用函数或是触发事件调用事件handler）

## 30. using关键字

### （1）使用命名空间

```cpp
using namespace std;
```

### （2）指代别名，比typedef更加直观

```cpp
using INT = int;
//等同于
typedef int INT;
//函数指针
using int(*)(int, string) = pt;	//定义了一个函数指针类型p
```

### （3）在派生类中引用基类成员

## 31. IO

### getline

有2种getline()函数

* \<istream>中的getline函数

  | getline(char* str, streamsize n)                 | 读取最多n个字符存储str中。n包含字符串结束标记符null，所以 最多读n-1个字符。 |
  | ------------------------------------------------ | ------------------------------------------------------------ |
  | **getline(char* str, streamsize n, char delim)** | **结束符delim会被读取，但不会放到str中**                     |

  ```cpp
  char name[256], wolds[256];
   cout<<"Input your name: ";
   cin.getline(name,256);
  ```

* \<string>的getline函数

  | istream& getline (istream& is, string& str);                 |      |
  | ------------------------------------------------------------ | ---- |
  | **istream& getline (istream& is, string& str, ``char` `delim);** |      |

  ```cpp
  int main(){
   string str;
   getline(cin, str, 'A');
   cout<<"The string we have gotten is :"<<str<<'.'<<endl;
   getline(cin, str, 'B');
   cout<<"The string we have gotten is :"<<str<<'.'<<endl;
  return 0;}
  ```

### cin/cout

**标准输入输出cin、cout：**

|                                                              |      |
| ------------------------------------------------------------ | ---- |
| cout.put('h').put('e').put('l')<<endl; //put一次输出一个字符，支持链式编程。 |      |
| cout.write("hello world!",strlen("hello world!"));//输出字符串 |      |
| //读取一个字符到ch： char ch; ch=cin.get()或cin.get(ch);     |      |



**标准错误流cerr：**非缓冲，插入到cerr的流会立即输出。

```cpp
cerr<<"error message.."<<endl;
```

**标准日志流clog：**clog对象是缓冲的。这意味着每个流插入到 clog 都会先存储在缓冲区，直到缓冲填满或者缓冲区刷新时才会输出。

这些小实例，我们无法区分 cout、cerr 和 clog 的差异，但在编写和执行大型程序时，它们之间的差异就变得非常明显。所以良好的编程实践告诉我们，使用 cerr 流来显示错误消息，而其他的日志消息则使用 clog 流来输出。

### 文本读写

![cppiotext](https://raw.githubusercontent.com/WangKun233/ImageHost/main/cppiotext.png)

### 二进制读写

![cppbinaryio1](https://raw.githubusercontent.com/WangKun233/ImageHost/main/cppbinaryio1.png)

![cppbinaryio2](https://raw.githubusercontent.com/WangKun233/ImageHost/main/cppbinaryio2.png)

## 32. 异常

**不要用异常，尤其是对性能要求比较高的地方。**

因为写出异常安全的代码不容易 容易泄露内存 所以大家都禁止异常。

c那么多年写下来，也没发现返回值有啥不好，程序严密坚如磐石，c++很难有这种感觉，怕层层返回，就尽量多用void函数。

从不用异常，没啥用。程序员应该自己保证捕捉所有错误并处理，不能处理的错误捕捉也没用。

### 语法

```cpp
void func1()
{
    try
	{
    	int x = 1;
    	throw x;
	}
	catch(int e)
	{
    	//异常处理
    }
}
```

### 限定抛出异常类型

```cpp
void func1() throw(int, string)	//函数func1限定抛出int、string类型
{
    try
	{
    	int x = 1;
    	throw x;
	}
	catch(int e)
	{
    	//异常处理
    }
}
```

### 使用异常

**标准异常：**C++ 提供了一系列标准异常，定义在 **\<exception\>** 中，我们可以在程序中使用这些标准的异常。

![image-20230829134711198](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230829134711198.png)

### **自定义异常**

继承`exception`这个所有异常的父类，override what()方法用于指定自定义异常产生的原因。

```cpp
//exception类中的what()方法
 _NODISCARD virtual char const* what() const
    {
        return _Data._What ? _Data._What : "Unknown exception";
    }
```

```cpp
class MyException :public exception
{
public:
    virtual char const* what() const noexcept override{
        return "myexception";
    }
};

int main()
{
    try {
        Book* b = new Book(6);
        cout << *b << endl;

        using p_t = int(*)(int, double);
        p_t pp = &func;
        cout << pp(1, 2.4) << endl;
    }
    catch (exception& ex) {
        std::cout<<ex.what();
    }
}
```

**不推荐直接使用系统内置异常类**

C++是个偏底层语言，不会帮开发者做额外的事情，但是C++也提供了带堆栈信息的异常——自建异常。你只需要继承异常类，然后带个堆栈信息量就行，还可以自定义堆栈信息详细度。**而且官方也不推荐直接使用系统内置异常类，都是建议使用自己的异常类。**



## 34. 强转

### static_cast

```cpp
static_cast<type>()
```

### dynamic_cast

### reinterpret_cast

### const_cast

## 35. 多态

多态概念：统一的写法，但是产生不同的行为。

### （1）静态多态

也就是编译期多态，包括：函数重载、操作符重载。

```cpp
void func(int a){}
void func(int a,string str){}

int main()
{
    func(1);
    func(2,"aaa");
}
```

### （2）动态多态

* 父类对象指向子类对象。调用虚函数时，会调用子类的虚函数而不是父类的。

  在父类中通过`virtual`声明虚函数，子类可`override`虚函数。

  ```cpp
  class Base
  {
  public:
      virtual func()
      {
          std::cout<<"func...Base"<<std::endl;
      }
  }
  
  class Son
  {
  public:
  	virtual func() override
      {
          std::cout<<"func...Son"<<std::endl;
      }
  }
  
  int main()
  {
      Base* b;
      b = new Son();
      b->func();	//func...Son
  }
  ```

  

* 函数指针，签名相同的函数都可以使用同类型的函数指针形参接收，然后在函数内调用。

```cpp
int func1(int a)
{
    return a++;
}
int func2(int a)
{
    return a--;
}

void func(int(*p)(int), int c)
{
    p(c);	//在这里调用形式都一样，但是可通过传入的函数不同实现不同的行为
}

int main()
{
    func(&func1, 2);	//3
    func(&func2, 2);	//1
}
```

### （3）实现原理

虚函数表VFtable，存有该类中所有虚函数的入口地址的数组。

虚表指针vptr，指向该类的虚函数表，32为系统下4字节。

在类中一旦声明了virtual函数，编译器会在**常量区**添加该类的虚函数表。在编译时会给对象实例的内存中添加一个隐藏的4B的指针，这会使得对象的内存占用增加4B。

过程：对于`b->func()`，子类实例son的虚表指针就会首先指向自己的虚函数表，运行时查找该表，若找到func则调用；找不到的话，则会指向父类Base的虚函数表，再次查找，调用父类的func。从而实现多态。

对象调用普通函数时，在编译期就可以决定，而调用虚函数是在运行时决定。通过虚表指针指向虚函数表，然后在表中查找（寻址）要调用的函数。因为存在查找的过程，所以速度较慢，不需要多态的函数不要声明为virtual。

### （4）对象的内存布局

* 类实例化为对象时函数不占有内存，数据成员占有内存。

* 没有数据成员或虚函数表的类，其实例占1B，表示对象的存在。

  有数据成员或虚函数表，就是占用实际大小。

* vptr在实例对象内存的起始位置。

* **多继承时，子类对象继承了几个父类，子类对象中就有几个VPTR指针，分别指向几张虚函数表。**

  **其中子类自身定义的新的虚函数会排在第一张虚函数表的后面，顺序同声明的顺序一致。**

* **子类override的父类的虚函数，将会覆盖相应的父类的虚函数表中的同名函数。且覆写后的函数的顺序，同父类中声明的顺序一致，同子类覆写的顺序无关。**

* 一个基类不论有多少虚函数，都只有一个vptr。

* 子类继承基类时，会把基类所有数据成员都继承过来，还可以调用父类的所有函数，包括虚函数。

* 一个子类实例的内存占用是：**自身数据成员+父类实例对象大小+虚表指针个数*4**

* 对类使用ZeroMemory时，会将vptr置空，这会导致调用虚函数时程序崩溃。所以不要用ZeroMemory初始化类。



## 37. RAII



## 38. RTTI



## 39. viotile







---

# 二、STL

容器存入元素，都是将元素的**拷贝存入**容器中，不是将元素本身存入容器。所以把类对象放到容器中，一定要写类的拷贝构造和=重载。 

使用算法需要引入`<algorithm>`头文件

## 1. 迭代器

```cpp
#include<vector>

//回调函数，将迭代器遍历的每个元素都传入执行一次
void printVector(int v)	
{
    std::cout<<v<<std::endl;
}

vector<int> v;
foreach(v.begin(), v.end(), printVector);
```

```cpp
for(vector<int>::iterator it = v.begin();it!=v.end();++it)
{
    std::cout<<*it<<std::endl;
}
```

## 2. string

```cpp
#include<string>
std::string str = "str";
```



## 3. vector

动态数组。压入、弹出都在尾部。

**可随机访问。**

**排序很快。增删慢。**

![vectorImg](https://raw.githubusercontent.com/WangKun233/ImageHost/main/vectorImg.png)

| 常用                                                        |      |
| ----------------------------------------------------------- | ---- |
| begin()——迭代器头                                           |      |
| end()尾——迭代器尾                                           |      |
| capacity()——容量                                            |      |
| size()——实际尺寸                                            |      |
| push_back(ele)——在尾部插入ele                               |      |
| pop_back()——弹出末尾元素                                    |      |
| insert(iterator pos, int count, ele)——在pos前插入count个ele |      |
| v.assign(it1, it2)——将迭代器[it1, it2)区间的数据拷贝给v     |      |
| v.assign(n, ele)——给v拷贝n个ele                             |      |
| v.assign(arr, arr + n)——使用数组arr的n个元素初始化给v       |      |
| vector\<T>(v).swap(v)——将v的capacity缩小为size              |      |

### 构造

![image-20230829164014517](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230829164014517.png)

**拷贝构造vector\<T> v(v1)，会将v的size初始化为v1的capacity()。**

```cpp
//也可用这种方法初始化
vector<int> v{10,20,30,40,50};  
```

### 赋值

用另一个vector的元素初始化当前vector

![image-20230829164101138](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230829164101138.png)

```cpp
vector<int> v;
deque<int> d;
v.assign(d.begin(), d.end());
```

### 存取数据

可以用`[index]`也可以用`at(index)`

C++11增加了data()的用法，它返回内置vector所指的数组内存的第一个元素的指针：

**访问数据时，先获取指针数据vector::data()，然后用指针方式访问，会大大提高效率。**

```cpp
#include<iostream>
#include<vector>
using namespace std;

int main(){
	vector<int> v;
	v.reserve(5);
	for(int i=0;i<5;i++){
		v.push_back(i);
	}
	int* p=v.data();
	for(int i=0;i<v.size();i++){
		cout<<p[i]<<endl;
	}
}
```



### 插入、删除

```cpp
v.insert(iterator pos, int count, ele)——在pos前插入count个ele
v.erase(pos1, pos2)——删除pos1到pos2之间的元素
v.erase(pos)——删除pos处的元素
v.clear()——清空V
```

vector清空时，capacity不变，只size变化，因此仍占有较多的空间。

所以可用`vector<T>(v).swap(v)`shrink：拷贝构造用capacity初始化自己的size、swap交换指针指向的内存块、vector(v)定义了一个匿名对象用v来拷贝。

### reserve预留空间

vector的capacity不够时，会以原来的2倍capacity申请内存，同时将所有元素拷贝到新内存，这一过程耗费资源。

所以可以用reserve先申请一定的capacity。

`reserve(int len)`申请的内存，没有初始化，不可以访问，不可以用`[index]`或`at()`赋值。

`resize(int size, T ele)`重新指定长度，若size>原size则新申请的置ele，若size<原size则多出来的删除。

**reserve是只开辟内存空间，而resize是开辟了空间置了默认值。**

## 4. deque

双向动态数组。方便访问首尾元素`front()`和`back()`。

方便在首尾分别压入元素`push_front()`、`push_back()`。

**排序慢。**

![image-20230829172614322](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230829172614322.png)

## 5. stack

不可随机访问。

FILO

| 常用属性          |      |
| ----------------- | ---- |
| top()——顶端元素   |      |
| push()——压入      |      |
| pop()——弹出       |      |
| empty()——是否为空 |      |
| size()——尺寸      |      |

## 6. queue

不可随机访问。无迭代器。

FIFO，队尾入，队头出。

![image-20230829173049865](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230829173049865.png)

| 常用属性      |      |
| ------------- | ---- |
| back()——队尾  |      |
| front()——队头 |      |
| push()——入队  |      |
| pop()——出队   |      |

## 7. list

链表。节点有数据域、指针域。

**增删快。排序慢。**

**不支持随机访问。不能用index访问元素。**

| 常用属性                                            |      |
| --------------------------------------------------- | ---- |
| lst(n, ele)——将lst初始化为n个ele                    |      |
| assign(n, ele)——赋值                                |      |
| push_back()/push_front()                            |      |
| pop_back()/pop_front()                              |      |
| insert(pos, ele)                                    |      |
| erase(pos)——删除pos处数据，返回下个数据的位置       |      |
| remove(ele)——删除所有ele                            |      |
| clear()——清空                                       |      |
| reverse()——翻转链表                                 |      |
| sort()——排序，从小到大。list自带排序，不是algorithm |      |

## 8. set

set的适用情形/作用：**快速检索、去重、排序**。 

存储数据时，会对数据自动排序。set和multiset的区别是，set不允许存储相同的数据，**multiset允许存储相同的数据**。

**set、multiset内的元素都是只读。** 

set和multiset不能直接修改值，因为直接修改会使二叉树混乱，所以需要修改时应该先删除该元素，再insert()元素。 

默认从小到大，若想从大到小，定义仿函数functor或lambda表达式指定排序规则：

```cpp
class myCompare 
{ 
public: 
	bool operator()(typename val1,typename val2) 
	{ 
		return val1>val2;      //val1<val2规则为从小到大排列，val1>val2表示规则从大到小排列。 
	} 
}; 

set<typename,myCompare> s1;  //将类的类型作为第二参数传入，s1用该规则排序存储 
s1.insert(1); 
s1.insert(2); 
s1.insert(3); 
s1.insert(4); 
s1.insert(5); 

set<typename,[](typename val1, typename val2)->bool{return val1>val2}> s1;
```

| 常用API                                 |      |
| --------------------------------------- | ---- |
| insert(ele)——插入元素                   |      |
| erase(pos)/erase(ele)/erase(pos1, pos2) |      |
| clear()                                 |      |

## 9. pair

数据对

```cpp
pair<T1, T2> p = std::make_pair(val1, val2);
cout<<p.first<<p.second<<endl;

//也可以写成
auto p = std::make_pair(val1, val2);
```

## 10. map

存储的元素是pair，一个是key，一个是value。

**会对key自动排序，可指定排序规则。**

内部是红黑树。

一个key只能对应一个value。**multimap可以多个key对应一个value。**

| 常用API                                                      |
| ------------------------------------------------------------ |
| empty()——判断是否为空                                        |
| size()——返回容器元素个数                                     |
| pair<map<T1, T2>::iterator, bool> insert(make_pair(val1, val2))——插入pair，返回值first是map的迭代器，second是是否插入成功 |
| map[key]——访问key对应的value。若key处的value不存在，则添加一个新的key/value对，并使用默认值初始化value。 |

**当key是自定义类时，需要用仿函数指定存储规则：**

```cpp
std::map<Person, int, comp> mp;
mp.insert(new Person(), 1);
mp.insert(new Person(), 2);
mp.insert(new Person(), 3);
```

## 11. unordered_map

**哈希表，只完成key到value的映射，不对key进行排序，查找比map快很多。**

```c++
#include<unordered_map>
```

## 12. sort

头文件为`#include<algorithm>`和`using namespace std`。 

时间复杂度为`n*log(n)`

```cpp
//默认从小到大
template<class RandomAccesssIterator> 
void sort(RandomAccessIterator first,RandomAccessIterator last); 
//可通过comp指定
template<class RandomAccesssIterator> 
void sort(RandomAccessIterator first,RandomAccessIterator last,Compare comp); 

bool comp(typename a,typename b) 
{ 
	return a<b; //从小到大 
	//return a>b;//从大到小 
} 
```

## 13. fill

把数组或容器it1~it2填充val

```cpp
fill(it1, it2, val)
```

## 14. reverse

把数组或容器it1~it2中间的值翻转

```cpp
reverse(it1, it2)
```

## 15. accumulate

在指定初值的基础上累加：

```cpp
int sum = accumulate(it1, it2, val0)	//sum=val0+it1~it2范围内的求和
```

---

# 三、现代C++

智能指针

右值引用

## 1. 取消转义

```cpp
string path = R"(E:\workspace\XML.md)"
```

## 2. nullptr

cpp中NULL被define为0，是int类型。

nullptr是为cpp量身定做的空指针。若编译器支持C++11标准，一律使用nullptr。

```cpp
int* p = nullptr;
char* p = nullptr;
double* p =nullptr;
```

## 3. constexpr

作用：让编译器知道是常量表达式，在编译期进行计算。

### （1）修饰常量

因为const有两种语义：常量、只读变量。

```cpp
//常量
const int NUM = 10;
int arr[NUM]{0};	//定义静态数组时，长度不能是变量，而NUM是常量，所以这里不报错

//只读变量
void func(const int a)
{
    //函数体不允许修改a的值，因为a是个只读的变量
}
```

常量表达式的计算是在编译时期就得出结果的，非常量表达式在运行时得出结果。

但编译器是无法直接判断常量表达式、非常量表达式，C++11后的constexpr关键词可以直接声明为常量表达式，编译期就可以得出结果。

所以，**对于常量表达式、基础类型的常量都推荐用constexpr修饰。**

### （2）修饰常量表达式函数

修饰的函数返回值是常量。

在编译阶段，就将函数调用用常量替换。

```cpp
constexpr int func()
{
    constexpr int a = 10;
    return a;
}
```

### （3）修饰构造函数

* 构造函数体必须是空的
* 初始化参数必须使用列表初始化

```cpp
class T
{
    public:
    	int a;
    public:
    	constexpr T():a(10)
        {
            
        }
}
```

## 4. auto

必须初始化，通过初始化的值来进行类型的自动推导。

限制：

* 不能用于定义数组的类型

  ```cpp
  auto arr[]{1,2,3,4};	//error
  ```

* 不能用于推导模版

  ```cpp
  template<typename T>
  class A
  {
  }
  
  int main()
  {
      A<double> t;
      A<auto> tt = t;	//error，不能这么推导
  }
  ```

常用场景：

* STL迭代器的类型

  ```cpp
   std::map<int, std::string> mp;
  
   mp.insert(std::make_pair(1, "aaa"));
   mp.insert(std::make_pair(2, "bbb"));
   mp.insert(std::make_pair(3, "ccc"));
   
   for(auto iter=mp.begin();iter!=mp.end();iter++ )
   {
       std::cout<<iter->first<<std::endl;
   }
  ```

## 5. decltype

通过表达式的类型进行推导。

```cpp
const int a = 10;
decltype(a) b = 20;	//b：const int
```

* 当括号內是函数名，用函数返回值类型推导。

  （特例：对于返回纯右值（常量）的函数，推导出的类型要去掉const。）

  ```cpp
  //括号内是函数名。表示使用函数返回值推导。
  const int& func(){}
  decltype(func) a;	//a是const int&类型
  ```

* 括号内是左值（可取地址）

  括号内是一个加了括号的表达式。

  这两种情况推导出的是引用。

  ```cpp
  class A
  {
      public:
      	int num = 9;
  }
  A a;
  
  decltype(a.num) aa = 0;	//aa:int
  decltype((a.num)) bb = aa;	//a.num外面加了括号。bb:int&
  //加法表达式
  int m=0;
  int n=0;
  decltype(m+n) c = 0;	//c:int
  decltype(q=m+n) d = c;	//q可取地址，是一个左值。d:int&
  ```

常用场景：

常用于泛型。

```c++
//容器迭代器的类型推导
template<typename T>
class A
{
    public:
    	printA(T& t)
        {
            for(m_iter=t.begin();m_iter!=t.end();m_iter++)
            {
                std::cout<<*m_iter<<std::endl;
            }
        }
    private:
    	decltype(T().begin()) m_iter;	//T::iterator m_iter这样写是不对的
}
```

```c++
//函数返回值类型后置
```

## 6. final

放在类或者虚函数的后面。

表示类不能被继承。

虚函数的override截止到这里，调用了final的这个类的子类不能再override了。

不能放在普通函数后。

```c++
class Base
{
    public:
    	virtual void test(){}	//final不是放在这里的。不然这个虚函数就不能被override了，设为虚函数就没意义。
}

class Son:public Base
{
    public:
    	void test() final{}		//override截止到这里为止。
}

class GrandSon:public Son
{
    public:
    	void test(){}	//这里会报错。因为Son类对虚函数test()用final修饰了。
}
```

```cpp
class Base
{
    
}

class Son final:public Base	//类Son不允许有子类
{
    
}

class GrandSon:public Son	//报错
{
    
}
```

## 7. override

override虚函数，目的是实现多态。

就是声明这个函数是重写的，让编译器检查虚函数重写是否出错（比如拼写函数名），也让阅读代码更直观，一看就知道是重写。

## 8. using

### （1）定义类型的别名

```c++
typedef oldname newname;
using newname=oldname;

//定义指针
typedef int(*pt)(int,string);
using pt=int(*)(int,string);	//更好理解
```

* 定义模版类型的别名

  ```c++
  需求在map里存int-int/int-double/int-string三种类型的键值对，可用map<int,T>
  想把map<int,T>定义为一个关于T的类型：mapint<T>
  typedef map<int,T> mapint<T>	//这是错的
  using mapint<T> map<int,T>		//可行
  ```


### （2）在子类中使用父类的成员

子类继承父类，子类有父类同名函数，父类的函数会被隐藏。

通过子类对象调用函数时，调用的是子类的而不是父类的该名称的函数。

```cpp
class Base
{
    public:
    	int a;
    	void func()
        {
            std::cout<<"base.."<<std::endl;
        }
}

class Derived:public Base
{
    public:
    	using Base::a;
    	using Base::func;
}

int main()
{
    Derived d;
    d.func();	//base..
}
```

## 9. 委托构造

在构造函数中，通过初始化列表调用其他构造函数。

目的：简化构造函数中写的重复代码。

```cpp
class A
{
    private:
    	int m_min;
    	int m_max;
    	int m_mid;
    
    public:
    	A(){}
    	A(int min)
        {
            this.m_min=min;
        }
    	A(int min, int max):A(min)
        {
            this.m_max=max;
        }
    	A(int min, int max, int mid):A(min,max)
        {
            this.m_mid=mid;
        }
}
```

## 10. 继承构造函数

对于从父类继承到的数据成员，使用父类的构造函数。

```c++
class Derived:public Base
{
    public:
    	using Base::Base;	//使用父类的所有构造函数
}
```

## 11. 列表初始化

在C++11之前，对不同的数据类型有不完全相同的初始化方式。

列表初始化把所有类型的初始化方式统一。

**在花括号里写上要初始化的值，放在要初始化的变量、对象的后面。**

使用要求：

* 普通数组
* 类和结构体要没有用户自定义的构造函数、没有private/protected的数据成员、没有基类、没有虚函数

例子：

```cpp
double a{10.21};
int arr[]{1,2,3,4};
int* p = new int{10};
int* arrp = new int[3]{1,2,3};
```

## 12. std::initializer_list

类似c#中params。

**可接受任意多个相同类型的数据。**

传参用初始化列表，列表里可传任意个数的参数。

```cpp
//func可传任意个数
void func(std::initializer_list<int> ils)
{
    for(auto it=ils.begin();it!=ils.end();it++)
    {
        std::cout<<*it<<std::endl;
    }
}
int main()
{
    func({1,2,3,4,5,6});	//不能写成func(1,2,3,4,5,6);
}
```

## 13. 基于范围的for循环

**只读取（访问）一次容器，确定容器元素的范围，然后就遍历。所以遍历时修改容器中元素，遍历中无法体现。（比如删除几个元素，但是遍历时元素个数不变）**

基于迭代器的for循环，每次循环都会判断边界，所以可以在遍历时动态修改容器中元素。

* `auto item:vt`遍历容器vt时，发生拷贝，将每个元素拷贝到item。

  拷贝降低效率。

  修改item无法修改原容器中的元素。

* `auto& item:vt` 使用引用，不发生拷贝效率提高，可修改容器中元素。
* `const auto& item:vt`，不发生拷贝，且限定不可以修改容器中元素。

```cpp
vector<int> vt{1,2,3,4,5};
for (auto item:vt)
{   
}
```

注意：

set中元素是只读的。

## 14. 可调用对象包装器function

把不同类型的可调用对象进行包装，就可以用一种统一的方式来使用，或作为参数传递。

* 可调用对象：可以按照函数使用的方法来调用的对象。

  函数指针、仿函数、类成员函数指针、类数据成员指针

  ```cpp
  //函数指针
  void func(int a, string str);
  using fptr = void(*)(int,string);
  
  //仿函数
  class A
  {
  public:
      int m_a;
  public:
     	void operator()(int a)
      {
          std::cout<<a<<std::endl;
      }
      void print(string str)
      {
           std::cout<<str<<std::endl;
      }
      static void hello()
      {
          std::cout<<"hello"<<std::endl;
      }
  }
  //类成员函数指针
  //定义时要加上表明是哪个类。
  using fptr=void(A::*)(string);
  fptr fp=&A::print;
  
  //类数据成员指针
  using ptr=int A::*	//表明是A类内的int*
  ptr pt = &A::m_a;	
  
  int main()
  {
      A b;
      b(3);	//仿函数
      
      A a;
      (a.*fp)("hello");	//Test().print("hello");
      a.*pt = 10;			//Test().m_a=10;
  }
  ```

* 场景：

  需要在一个函数func中把这4中可调用对象作为参数使用，那么需要写func的4种重载。麻烦！

  所以，如果有种统一的类型可以接收这4种参数（类似用函数指针可以接一类函数），只要写1个func就行。

  就可以用可调用对象包装器在func中接收参数，把可调用对象打包一下传进去。

* 语法

  把可调用对象赋值给包装器。

  ```cpp
  #include<functional>
  //包装普通函数
  function<void(int,string)> f1=func;	//函数名
  //包装类的静态函数
  function<void()> f2=A::hello;	//函数名要指定类作用域
  //包装仿函数
  A b;
  function<void(int)> f3=b;	//可调用对象：实例对象
  
  
  int main()
  {
      f1(1,"aaa");
      f2(2,"aaaa");
      f3(3);
  }
  ```

  ```cpp
  //实现回调（和函数指针一样，就是包了一层，向上又抽象了一个更广泛的类型）
  class B
  {
      public:
      	A(function<void(int,string)& f):callback(f){}
      	void notify(int id, string name)
          {
              callback(id,name);	//调用传入
          }
      private:
      	function<void(int,string) callback;
  }
  
  int main()
  {
      B b(func);
      b.notify(1,"aaa");
      B c(A::hello);
      c.notify();
  }
  ```

## 15. 可调用对象绑定器bind

**C++14起bind有可替换的特性。**

std::bind：

* 绑定后得到一个**仿函数**。

* 可以绑定常数，也可以绑定变量。

* 把可调用对象的所有参数绑定。

  ```cpp
  void output(int x, int y)
  {
      std::cout<<x<<y<<std::endl;
  }
  
  std::bind(output, 1, 2)();	//把x/y分别绑定了值1/2，得到一个仿函数，用()调用
  ```

* 将可调用对象的部分参数进行绑定。

  占位符：`std::placeholders_1`表示第一个参数先占位，仿函数的参数传入到这个参数。

  ```cpp
  //普通函数、变量
  auto f = std::bind(可调用对象地址, 绑定的参数/占位符)
  	bind(output, placeholders_1,2)(10);		//10->placeholders_1->x
  	bind(output, placeholders_2,2)(10);		//10->10->placeholders_2->y
  //类的成员函数
  auto f = std::bind(可调用对象地址, 类实例对象的地址, 绑定的参数/占位符)
  ```

## 16. 





---

# 四、并发

协程、线程池



---

# 五、泛型

## 模板的二次编译

模板函数、模板类都要经过2次编译：1.检查语法是否有错误；2.根据调用模板处的具体数据类型，决定泛型类型。



## 函数模板

泛型函数。

### 定义

```cpp
template<typename T> func(T& a,T& b)
{
    T temp=a; 
	a=b; 
	b=temp; 
}
```

## 类模板

### 定义

```cpp
template<typename T> class Animal
{
    private:
    	T a;
    	T b;
    public:
        void bark();
}

int main()
{
    Parent<int> parent(10,20); 
}
```

### 派生子类

* 将模板类特化成具体类，然后派生具体类

  ```cpp
  class Cat:public Animal<int>
  {
      ...
  }
  ```

* 模板类不特化，派生模板类

  ```cpp
  template<class T>  class Cat:public Animal<T> 
  {
      ...
  }
  ```

### template\<class T\> 

在模板类类体外写成员方法要加`template<class T> `

```cpp
//在类体外写成员方法，必须加模板声明 
//作用域也要加<T> 
template<class T>  void Animal<T>:bark()
{
    std::cout<<"bark.."<<std::endl;
}
```

### 使用模板要把声明和头文件写在一起

![image-20230829094124978](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230829094124978.png)

![image-20230829094147738](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230829094147738.png)

![image-20230829094206559](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20230829094206559.png)

上面的代码会报错，因为模板的2次编译，以及每个.cpp文件都是编译成单独.o目标文件再链接的。所以在.main文件中include"Person.h"文件后，main文件中只有模板类Person的声明，这样第一次编译时只检查语法错误，虽然在main文件中对Person类进行了特化\<int>。由于头文件中无函数定义，所以编译器会认为这里的函数实现在其他文件中，会在这里做一个符号代替函数，将找函数实现的工作交给连接器。

而编译Person.cpp时，只有定义，却无法获得特化的T具体类型。因此最后Person.o和main.o链接时，main.o中的符号会找不到。

**解决：模板函数、模板类的声明和实现不要分开写在不同文件中。可以用将声明和实现放在一个.hpp文件中。**
