# 0. 一些要注意的点

* 使用push_back()和emplace_back()添加元素时，若元素有有参构造，不用手动构造，直接传参数进去，会自动构造，并拷贝/移动到容器中。

  ```cpp
  std::vector<std::thread> vec;
  vec.push_back([](){
      std::cout<<"hello"<<std::endl;	
  });	//传的参数是函数，但放进容器的是线程，自动调用了thread()
  ```

* 

# 一、cpp基础

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
| strlen(s1)                                             | 求const char*的长度。不包括结尾的\0。（sizeof()获得的字节数包括\0） |
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

| 功能                    |                                      |
| ----------------------- | ------------------------------------ |
| 拷贝s1到s2              | s2=s1                                |
| 拼接s1和s2              | s1+s2                                |
| str.size()、str.lengt() | 功能完全相同：求长度，不包括结尾的\0 |
|                         |                                      |
|                         |                                      |
|                         |                                      |

### （3）其他类型转字符串

`to_string()`是C++11标准。

```cpp
int a = 1;
std::string str = std::to_string(a);
```

```cpp
template<class T>
string NumToStr(T& num){
    ostringstream oss;
    oss<<num;
    string str(oss.str());
    return str;
}
```

### （4）字符串转其他类型

```cpp
str.c_str();		//从字符串返回一个C风格字符串：const char*。包括结尾的'\0'
```

```cpp
//string 转 int,long,double
string c = "3.14"
//方法一
cout << atoi(c.c_str()) << endl;	//str.c_str()从字符串返回一个C风格字符串：const char*
cout << atol(c.c_str()) << endl;	//atoi()是C中将const char*转int的函数
cout << atof(c.c_str()) << endl;  
//方法二
template<class T>
T StrToNum(const string& str){
    istringstream iss(str);
    T num;
    iss>>num;
    return num;
}
```



## 13. 引用

引用很容易与指针混淆，它们之间有三个主要的不同：

- 不存在空引用。引用必须连接到一块合法的内存。
- 一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。
- 引用必须在声明时被定义。指针可以先声明但不定义，在后面再定义。

当函数返回一个引用时，则返回一个指向返回值的隐式指针。这样，函数就可以放在赋值语句的左边



## 14. 时间

---

> `std::chrono`是C++11标准库中的一个模块，用于处理日期和时间。
>
> `<ctime>`是C风格的日期时间库。
>
> 用`std::chrono`，详见C++11。

## 16. 结构体

---

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

### （4）构造函数

编译器默认添加1个无参构造。	

**不要在构造里写业务。**

若显式添加任意一种构造（包括拷贝构造），则默认无参构造消失，需要手动添加。（C#也这样）

* 拷贝构造：

  ```cpp
  T t1 = T(t2);	//调用T的拷贝构造
  T t1 = t2;		//调用T的拷贝构造
  //调用=重载，不调用拷贝构造
  T t1;
  t1 = t2;
  ```

  

### （5）拷贝构造

**自定义类建议都写上拷贝构造和=操作符重载。**便于赋值、存放于容器中。

**拷贝构造的主要目的就是浅拷贝。**

**=赋值是调用了拷贝构造函数的。**

```cpp
class MyClass
{
    template<typename T> MyClass (const MyClass<T>& another) 
    {
   	    this.m_a = another.m_a;					//ClassName的普通成员
        this.p_addr_size = another.p_addr_size;	//容器大小
        this.p_addr = new T[this.p_addr_size];	//指针深拷贝
        for(int i=0;i<this.p_addr_size;++i)
        {
            this.p_addr[i] = another.p_addr[i];	//T是基本类型的话可以直接赋值。若T是复杂类型，需要重载=
        }
	}
    
    // 赋值运算符重载
    //如果返回直接对象MyClass，return *this时把临时对象拷贝一份给当前接收的变量，然后临时对象*this销毁。
    //返回的是引用MyClass&，通过引用指向临时变量，不发生拷贝，延长临时对象*this的生命周期，在原对象上进行操作。
    MyClass& operator=(const MyClass& other) {
		//如果是同一块地址就返回
        if (this == &other) 	//防止对象自己给自己赋值,a=a,会导致无限递归调用operator=()导致栈溢出
        {
            return *this;
        }
        x = other.x;
        return *this;
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

**什么情况会调用拷贝构造函数：**

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

### （6）移动构造

```cpp
A(const A&& another):m_ptr(another.m_ptr)
{
    //你的一部分资源（another的m_ptr指向的堆内存）归我了
    another.m_ptr=nullptr;		        //为了防止another析构的时候把资源释放掉
    std::cout<<"move construct..."<<std::endl;
}
```

### （7）父类引用指向子类

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

使用override关键字时，只需要在派生类的函数声明中添加即可，不需要在函数实现中使用。同时，override关键字的使用并不是强制性的。

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

**实际上理解为`readonly`更适合，即只读变量。并不是真正的常量。**通过const_cast可以强转成变量。

真正的常量是纯右值，字面量，如：1,2,3，"hello"等。

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

### （2）修饰参数列表

表明在函数体内不可修改这个参数

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

* 





## 25. static

> * 修饰类成员，表示成员属于类所有
> * 修饰全局变量：将全局变量的作用域仅为当前文件或函数，不可被其他文件或函数访问。
>
> * 修饰局部变量：延长生命周期，在函数结束时不会销毁变量，仍可访问。
>

* **非const静态成员必须在类外初始化**

```cpp
Test{ 
private: 
	static int m_c; 
} 
int Test::m_c=1;      //在类外初始化static变量m_c
```

非static成员在C++11标准后可通过=在声明处直接初始化。

```cpp
Test{ 
private: 
	int m_c = 1; 
} 
```

* **static成员函数只能访问static成员**（C#也一样）

  成员通过this指针实现和调用对象的绑定。

  因为static成员函数属于类所有，没有指代当前调用成员函数的对象的this指针，所以不能调用非static成员。


* **如果static成员函数想要访问非静态成员对象，也有办法：**

  在static成员函数内增加一个当前对象指针的参数，传入this，通过this获取实例的非静态成员对象

  ```cpp
  ```

  

* const静态成员可以在类内部初始化

  因为初始化后

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

  

## 27. delete关键字



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

---

### （1）getline()

有2种getline()函数

* `<istream>`中的getline函数

  | istream& getline(char* str, streamsize n)                 | 读取最多n个字符存储str中。n包含字符串结束标记符null，所以 最多读n-1个字符。 |
  | --------------------------------------------------------- | ------------------------------------------------------------ |
  | **istream& getline(char* str, streamsize n, char delim)** | **结束符delim会被读取，但不会放到str中**                     |

  ```cpp
  char name[256], wolds[256];
   cout<<"Input your name: ";
   cin.getline(name,256);
  ```

* `<string>`中的std::getline()（**用这种**）

  ```cpp
  <string>
  ```

  | istream& std::getline (istream& is, string& str);            | `getline`函数读取到文件末尾或者遇到错误时，它会返回一个空的`istream`对象，此时`while`循环的条件不成立，循环结束。 |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | **istream& std::getline (istream& is, string& str, ``char` `delim);** |                                                              |
  

### （2）标准输入输出

`cin、cout`

|                                                    |                                     |
| -------------------------------------------------- | ----------------------------------- |
| cout.put('h').put('e').put('l')<<endl;             | put一次输出一个字符，支持链式编程。 |
| cout.write("hello world!",strlen("hello world!")); | 输出字符串                          |
| char ch; ch=cin.get()或cin.get(ch);                | 读取一个字符到ch                    |

**标准错误流cerr：**非缓冲，插入到cerr的流会立即输出。

```cpp
cerr<<"error message.."<<endl;
```

**标准日志流clog：**clog对象是缓冲的。这意味着每个流插入到 clog 都会先存储在缓冲区，直到缓冲填满或者缓冲区刷新时才会输出。

这些小实例，我们无法区分 cout、cerr 和 clog 的差异，但在编写和执行大型程序时，它们之间的差异就变得非常明显。所以良好的编程实践告诉我们，使用 cerr 流来显示错误消息，而其他的日志消息则使用 clog 流来输出。

### （3）文本文件读写

* API

  ```cpp
  #include <fstream>	//文件流
  
  //读取字符
  istream& ifstream::open(path, std::ios::in);	//打开文件。openMode:std::ios::in
  bool ifstream::isopen();					  //判断打开是否成功
  istream& ifstream::get(char ch);			  //从流读个char到ch存储。读到末尾或错误返回空istream对象
  
  //写入字符
  ostream& ifstream::open(path, std::ios::out);	//打开文件
  ostream& ofstream::put(char ch);	//char写入流
  ostream& ofstream::write(const char* str, strlen);	//char[]写入流
  ```

  ```cpp
  #include <string>
  
  //读取行
  std::getline(istream& ism, string temp);
  //写入字符串
  
  ```

* demo

  ```cpp
  #include<iostream>
  #include <fstream>
  
  int main(int argc, char** args)
  {
      std::ifstream ifsm;
      ifsm.open(R"(C:\Users\Administrator\Desktop\todo.txt)", std::ios::in);
       if(!ifsm.is_open)
      {
          std::cerr<<"open readfile failed.."<<std::endl;
          return;
      }
      
      std::ofstream ofsm;
      ofsm.open(R"(C:\Users\Administrator\Desktop\_todo.txt)", std::ios::out | std::ios::app);
       if(!ofsm.is_open)
      {
          std::cerr<<"open writefile failed.."<<std::endl;
          return;
      }
  
      std::string strLine;	//存储每行
      if (ifsm.is_open())
      {
          while (std::getline(ifsm, strLine))
          {
              const char* cc = strtemp.c_str();	//ofsm.write第一个参数是const char*，c_str()不包括末尾\0
              ofsm.write(strtemp.c_str(), strtemp.length());
              ofsm.write("\n",1);		//每行结尾补个换行
          }
  
      }
      ism.close();
      osm.close();
  
      return 0;
  }
  ```

  ```cpp
  #include<iostream>
  #include <fstream>
  
  int main(int argc, char** args)
  {
      std::ifstream ifsm;
      ifsm.open(R"(C:\Users\Administrator\Desktop\todo.txt)", std::ios::in);
      if(!ifsm.is_open)
      {
          std::cerr<<"open readfile failed.."<<std::endl;
          return;
      }
      
      std::ofstream osm;
      ofsm.open(R"(C:\Users\Administrator\Desktop\_todo.txt)", std::ios::out | std::ios::app);
      if(!ofsm.is_open)
      {
          std::cerr<<"open writefile failed.."<<std::endl;
          return;
      }
     
      char ch;
      //一个char一个char读写。换行符和\0都不会漏掉
      while (ifsm.get(ch))
      {
      	ofsm.put(ch);
      }
     
      ism.close();
      osm.close();
  
      return 0;
  }
  ```

### （4）二进制文件读写

* API

  ```cpp
  #include <fstream>	//文件流
  
  //把二进制数据从文件中读取到对象instance中保存
  istream& ifstream::open(path, std::ios::in|std::ios::binary);	//打开文件
  bool ifstream::isopen();				//判断打开是否成功
  istream& ifstream::read(char* instance, int sizeOf);	//instance为对象的地址，sizeOf为该对象的sizeof
  
  //把对象instance的二进制数据写入到文件中
  istream& ofstream::open(path, std::ios::out|std::ios::binary);	
  istream& ofstream::write((char*)&instance, int sizeOf);	//sizeOf=sizeof(Instance)
  ```

* demo

  ```cpp
  class A
  {
  private:
  public:
      int a = 0;
      A(){}
      A(int a):a(a){}
      A (const A& another):a(another.a){}
      A& operator=(const A& another)
      {
          if (this == &another)
              return *this;
          this->a = another.a;
          return *this;
      }
      virtual ~A(){}
  };
  
  
  int main(int argc, char** args)
  {
      A a(20);
      std::cout << "a=" << a.a << std::endl;
  
      //写入文件
      std::ofstream ofsm;
      ofsm.open(R"(C:\Users\Administrator\Desktop\w.dat)", std::ios::out | std::ios::binary);
      if (!ofsm.is_open())
      {
          std::cerr << "open writefile failed.." << std::endl;
          return 0;
      }
      ofsm.write((char*)&a, sizeof(A));
      ofsm.close();
  
      //从文件中读取
      std::ifstream ifsm;
      ifsm.open(R"(C:\Users\Administrator\Desktop\w.dat)", std::ios::in | std::ios::binary);
      if (!ifsm.is_open())
      {
          std::cerr << "open readfile failed.." << std::endl;
          return 0;
      }
      A b;
      ifsm.read((char*)&b, sizeof(A));
      std::cout << "b=" << b.a << std::endl;
      ifsm.close();
  
      return 0;
  }
  ```

## 32. 异常

---

**不要用c++自带异常，尤其是对性能要求比较高的地方。**

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

也就是编译期多态，包括：函数重载（和返回值无关，虽然函数签名包括：返回值、参数列表，但返回值与重载无关）、操作符重载。

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

> 容器存入元素，都是将元素的**拷贝存入**容器中，不是将元素本身存入容器。所以把类对象放到容器中，一定要写类的拷贝构造和=重载。 （新标准有的函数不拷贝，但为了保险每个类都要写拷贝、移动、operator=）
>
> 使用算法需要引入`<algorithm>`头文件
>
> 计算容器占用内存：`v.count()*sizeof(typename)`，直接sizeof(v)计算的是容器变量自身的大小。

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

> 动态数组。压入、弹出都在尾部。

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

### 末尾添加数据

* **使用`vec.emplace_back();`，不要用`push_back()`**

  ```cpp
  vec.push_back();
  vec.emplace_back();
  ```

  两者的底层实现不同：push_back()是将元素构造后调用拷贝构造到容器中，emplace_back()则是在容器末尾直接构造元素。后者少一次拷贝，所以效率更高。

  而新标准的C++中push_back()在拷贝对象时，也会优先调用移动构造，若没移动构造会调用拷贝构造。

* 如果元素存在有参构造，可以直接传参数，push_back()和emplace_back()会直接调用构造

  ```cpp
  #include <iostream> 
  #include <vector> 
  using namespace std;
  
  class Person
  {
  private:
  	int age;
  	int bbb;
  public:
  	Person()
  	{
  		std::cout << "construct.." << std::endl;
  	}
  	Person(int a, int b) :age(a), bbb(b)
  	{
  		std::cout << "variable construct.." << std::endl;
  	}
  	Person(const Person& ano) :age(ano.age)
  	{
  		std::cout << "copy construct.." << std::endl;
  
  	}
  	Person(const Person&& ano) :age(ano.age)
  	{
  		std::cout << "move construct.." << std::endl;
  	}
  
  	Person& operator=(const Person& ano)
  	{
  		std::cout << "= construct.." << std::endl;
  
  		if (this == &ano)
  			return *this;
  		this->age = ano.age;
  		return *this;
  	}
  
  	~Person() {}
  };
  
  int main(int argc, char** argv)
  {
  	std::vector<Person> vec;
  	Person p;	//调用1次构造
  	vec.push_back(p);	//调用1次拷贝构造
  	vec.emplace_back(std::move(p));	//调用1次移动构造
  	//可以不在外面创建p
  	vec.emplace_back(1, 2);	//自动调用Person的有参构造，在vec尾部构造一个对象
  }
  ```

### 随机访问

可以用`[index]`也可以用`at(index)`

C++11增加了data()的用法，它返回内置vector所指的数组内存的第一个元素的指针：

**访问数据时，先获取指针数据vector::data()，然后用指针方式访问，会大大提高效率。**

```cpp
#include<iostream>
#include<vector>
using namespace std;

int main()
{
	vector<int> v;
	v.reserve(5);
	for(int i=0;i<5;i++)
    {
		v.push_back(i);
	}
	int* p=v.data();
	for(int i=0;i<v.size();i++)
    {
		cout<<p[i]<<endl;
	}
}
```

### 按pos插入、删除

```cpp
v.insert(iterator pos, int count, ele)——在pos前插入count个ele
v.erase(pos1, pos2)——删除pos1到pos2之间的元素
v.erase(pos)——删除pos处的元素
v.clear()——清空V
```

vector清空时，capacity不变，只size变化，因此仍占有较多的空间。

所以可用：

```cpp
vector<T>(v).swap(v)	//用v的size创建一个reserve再换给v
```

shrink：拷贝构造用capacity初始化自己的size、swap交换指针指向的内存块、vector(v)定义了一个匿名对象用v来拷贝。

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
| push()        | 入队 |
| emplace()     | 入队 |
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

## 8. 排序规则

---

回调函数的功能，每个元素都会经过回调函数的比较。

### （1）仿函数

有一些内置仿函数

```cpp
class Comp 	//仿函数functor
{ 
public: 
	bool operator()(typename val1,typename val2) 
	{ 
		return val1>val2;      //val1<val2规则为从小到大排列，val1>val2表示规则从大到小排列。 
	} 
}; 
```

### （2）lambda

泛型类型用`decltype()`推导lambda的类型。

```cpp
auto comp = [=](Person* a, Person* b) {return a->name < b->name; };
set < Person, decltype(comp)> s1(comp);
```

### （3）函数

```cpp
//函数指针
bool comp(Person a, Person b)
{
   return a.age < b.age;
}
using compPtr = bool(*)(Person,Person);
map<Person, int, compPtr> aaa(comp);
```

### （4）谓词

## 9. set

---

> 要指定排序规则。
>
> set的适用情形/作用：**快速检索、去重、排序**。 
>
> 存储数据时，会对数据自动排序。
>
> set不允许存储相同的数据，**multiset允许存储相同的数据**。
>
> **set、multiset内的元素都是只读。** 
>
> ```cpp
> #include <set>
> ```
>
> ```cpp
> //仿函数排序规则
> class Comp 	
> { 
> public: 
> 	bool operator()(typename val1,typename val2) 
> 	{ 
> 		return val1>val2;      //val1<val2规则为从小到大排列，val1>val2表示规则从大到小排列。 
> 	} 
> }; 
> 
> set<typename,Comp> s1;  //将类的类型作为第二参数传入，s1用该规则排序存储 
> ```
>
> ```cpp
> auto comp = [=](Person* a, Person* b) {return a->name < b->name; };
> set < Person, decltype(comp)> s1(comp);
> ```

set和multiset不能直接修改值，因为直接修改会使二叉树混乱，所以需要修改时应该先删除该元素，再insert()元素。 

| 常用API                                 |      |
| --------------------------------------- | ---- |
| insert(ele)——插入元素                   |      |
| erase(pos)/erase(ele)/erase(pos1, pos2) |      |
| clear()                                 |      |

## 10. multiset

> 允许存储相同的数据

## 11. pair

数据对

```cpp
pair<T1, T2> p = std::make_pair(val1, val2);
cout<<p.first<<p.second<<endl;

//也可以写成
auto p = std::make_pair(val1, val2);
```

## 12. map

> 存储的元素是pair，一个是key，一个是value。
>
> 要指定排序规则。
>
> **key不能重复，会对key自动排序，可指定排序规则。**
>
> 内部是红黑树。
>
> 一个key只能对应一个value。
>
> ```cpp
> #include <map>
> ```
>
> ```cpp
> //自定义排序规则仿函数Comp
> class Comp
> {
>     bool operator(Person a,Person b)
>     {
>         return a.age < b.age;	//ascend
>     }
> }
> 
> map<Person, int, Comp> aaa;
> ```
>
> ```cpp
> //lambda
> auto comp = [](Person a,Person b){return a.age < b.age;};
> map<Person, int, decltype(comp)> aaa(comp);	//decltype(comp)推导comp的类型
> ```
>
> ```cpp
> //函数指针
> bool comp(Person a, Person b)
> {
>    return a.age < b.age;
> }
> using compPtr = bool(*)(Person,Person);
> 
> map<Person, int, compPtr> aaa(comp);
> ```
>
> 

| 常用API                                                      |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| empty()——判断是否为空                                        |                                                              |
| size()——返回容器元素个数                                     |                                                              |
| pair<map<T1, T2>::iterator, bool> insert(make_pair(val1, val2)) | 插入pair，返回值first是map的迭代器，second是是否插入成功     |
| map[key]                                                     | 访问key对应的value。若key处的value不存在，则添加一个新的key/value对，并使用默认值初始化value。 |
| pair<iter,bool> insert(const pair<>& val)                    | 将val插入，返回迭代器和是否插入成功的bool                    |

**当key是自定义类时，需要用仿函数指定存储规则：**

```cpp
//insert
std::map<Person, int, comp> mp;
mp.insert(make_pair(new Person(), 1));
mp.insert({new Person(), 2});
```

## 13.multimap

> 允许存储**key相同但value**不同的pair
>
> key相同的数据放在一起连续存储
>
> 可以通过key查询到所有该key对应的value



## 14. unordered_map

**哈希表，只完成key到value的映射，不对key进行排序，查找比map快很多。**

```c++
#include<unordered_map>
```

## 15. sort

> 不是list的成员函数那个sort，是算法库里的sort。
>
> 要指定排序规则。
>
> 时间复杂度为`n*log(n)`
>
> ```cpp
> #include<algorithm>
> ```
>
> ```cpp
> //第三个参数传入函数地址或
> sort(iter1, iter2, &funcComp);	
> ```
>
> ```cpp
> //回调函数
> bool comp(int v1, int v2)
> {
>     return v1<v2;
> }
> //函数指针
> using CompPtr = bool(*)(int,int);
> CompPtr* compPtr = func;
> 
> //lambda
> auto f = [](int v1, int v2){return v1<v2;};
> 
> sort(iter1, iter2, comp);
> sort(iter1, iter2, compPtr);
> sort(iter1, iter2, f);
> ```
>
> 



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

# 三、现代C++

> 左值引用、右值引用
>
> 移动语义、完美转发
>
> 可变参数列表
>
> 智能指针

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

作用：**让编译器知道**是常量表达式，在编译期进行计算。

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

作用：通过表达式的类型进行推导。

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

作用：**表示类不能被继承。**

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

作用：就是**声明这个函数是重写的，让编译器检查**虚函数重写是否出错（比如拼写函数名），也让阅读代码更直观，一看就知道是重写。

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

## 10. 继承构造

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

> 初始化列表
>
> 在C++中，`std:.initializer list<T>`是一个模板类，接受任意个同类型参数，允许你以花括号{}初始化对象列表。它主要用于初始化包含多个元素的对象，如数组、vector等。当你用花括号初始化一个对象时，当使用花括号初始化时，如果存在接受std::initializer list<T>的构造函数，编译器将优先使用它，并用列表中的元素构造一个`std::initializer list<T>`对象。
>
> **可接受任意多个相同类型的参数。**
>
> 传参用列表里可传任意个数的参数。

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

```cpp
vector<vector<int>> vec;
vec.push_back({1,2});	//push_back有以std::initializer_list<int>为参数的重载
vec.emplace_back({1,2});	//error
```

当你在代码中使用`matrix.push back({1,2,3});`时，这里的{1,2,3}实际上是一个`std::initializer list9<int>`，编译器利用它来创建一个`std::vector<int>`的临时对象?，然后将这个临时对象添加到matrix中。std::vector有一个接受`std::initializer list<T>`作为参数的构造函数，所以这段代码可以成功编译。

然而，当你尝试使用`matrix.emplace back9({1,2,3});`时，出现的编译错误是因为emplace back期望直接构造其元素，避免额外的复制或移动操作。emplace back会尝试用提供的参数直接在vector的存储空间中构造一个`std::vector<int>`对象，而不是接受一个已经构造好的对象。在这种情况下，{1,2,3}没有明确地告诉编译器它应该如何直接构造一个`std:.vector<int>`对象。为了使`emplace back`工作，你需要直接传递构造`std::vector<int>`所需的参数。但在这个例子中，你无法仅通过{1,2,3}来直接在matrix末尾就地构造一个`std:.vector<int>`对象，因为这里的(1,2,3}并不能直接解释为构造`std::vector<int>`所需的所有参数。



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

**set中元素是只读的。**

## 14. 可调用对象包装器function

作用：**把不同类型的可调用对象包装成统一的包装器类型**，就可以用一种统一的方式来使用，或作为参数传递。

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

* 可以对这个得到的仿函数用包装器包装。包装器的泛型类型同被包装的函数。

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
  auto f = std::bind(可调用对象地址, 绑定的参数, 占位符)
  	auto f1 = bind(output, placeholders_1,2);		//10->placeholders_1->x
  	f1(10);
  	bind(output, placeholders_2,2)(10);		//10->10->placeholders_2->y
  //类的成员函数
  auto f = std::bind(可调用对象地址, 类实例对象的地址, 绑定的参数, 占位符)
      A a;
  	auto f1 = std::bind(&A::output, &a, 1, std::placeholders_2);	//x固定死是1
  	f1(10);	//y=10
  //成员变量
  auto f = std::bind(成员变量地址, 实例对象地址)
      A a;
      auto f = std::bind(&A::m_a, &a);
  	f()=100;	//包装一个变量，没参数不用传参，加个括号调用可以赋值
  ```

* 对bind绑定后得到的仿函数进行包装，得到包装器类型

  ```cpp
  //类的成员函数
  A a;
  auto f1 = std::bind(&A::output, &a, 1, std::placeholders_2);
  function<void(int,int)> f11 = f1;	//因为A::output的类型是void(int,int)
  //成员变量
  A a;
  auto f = std::bind(&A::m_a, &a);
  function<int&(void)> ff = f;	//m_a类型是int，变量不需要参数所以写void。读也可以写，所以是引用int&
  ```

## 16. 右值引用

**右值引用作用：**延长临时对象的生命周期。不发生拷贝，通过引用直接操作这个临时对象。提高程序效率（消耗很多资源创建的对象，不要用完就析构，通过右值引用它，可以直接用，还可以延长生命周期防止析构）。

左值引用作用：传参时，直接传本身。（传值传参，返回值时，会发生拷贝）

### （1）左值、右值

**怎么区分：能不能取地址。**

* 左值`lvalue`，locator value，可定位、可取地址。

  ```text
  左值 lvalue 是有标识符、可以取地址的表达式，最常见的情况有：
  
  变量、函数或数据成员
  返回左值引用的表达式
  如 ++x、x = 1、cout << ' '
  int x = 0;
  cout << "(x).addr = " << &x << endl;
  cout << "(x = 1).addr = " << &(x = 1) << endl;
  cout << "(++x).addr = " << &++x << endl;
  //cout << "(x++).addr = " << &x++ << endl; // error
  cout << "(cout << ' ').addr=" << &(cout << ' ') << endl;
  字符串字面量是左值，而且是不可被更改的左值。字符串字面量并不具名，但是可以用&取地址所以也是左值。
  如 "hello",在c++中是 char const [6] 类型，而在c中是 char [6] 类型
  cout << "(\"hello\").addr=" << &("hello") << endl;
  如果一个表达式的类型是一个lvalue reference (例如, T& 或 const T&, 等.)，那这个表达式就是一个lvalue。
  ```

* 右值`rvalue`，read value，只读。比如：**常量、函数返回值**。

  **右值有两种：纯右值（常量，无标识符，不能取址）、将亡值（将被销毁的，可以有标识符，但是不能取址）**

  ```text
  纯右值：
  
  纯右值 prvalue 是没有标识符、不可以取地址的表达式，一般也称之为“临时对象”。最常见的情况有：
  
  返回非引用类型的表达式
  如 x++、x + 1
  除字符串字面量之外的字面量如 42、true
  ```

  ```cpp
  将亡值：
  
  
  隐式或显式调用函数的结果，该函数的返回类型是对所返回对象类型的右值引用
  int&& f(){
      return 3;
  }
  
  int main()
  {
      f(); // The expression f() belongs to the xvalue category, because f() return type is an rvalue reference to object type.
  
      return 0;
  }
  对对象类型右值引用的转换
  int main()
  {
      static_cast<int&&>(7); // The expression static_cast<int&&>(7) belongs to the xvalue category, because it is a cast to an rvalue reference to object type.
      std::move(7); // std::move(7) is equivalent to static_cast<int&&>(7).
  
      return 0;
  }
  类成员访问表达式，指定非引用类型的非静态数据成员，其中对象表达式是xvalue
  struct As
  {
      int i;
  };
  
  As&& f(){
      return As();
  }
  
  int main()
  {
      f().i; // The expression f().i belongs to the xvalue category, because As::i is a non-static data member of non-reference type, and the subexpression f() belongs to the xvlaue category.
  
      return 0;
  }
  xvalue有标识符，所以也被称为lvalue。跟左值 lvalue 不同，xvalue 仍然是不能取地址的——这点上，xvalue 和 prvalue 相同。所以，xvalue 和 prvalue 都被归为右 值 rvalue。
  ```


### （2）引用

引用，就是别名，不增加额外内存开销。

**定义时就必须初始化。**

* 左值引用：

  **本身不是地址，绑定了变量的地址。**因此可修改绑定其变量的值，因为左值后面代表的是变量的地址。

  可接收：变量、临时对象、返回左值的函数返回值（能取地址的）

  不可直接接收：纯右值（常量、常量字符串等）

  ```cpp
  //左值
  int num = 1;
  //左值引用
  int& a = num;
  ```

* 右值引用：

  可接收：**常量、临时对象（将亡值）、返回右值的函数返回值**

  ```cpp
  //右值引用
  int&& a = 1;	//常量（纯右值）
  ```

* 特殊情况：常量左值引用、常量右值引用

  ```cpp
  //常量左值引用。四种都可以接收。
  const int& c =num;
  const int& c = a;
  const int& c = 1;
  const int& c = d;
  //常量右值引用
  const int&& d = 6;
  const int&& e = b;	//error。右值引用不能初始化另一个右值引用。右值引用只能通过右值初始化。
  ```

### （3）移动构造

**传入构造的参数是个右值引用**，**把这个右值引用的一部分资源移动给当前构造出来的这个对象用**，不要再自己开辟资源。

不是移动所有资源，至于移动哪些资源，由移动构造内的代码逻辑指定。

**传入的another是右值编译器才会调用移动构造，否则还是调用拷贝构造。**

一般都是转移堆内存。

目的就是浅拷贝，不发生耗费资源的深拷贝。但是直接的浅拷贝会对同一块堆内存析构两次。

```cpp
class A
{
private:
    int* m_ptr;
public:
    A getObj()
	{
   		A a;
    	return a;	//返回临时对象
	}
     A getObj1()
	{
    	return A();	//匿名对象。将亡值：将被销毁，不能取地址。
	}
    
    A(const A& another):m_ptr(new int(*(another.m_ptr)))
    {
        std::cout<<"copy construct.."<<std::endl;
    }
    //移动构造
    A(const A&& another):m_ptr(another.m_ptr)
    {
        //你的一部分资源（another的m_ptr指向的堆内存）归我了
        another.m_ptr=nullptr;		        //为了防止another析构的时候把资源释放掉
        std::cout<<"move construct..."<<std::endl;
    }
}

int main()
{
    //getObj内部的a是个局部变量，getObj执行完生命周期就结束了。所以编译器自动调用移动构造。
    A b = getObj();	
    //也可以写成：A&& b = getObj();
    
    //没有移动构造时。用右值引用接收，需要返回值是个将亡值。（匿名、临时）
    A&& c = getObj1();	//这里的c会被编译器看做左值
}
```

## 17. std::move

> 作用就是把**左值转换成右值**。
>
> std::move解决的问题是对于一个本身是左值的右值引用变量需要绑定到一个右值上，所以需要使用一个能够传递右值的工具，
>
> 将一个对象从左值转换为右值引用。它的作用是将对象的所有权从一个对象转移到另一个对象，从而实现移动语义。
>
> **移动语义使得在 C++ 里返回大对象（如容器）的函数和运算符成为现实，因 而可以提高代码的简洁性和可读性，提高程序员的生产率。**
>
> ```cpp
> #include <utility>
> ```

给右值引用初始化时，若右侧的值不满足条件（是左值），用move转换一下就成了右值。

```cpp
//c接收的是getObj1()返回的将亡值，编译器看成左值，直接用来初始化右值a会报错
A&& a = move(c)
```

使用场景：

* 给右值引用初始化

* 一个对象之后不再使用了需要销毁，但它的一部分资源，需要保留下来接着用。就用个右值引用接收

  ```cpp
  list<string> ls1{"s1", "s2", "s3", "s4"}
  list<string> ls2 = ls1;	//ls1到ls2发生了拷贝构造，重新开辟了资源
  list<string> ls3 = move(ls1);	//将ls1转成右值赋值给ls3，会调用移动构造，现在ls3拥有了ls1的资源。减少了拷贝次数，提高效率
  ```

## 18. std::forward

完美转发

> **作用：**模版推导时，参数传进来时是右值，就转发成右值。传进来是左值，就转发成左值。
>
> ```cpp
> #include <utility>
> 
> std::forward<T>(t);
> ```
>
> 将函数的参数以相同的类型和值完全传递给其他函数，包括左值引用和右值引用。这在模板编程中非常有用，因为它允许我们编写通用的函数模板，而不需要关心参数的类型和值。
>
> **为什么C++需要完美转发？**
>
> **一个绑定到universal reference上的对象可能具有 lvalueness 或者 rvalueness，正是因为有这种二义性**,所以催生了`std::forward`: 如果一个本身是 lvalue 的 universal reference 如果绑定在了一个 rvalue 上面，就把它重新转换为rvalue。**我们希望在传递参数的时候，可以保存参数原来的lvalueness 或 rvalueness，即是说把参数转发给另一个函数。**
>
> ```cpp
> 我们从一个简单的例子出发。 假设有这么一种情况，用户一般使用testForward函数，testForward什么也不做，只是简单的转调用到print函数。
> 
> template<typename T>
> void print(T & t){
>     std::cout << "Lvalue ref" << std::endl;
> }
> 
> template<typename T>
> void print(T && t){
>     std::cout << "Rvalue ref" << std::endl;
> }
> 
> template<typename T>
> void testForward(T && v){ 
>     print(v);//v此时已经是个左值了,永远调用左值版本的print
>     print(std::forward<T>(v)); //本文的重点
>     print(std::move(v)); //永远调用右值版本的print
> 
>     std::cout << "======================" << std::endl;
> }
> 
> int main(int argc, char * argv[])
> {
>     int x = 1;
>     testForward(x); //实参为左值
>     testForward(std::move(x)); //实参为右值
> }
> 上面的程序的运行结果：
> 
> Lvalue ref
> Lvalue ref
> Rvalue ref
> ======================
> Lvalue ref
> Rvalue ref
> Rvalue ref
> ======================
> 用户希望testForward(x);最终调用的是左值版本的print，而testForward(std::move(x));最终调用的是右值版本的print。
> 
> 可惜的是，在testForward中，虽然参数v是右值类型的，但此时v在内存中已经有了位置，所以v其实是个左值！（请仔细阅读这段话，保证你理解了）
> 
> 所以，print(v)永远调用左值版本的print，与用户的本意不符。print(std::move(v));永远调用右值版本的print，与用户的本意也不符。只有print(std::forward<T>(v));才符合用户的本意，这就是本文的主题。
> 通过forward，用户传进testForward的参数不论是左值、右值，都可以给T&&接收。
> 
> 不难发现，本质问题在于，左值右值在函数调用时，都转化成了左值，使得函数转调用时无法判断左值和右值。
> ```

* 转发可变参数列表

  ```cpp
  forward<Args>((args));
  ```

  



## 19. 万能引用、引用折叠、完美转发

https://zhuanlan.zhihu.com/p/99524127

### （1）万能引用

在每一个例子当中，如果你看到T&& (其中T是模板参数)，那这里就有类型推导，那T&&就是universal reference。如果你看到 “`&&`” 跟在一个具体的类型名后面 (e.g., `Widget&&`)，那它就是个rvalue reference。

我前面说过声明引用的时候必须用 “`T&&`”的形式才能获得一个universal reference。这个一个很重要的信息。再看看这段代码:

```cpp
template<typename T>
void f(std::vector<T>&& param);     // “&&” means rvalue reference
```

这里，我们同时有类型推导和一个带“`&&`”的参数，但是参数确不具有 “`T&&`” 的形式，而是 “`std::vector<t>&&`”。其结果就是，参数就只是一个普通的rvalue reference，而不是universal reference。 Universal references只以 “`T&&`”的形式出现！即便是仅仅加一个const限定符都会使得“`&&`”不再被解释为universal reference:

```cpp
template<typename T>
void f(const T&& param);               // “&&” means rvalue reference
```

现在， “`T&&`” 正是universal reference所需要的形式。这不是说你的模板参数非得要用T:

```cpp
template<typename MyTemplateParamType>
void f(MyTemplateParamType&& param);  // “&&” means universal reference
```

有的时候你可以在函数模板的声明中看到`T&&`，但却没有发生类型推导。来看下`std::vector`的 `push_back` 函数:[3]

```cpp
template <class T, class Allocator = allocator<T> >
class vector {
public:
    ...
    void push_back(T&& x);       // fully specified parameter type ⇒ no type deduction;
    ...                          // && ≡ rvalue reference
};
```

这里, `T` 是模板参数, 并且`push_back接受一个``T&&`, 但是这个参数却不是universal reference! 这怎么可能?

如果我们看看`push_back在类外部是如何声明的，这个问题的答案就很清楚了。`我会假装`std::vector`的 `Allocator` 参数不存在，因为它和我们的讨论无关。我们来看看没Allocator参数的`std::vector::push_back`:

```cpp
template <class T>
void vector<T>::push_back(T&& x);
```

`push_back`不能离开`std::vector<T>`这个类而独立存在。但如果我们有了一个叫做`std::vector<T>`的类，我们就已经知道了T是什么东西，那就没必要推导T。

举个例子可能会更好。如果我这么写:

```cpp
Widget makeWidget();             // factory function for Widget
std::vector<Widget> vw;
...
Widget w;
vw.push_back(makeWidget());      // create Widget from factory, add it to vw
```

代码中对 `push_back` 的使用会让编译器实例化类 `std::vector<Widget>` 相应的函数。这个`push_back` 的声明看起来像这样:

```cpp
void std::vector<Widget>::push_back(Widget&& x);
```

看到了没? 一旦我们知道了类是 `std::vector<Widget>`，`push_back`的参数类型就完全确定了: 就是`Widget&&`。这里完全不需要进行任何的类型推导。

对比下 `std::vector` 的`emplace_back`，它看起来是这样的:

```cpp
template <class T, class Allocator = allocator<T> >
class vector {
public:
    ...
    template <class... Args>
    void emplace_back(Args&&... args); // deduced parameter types ⇒ type deduction;
    ...                                // && ≡ universal references
};
```

`emplace_back` 看起来需要多个参数(Args和args的声明当中都有...)，但重点是每一个参数的类型都需要进行推导。函数的模板参数 `Args` 和类的模板参数`T`无关，所以即使我知道这个类具体是什么，比如说，`std::vector<Widget>`，但我们还是不知道`emplace_back`的参数类型是什么。

我们看下在类`std::vector<Widget>`外面声明的 `emplace_back`会更清楚的表明这一点 (我会继续忽略 `Allocator` 参数):

```cpp
template<class... Args>
void std::vector<Widget>::emplace_back(Args&&... args);
```



## 20. std::shared_ptr

**共享指针**，可以多个shared_ptr指向同一块内存，内部引用计数管理。

unique_ptr不能管理同一块内存。

```cpp
#include<memory>
```

### （1）初始化

**直接用堆内存+构造函数初始化的方式只能初始化一个共享指针。**

```cpp
std::shared_ptr<T> sp(创建堆内存)			//pt管理这块堆内存
std::shared_ptr<T> sp2 = pt1;						//pt2也管理pt1指向的这块内存
std::shared_ptr<T> sp3(move(pt1))					//move把pt1转成右值。pt1失去了管理的堆内存。
shared_ptr<T> pt4 = make_shared<T>(初始值);			//make_shared代替new开辟了内存、初始化值，然后让pt4管理

sp.reset()										//重置。ptr解除了对原内存的管理
sp.reset(new int(1))							//从指向当前堆内存解除管理，指向指定的新内存new int(1)
```

```cpp
std::shared_ptr<int> sp(new int(1));	//new int(1)堆内存初始化值为1
std::shared_ptr<int> sp = make_shared<int>(1);
```

* 获取内部原始指针

  ```cpp
  T* get()
  ```

* 获取引用计数

  ```cpp
  use_count()
  ```

### （2）使用方法

* 获取原始指针，进行操作

  ```cpp
  std::shared_ptr sp = make_shared(new A());
  A* sp = ptr.get();		//pta指向这个对象	
  sp->func();		
  ```

* 直接使用智能指针

  ```cpp
  sp->func();			//直接调用
  ```

### （3）删除器

第二个参数，传入删除器函数的地址。

本质是回调函数，当内部计数器为0时，自动调用，用来清理内存。类型是函数指针或函数对象。

智能指针管理的内存不是数组时，智能指针自己会在释放内存时调用删除器。

**C++11中管理的内存是数组时，需要手动指定删除器：**

**编译器一般都支持C++11以后版本，不用手动指定deleter。**

* 可以用默认删除器`default_deleter<T>`

  ```cpp
  std::shared_ptr<A> sp(new A()[5], default_deleter<A[]>());
  ```

* 也可以自己写lambda

  ```cpp
  std::shared_ptr<A> sp(new A(), [](A* p){
      delete p;
  });
  
  std::shared_ptr<A> sp(new A()[5], [](A* p){
      delete[] p;	//pa指向一个数组
  });
  ```

### （4）C++11以后版本对于数组也可以自动调用删除器

```cpp
std::shared_ptr<A[]> sp(new A[]);	//默认的删除器就行。把类型指定成数组类型就行
```

### （5）注意

* new出来的内存地址通过构造函数初始化共享指针，只能初始化一次。

  否则会导致内存析构2次。

  ```cpp
  int* p = new int(1);
  shared_ptr sp1(p);	//ok
  shared_ptr sp2(p);	//error
  //这种初始化的方式不对。可以用：
  shared_ptr sp2 = sp1;	//ok
  shared_ptr sp2(sp1);	//ok
  ```

* 不要直接return一个管理了this的shared_ptr（类里面）

  

  ```cpp
  class A
  {
  public:
      shared_ptr<A> getsharedptr()
      {
          //error
          //因为this和new A()是同一块内存。相当于用这块内存地址用构造初始化了2个共享指针。犯了注意点1的错
          return shared_ptr<A>(this);	
      }
  }
  
  int main()
  {
      shared_ptr<A> sp(new A());	
  }
  ```

  正确做法：

  ```cpp
  class A:public enable_shared_from_this<A>
  {
  public:
      shared_ptr<A> getsharedptr()
      {
       	return shared_from_this();
      }
  }
  
  int main()
  {
      shared_ptr<A> sp(new A());	
  }
  ```

  类去继承`enable_shared_from_this<T>`类，然后`return shared_from_this()`

  `enable_shared_from_this<T>`类内部有个weak_ptr，被继承后，这个类new的时候就会自动关联到父类这个弱指针，`shared_from_this()`就会调用弱指针的lock()函数返回一个shared_ptr。

### （6）std::weak_ptr

弱引用指针。

**作用：辅助shared_ptr，监视shared_ptr管理的内存。**

* 初始化

  ```cpp
  std::shared_ptr sp(new int);
  
  std::weak_ptr p1(sp);
  std::weak_ptr p2 = ptr1;
  std::weak_ptr p3(p1);
  ```

* use_count()

  获取shared_ptr管理的内存的引用计数

* expired()

  判断shared_ptr管理的内存是否已被释放

  ```cpp
  bool expired() const noexcept;
  ```

* lock()

  创建一个管理weak_ptr监视的这块内存的shared_ptr

  ```cpp
  shared_ptr<T> lock() const noexcept;
  ```

  ```cpp
  shared_ptr<int> sp1,sp2;
  sp1 = std::make_shared<int>(10);
  
  weak_ptr<int> sp3 = sp1;
  sp2=sp3.lock();		//ptr2此时也指向了ptr1管理的内存
  ```

  

## 20. std::unique_ptr

独占指针，某块内存只能有1个指针指向。

### （1）初始化

```cpp
unique_ptr<T> ptr1(new int(1));		//构造函数初始化
unique_ptr<T> ptr2 = move(ptr1);	//将ptr1转右值，资源转移给ptr2（调用了移动构造）
unique_ptr<int> ptr3 = func();		//将亡值的资源转移给了ptr3。（调用了移动构造）
ptr3.reset();						//重置
ptr3.reset(new int(10));			//重置，指向另一块内存

unique_ptr<int> func()
{
    return unique_ptr<int>(new int(10));	//将亡值。右值
}
```

### （2）删除器

手动指定，比共享指针稍微麻烦点，需要在模版类型中指定删除器类型。

一般不用手动指定，默认删除器会自己调用。

* lambda不捕获外部变量

  此时lambda是仿函数，但也可以用函数指针接收。

  ```cpp
  using delefunc = void(*)(int);
  unique_ptr<int, delefunc> ptr(new int(10),[](int* p){
      delete p;
  });
  ```

* lambda捕获外部变量

  此时lambda是仿函数，但是不能用函数指针接收了。

  但是仿函数、函数指针都是可以用可调用对象包装器包装的。

  注意：lambda的返回值、参数列表类型。

  ```cpp
  unique_ptr<int,function<void(int*)> ptr(new int(10), [=](int* p){
      delete p;
  });
  ```

* 默认删除器可管理数组

  ```cpp
  unique_ptr<A[]> sp(new A()[5]);
  ```

* shared_ptr循环引用导致内存泄漏

  内存计数永远不为0，永远不被释放回收，泄漏。

  

## 21. lambda

仿函数。不捕捉外部变量时，可看成函数指针。

## 22. delete

---

> 优先使用delete关键字删除函数而不是private却又不实现的函数  
>
> ```cpp
> template <class charT, class traits = char_traits<charT> >
> class basic_ios : public ios_base {
> public:
> ...
> basic_ios(const basic_ios& ) = delete;
> basic_ios& operator=(const basic_ios&) = delete;
> ...
> };
> ```

* 方便起见， 删除函数被声明为公有的， 而不是私有的。  

## 23. emplace_back()

> `emplace_back()` 会试图通过提供的参数直接在vector的存储空间构造元素，而不是接受1个已经构造好的元素。从而避免了拷贝或移动。
>
> 优点是避免了不必要的拷贝或移动构造过程。它优先通过在容器的尾部直接调用构造函数来构造新元素，从而减少了拷贝或移动构造的操作。
>
> **然而，在某些特定情况下，它仍然可能会触发拷贝构造函数。**这取决于具体的编译器、容器实现以及代码的具体使用情况。

对应`push_back()`

从C++11开始vector、list、deque、queue都有这个函数，相较于`push_back()`不用拷贝，效率更高。（用法：

* ```cpp
  #include <iostream> 
  #include <vector> 
  using namespace std;
  
  
  class Person
  {
  private:
  	int age;
      int bbb;
  public:
  	Person()
  	{
  		std::cout << "construct.." << std::endl;
  	}
  	Person(int a, int b) :age(a),bbb(b)
  	{
  		std::cout << "variable construct.." << std::endl;
  	}
  	Person(const Person& ano):age(ano.age)
  	{
  		std::cout << "copy construct.." << std::endl;
  
  	}
  	Person(const Person&& ano) :age(ano.age)
  	{
  		std::cout << "move construct.." << std::endl;
  	}
  
  	Person& operator=(const Person& ano)
  	{
  		std::cout << "= construct.." << std::endl;
  
  		if (this == &ano)
  			return *this;
  		this->age = ano.age;
  		return *this;
  	}
  
  	~Person() {}
  };
  
  int main(int argc, char** argv)
  {
  	std::vector<Person> vec;
  	Person p;	//调用1次构造
  	vec.push_back(p);	//调用1次拷贝构造
  	vec.emplace_back(std::move(p));	//调用1次移动构造
  	//可以不在外面创建p
  	vec.emplace_back(1, 2);	//自动调用Person的有参构造，在vec尾部构造一个对象
  }
  ```

  ```text
  construct..			//Person p
  copy construct..	//vec.push_back(p)
  move construct..	//std::move(p)
  copy construct..	//vec.emplace_back()
  variable construct..	//Person p(1)
  copy construct..		//
  copy construct..
  ```

  

* 区别：

  首先使用 `push_back()` 方法添加创建好的元素，可以看出使用到了**拷贝构造函数**。

  ```cpp
  int main() {
      using namespace std;
      vector<Person> person;
      auto p = Person(1); // >: Construct a person.1
      person.push_back(p);
      /**
       * >: Copy-Construct1 因为容器扩容，需要把前面的元素重新添加进来，因此需要拷贝
       */
  }
  ```

  然后再使用 `emplace_back()` 函数添加元素进来：

  ```cpp
  int main() {
      using namespace std;
      vector<Person> person;
      auto p = Person(1); // >: Construct a person.1
      person.emplace_back(move(p)); // >: Move-Construct1
      person.emplace_back(2);
      /**
       * >: Construct a person.2  // 构建一个新的元素
       * >: Move-Construct1       // 拷贝之前的元素过来，这个时候用的是 Person(const Person &&p)
       */
  }
  ```

  可以看到直接使用**构造参数列表**来添加元素的方法，它会使用到了**移动构造函数 `move`** 。这也是 `emplace_back()` 方法的一大特色。

## 24. emplace

对应`insert()`

```cpp
template< class... Args >
iterator emplace( const_iterator pos, Args&&... args );
```

## 25. tuple

这个可变参数模板类可以携带任意类型任意个数的模板参数：

```cpp
std::tuple<int> tp1 = std::make_tuple(1);
std::tuple<int, double> tp2 = std::make_tuple(1, 2.5);
std::tuple<int, double, string> tp3 = std::make_tuple(1, 2.5, “”);
```

## 26. std::chrono

---

> https://zhuanlan.zhihu.com/p/373392670
>
> C++11添加的时间库
>
> ```cpp
> #include <chrono>
> ```
>
> `chrono::system_clock`来源是系统时钟。然而在大多数系统上，系统时间是可以在任何时候被调节的。所以如果用来计算两个时间点的时间差，这并不是一个好的选择。
>
> `chrono::steady_clock`是一个单调时钟。此时钟的时间点无法减少，因为物理时间向前移动。因而**steady_clock是度量间隔的最适宜的选择。**

### （1）时间段

> 类型是`std::chrono::duratioin`
>
> **时间段可以直接使用`+`和`-`相加相减**
>
> **时间段可以通过`==`判断是否相等**
>
> 调用duration类的`count()`成员函数来获取具体数值

| std::chrono::nanoseconds  | duration<至少 64 位的有符号整数类型, std::nano>        |
| ------------------------- | ------------------------------------------------------ |
| std::chrono::microseconds | duration<至少 55 位的有符号整数类型, std::micro>       |
| std::chrono::milliseconds | duration<至少 45 位的有符号整数类型, std::milli>       |
| std::chrono::seconds      | duration<至少 35 位的有符号整数类型>                   |
| std::chrono::minutes      | duration<至少 29 位的有符号整数类型, std::ratio<60>>   |
| std::chrono::hours        | duration<至少 23 位的有符号整数类型, std::ratio<3600>> |

```cpp
// 时间段单位转换
auto toHours = std::chrono::duration_cast<std::chrono::hours>(secs); 
auto toSeconds = std::chrono::duration_cast<std::chrono::seconds>(hours); 
```

```cpp
//想要知道2个小时零5分钟一共是多少秒

chrono::hours two_hours(2);		//创建2hours的时间段
chrono::minutes five_minutes(5);
auto duration = two_hours + five_minutes;	//时间段求和
auto duaration_seconds = chrono::duration_cast<chrono::seconds>(duration);	//时间段转成秒单位
cout << "02:05 is " << duaration_seconds.count() << " seconds" << endl;		//获取秒计时的数
```

### （2）时间点

> 类型是`std::chrono::time_point`
>
> 时间点重载了操作符，**可通过`==`，`!=`，`<`，`<=`，`>`，`>=`操作符来实现比较操作。**
>
> ```text
> 时间点 + 时间段 = 时间点
> 时间点 - 时间点 = 时间段
> ```

```cpp
auto start = chrono::steady_clock::now();	//获取当前时间点
double sum = 0;
for(int i = 0; i < 100000000; i++) {
    sum += sqrt(i);
}
auto end = chrono::steady_clock::now();		//获取当前时间点

auto time_diff = end - start;	//计算时间段
auto duration = chrono::duration_cast<chrono::milliseconds>(time_diff);	//将时间段转成ms单位
cout << "Operation cost : " << duration.count() << "ms" << endl;
```

## 27. 分数

---

> 提供了编译期的比例计算功能
>
> ```cpp
> #include <ratio>
> ```

* API

  ```cpp
  类成员Num即为分子，类成员Denom即为分母。我们可以直接通过调用类成员来获取相关值。    
  ratio_add，ratio_subtract，ratio_multiply，ratio_divide来完成分数的加减乘除四则运算
  ```

  ```cpp
  #include <iostream>
  #include <chrono>
  #include <ctime>
  #include <ratio>
  
  
  int main()
  {
      std::ratio<5, 22> r1;
      std::ratio<4, 33> r2;
      
      std::ratio_add<std::ratio<5, 22>, std::ratio<4, 33>> result;
  	
      std::cout << "res=" << ((double)result.num / (double)result.den) << std::endl;
  
      return 0;
  }
  ```

  





## 28. 可变模版参数

---

https://www.cnblogs.com/qicosmos/p/4325949.htmls

> 它对参数进行了高度泛化，它能表示0到任意个数、任意类型的参数。
>
> 声明可变参数模板时需要在typename或class后面带上省略号“...”。比如我们常常这样声明一个可变模版参数：template<typename...>或者template<class...>
>
> ```cpp
> template <class... Args>
> void f(Args... args);
> ```
>
> 省略号的作用有两个：
>
> 1.Args是**可变参数列表**类型（也叫：参数包）
>
> 2.`...Args`表示声明一个可变参数列表的类型，这个类型的参数包args中可以包含0到任意个模板参数；
>
> **3.在args右侧时，表示将参数包对象args展开成一个一个独立的参数，即：`args...`**
>
> args前面有省略号，它就是可变参数列表，它里面包含了0到N（N>=0）个模版参数。
>

### （1）可变模版参数函数

通过递归函数展开参数包，需要提供一个参数包展开的函数和一个递归终止函数，递归终止函数正是用来终止递归的。

```cpp
#include <iostream>
using namespace std;
//递归终止函数
template <class T>
void print(T t)
{
   cout << t << endl;
}
//展开函数
template <class T, class ...Args>
void print(T head, Args... rest)
{
   cout << "parameter " << head << endl;
   print(rest...);	//参数包rest必须展开才能传进print
}


int main(void)
{
   print(1,2,3,4);	//编译器自动推导了T的类型。显式指定类型是print<int>(1, 2, 3, 4);
   return 0;
}

//调用过程是这样的：
print(1,2,3,4);
print(2,3,4);
print(3,4);
print(4);
```

```cpp
#include <iostream>
#include <utility>	//foward
using namespace std;

template<typename T>
T func(T&& t)
{
    return forward<T>(t);
}

template<typename T, typename... Args>
T func(T&& t, Args&&... args)
{
    return forward<T>(t) + func<T>(forward<Args>(args)...);	//先对args完美转发，再展开传进func
}

int main()
{
    cout << func<int>(1, 2, 3, 4) << endl; 
}
```



### （2）可变模版参数类





# 四、C++11线程库

> ```cpp
> #include <thread>
> ```
>
> 不是Posix系统编程，C++11标准库的线程库是跨平台的。使用Posix标准函数只能在Unix平台上编程的OS级API。
>
> 但底层也是封装的Posix线程API 。

## 1. Thread创建

---

> Thread的禁用了拷贝和operator=。
>
> ```cpp
> #include <thread>
> ```

```cpp
//一创建就开始执行func
//args是传入到func的参数
std::thread th(func, args);

//主线程阻塞在该语句，等待子线程执行完毕
th.join();

//当前线程休眠
std::this_thread::sleep_for(std::chrono::microseconds(100));	//seconds休眠单位秒

//获取当前线程ID
 std::this_thread::get_id();


//分离子线程（使用的少）
//主线程可以结束。子线程在后台执行
th.detach();

//判断子线程是否可以调用join
//有的线程不能使用join，使用join会报错system_error。所以调用join前一般使用joinable()判断一下
bool th.joinable();
```

## 2. 线程函数中的数据未定义错误

---

* 线程函数的参数类型是左值引用

  解决：要用`ref()`把局部变量转成左值引用再传给线程函数。

  ```cpp
  void test(int& a)
  {
      a += 1;
  }
  
  int main(int argc, char** args)
  {
      //error
      //std::thread th1(test, 1);
      
      //error
      //int a = 1;
      //int& ra = a;
      //std::thread th1(test, ra);
  
      //OK
      int a = 1;	//a在主线程中定义，在主线程结束前地址一直存在
      std::thread th1(test, std::ref(a));	//ref(a)转成左值引用，传递地址
      
      th1.join();
  }
  ```

* 指针或引用指向局部变量

  **本质：主线程和子线程各跑各的，主线程先结束，局部变量被销毁，子线程通过引用（地址）访问该局部变量时，访问的值随机。**

  **解决：设法让变量在子线程访问之前不会被销毁。**

  ```cpp
  std::thread th1;
  void test(int& a)
  {
      std::this_thread::sleep_for(std::chrono::seconds(5));
      a += 1;
      std::cout << a << std::endl;
  }
  
  void func()
  {
      //a定义在func中，开线程对a进行操作，若func先执行完就会对a销毁。若test还未执行对a的操作，就无法正确访问a
      //解决：可以把a放到func()外面定义，延长生命周期
      int a = 1;	
      th1= std::thread(test, std::ref(a));
      std::cout << a << std::endl;
  }
  
  int main(int argc, char** args)
  {
      func();
      th1.join();
  }
  ```

  ```cpp
  void func(int* ptr)
  {
      *ptr+=1;
      std::cout<<*ptr<<std::endl;
  }
  
  int main()
  {
      int ptr = new int(1);
  	std::thread th(func, ptr);   
  	//解决：用智能指针跟踪ptr。线程调用ptr，所以ptr不会被释放。
      delete ptr;		//可能在子线程还没访问ptr那块地址时，ptr被某些操作意外释放了。然后子线程才访问，出现错误。
  	
      
      th.join();
  }
  ```

  ```cpp
  #include <memory>
  class A
  {
      public:
      	void func()
          {
              std::cout<<"hello"<<std::endl;
          }
  }
  
  int main()
  {
      //和上面一样。a可能在th线程还未执行func时就被意外释放了。
      //解决：智能指针，子线程里用到a，那么a就不会被释放。
      //shared_ptr<A> a = std::make_shared<A>();
      //std::thread th(a.func, a);
      A a;
      std::thread th(a.func, &a);	//a.func()实际是A::func(&a)
  	delete a;	//a被释放了
      
      th.join();
  }
  ```

* 线程想调用类的私有成员方法

  把调用代码封装成函数，设定为类的友元函数。

  ```cpp
  class A
  {
      //friend void functhread();
      private:
      	void func(){}
  }
  
  /*
  void functhread()
  {
      shared_ptr<A> a(new A());
      std::thread th(a->func, a);	//报错，func是private的。把这几行封装成函数，设定为A的友元函数。
      
      th.join();
  }
  */
  
  int main()
  {
      shared_ptr<A> a(new A());
      std::thread th(a->func, a);	//报错，func是private的。把这几行封装成函数，设定为A的友元函数。
      
      th.join();
  }
  ```

##  **不能多个线程同时对一块内存进行读写。**这是最根本的问题。

---

## 3. 互斥量mutex

---

> 当多个线程都会去读写一个变量时，可能1线程还未把执行结果写进变量，CPU就切到线程2执行了，线程2读的是线程1操作前的。又可能切到1运行，写进变量，又切到2运行，可当前2会把自己执行的结果覆盖1写进变量的结果。
>
> 当有线程在**读或写**一个变量时，叫做**共享变量**，不允许其他线程读或写这个变量。
>
> **线程安全：多线程执行的结果和单线程执行结果一致，就是线程安全。**

```cpp
#include <mutex>

std::mutex mtx;
int a = 0;

//lock()和unlock()中间的部分是临界区，其他线程访问时会被阻塞
mtx.lock();		//lock()当前线程获取互斥量所有权
a+=1;
mtx.unlock();	//unlock()释放互斥量所有权
```

## 4. 死锁

---

lock()获取互斥量。

lock()与unlock()中间代码为临界区，临界区内代码是原子操作，不被其他线程打断。在临界区内操作共享变量。

只有互斥量没有处于被其他线程获取的情况下，才能被获取。

当互斥量比较多、代码多的时候容易出现死锁。	

```cpp
std::mutex mtx1;
std::mutex mtx2;

void functhread1()
{
    mtx1.lock();
    mtx2.lock();	//等待mtx2释放
    mtx1.unlock();	//释放mtx1
}

void functhread2()
{
    mtx2.lock();
    mtx1.lock();	//等待mtx1释放
    mtx2.unlock();	//释放mtx2	
}
```

## 5. lock_guard

---

> STL中的一种互斥量封装类。
>
> **自动加锁解锁，**对于传入的互斥量在构造中加锁、析构中解锁。
>
> 代码执行到lock_guard对象作用域外时，解锁。
>
> 当作用域结束会调用析构内的unlock()解锁。
>
> **禁用了拷贝构造，=拷贝**
>
> ```cpp
>//lock_guard源码
> //RAII
>template <class _Mutex>
> class _NODISCARD lock_guard { // class with destructor that unlocks a mutex
> public:
> using mutex_type = _Mutex;
> 
> explicit lock_guard(_Mutex& _Mtx) : _MyMutex(_Mtx) { // construct and lock
> _MyMutex.lock();
> }
> 
> lock_guard(_Mutex& _Mtx, adopt_lock_t) : _MyMutex(_Mtx) {} // construct but don't lock
>   
> ~lock_guard() noexcept {
> _MyMutex.unlock();
> }
> 
> lock_guard(const lock_guard&) = delete;
>   lock_guard& operator=(const lock_guard&) = delete;
> 
> private:
> _Mutex& _MyMutex;
> };
> ```

```cpp
std::mutex mtx;
int data = 0;
void func1()
{
    std::lock_guard<std::mutex> lg(mtx);	//拿到了mtx
    for (int i = 0; i < 10000; i++)
    {
        data++;
    }
    //lg作用域结束，解锁
}

//局部作用域
void func2()
{
    {
        std::lock_guard<std::mutex> lg(mtx);	//加锁
        dosomething(data);	//操作共享变量
    }
    //剩下代码对非共享变量的操作。但是作用域还没完。
    ......
}


int main(int argc, char** args)
{
    std::thread th1(func1);
    std::thread th2(func1);

    th1.join();
    th2.join();

    std::cout << "data=" << data << std::endl;
}
```

```cpp
//互斥锁+双重检查实现懒汉式单例模式
static std::mutex mtx;
class LazyA
{
private:
    static LazyA* ins;
public:
    lazyA(){}=delete;
    ~lazyA(){}=delete;
   
    static LazyA& getInstance()
    {
         if (ins == nullptr)
         {
             std::lock_guard<std::mutex>  lg(mtx);
             if (ins == nullptr)
             {
                 ins = new LazyA();
             }
         }
         return *ins;
    }
    
    void printMsg(std::string msg)
    {
        std::cout << msg << std::endl;
    }
};
LazyA* LazyA::ins = nullptr;	//非const静态变量必须在类外初始化

void func()
{
    LazyA::getInstance()->printMsg("hello");
}

int main(int argc, char** args)
{
    std::thread th1(func);
    std::thread th2(func);

    th1.join();
    th2.join();

    return 0;
}
```

## 6. std::unique_lock（常用）

---

> ```cpp
> #include <mutex>
> ```
>
> 是lock_guard的加强版，所以用这个比较多。
>
> **作用域为临界区，临界区内操作原子操作。**
>
> **可使用局部作用域，**让临界区尽量小，最好只影响共享变量。对于不是共享资源的操作，最好放到临界区外。
>
> 若一个共享变量访问频率很高，可以给这个共享变量单独1个锁。

### 上锁

**调用构造时开始上锁。**

```cpp
std::unique_lock<std::mutex> ul(mtx);	//上锁

std::unique_lock<std::mutex> ul(mtx, std::defer_lock);	//延迟上锁
ul.lock();	//手动上锁
```

### 解锁

* 析构时自动解锁

* 或手动调用

  ```cpp
  ul.unlock();
  ```

### 超时

**`std::mutex`不支持时间操作。改用`std::timed_mutex`**

设定时间内尝试获取锁，超时就返回false

普通的加锁拿不到时会阻塞在这里，`try_lock_for`只阻塞规定时间。

```cpp
//这2行代码放在同一个作用域内
std::unique_lock<std::timed_mutex> ul(mtx, std::defer_lock);	//std::defer_lock设定延迟加锁了。构造中没有加锁
ul.try_lock_for(std::chrono::seconds(5));		//尝试获取锁5s，超过5s没有获取锁就返回false
```

## 7. 原子量atomic

---

> 互斥量，通过上锁、解锁，解决多线程执行临界区内代码问题
>
> 变量是原子量的话，本身的操作就是原子性的
>
> 原子量不能拷贝或移动
>
> ```cpp
> #include <atomic>
> ```

* API

  ```cpp
  //原子获取值
  load();
  //原子赋值
  store();
  ```

* demo

  ```cpp
  #include <iostream> 
  #include <atomic>
  #include <thread>
  
  std::atomic<int> data;
  
  void func()
  {
  	for (int i = 0; i < 1000000; i++)
  	{
  		data++;	//不用加锁了。对data的操作本身就是原子的
  	}
  }
  
  int main()
  {
  	std::thread th1(func);
  	std::thread th2(func);
  
  	th1.join();
  	th2.join();
  	std::cout << data.load() << std::endl;
  }
  ```

## 8. call_once

---

> 多个线程去调用某个函数。**call_once确保该函数只会被调用1次。**
>
> ```cpp
> call_once(std::once_flag, &func);
> ```
>
> call_once只能在线程中使用。在main()中调用会报错。

```cpp
#include<iostream>
#include <thread>
#include <string>
#include <mutex>

static std::once_flag onceflag;  //没有拷贝构造、=拷贝
class LazyA
{
private:
    static LazyA* ins;
    LazyA(){}   //构造不允许直接访问
public:
    LazyA(LazyA& another) = delete; //删除拷贝构造
    LazyA& operator=(LazyA& another) = delete;  //删除=
    virtual ~LazyA() {};
    static LazyA& getInstance()
    {
        if (!ins)
        {
            std::call_once(onceflag, &initIns);
        }
        return *ins;
    }

    static void initIns()	//getInstance()是static的，所以initIns也要是static的才可调用
    {
        if (!ins) 
        {
            ins = new LazyA();
        }
    }

    void printMsg(std::string msg)
    {
        std::cout << msg << std::endl;
    }
};
LazyA* LazyA::ins = nullptr;

void func()
{
    LazyA::getInstance().printMsg("hello");
}


int main(int argc, char** args)
{
    std::thread th1(func);
    std::thread th2(func);

    th1.join();
    th2.join();

    return 0;
}
```

## 9. condition_variable

---

>  **条件变量**
>
>  控制线程的阻塞和继续执行。（阻塞在条件变量cv上）
>
>  **当线程阻塞在条件变量cv上时，释放互斥锁mtx给其他线程。当线程继续向下执行时，拿到互斥锁mtx。**
>
>  所以构造条件变量时，要把互斥锁作为参数传入。
>
>  ```cpp
>  #include <condition_variable>
>  ```
>
>  ```cpp
>  std::unique_lock<std::mutex> ul(mtx);	
>  std::condition_variable cv;
>  
>  //Predicate成立，即true时向下执行。false时阻塞等待，释放锁mtx。
>  cv.wait(ul, Predicate);	//Predicate是谓词，是可调用对象，通常用lambda，
>  
>  cv.notify_one();	//通知一个线程解除阻塞向下执行
>  ```
>
>  子线程123从任务队列中取任务执行，任务队列为空时，子线程需要暂停从队列中取任务。
>
>  线程暂停通过`wait()`阻塞等待获取条件变量来实现。
>
>  生产者向队列中加任务时，调用`notify_one()`或`notify_all()`通知等待条件变量的线程继续执行线程。
>
>  ![image-20240428172036409](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240428172036409.png)

```cpp
#include<iostream>
#include <thread>
#include <string>
#include <mutex>
#include <condition_variable>
#include <queue>


std::mutex mtx;	//互斥量
std::unique_lock<std::mutex> ul(mtx);	//互斥锁
std::condition_variable cv;	//条件变量
std::queue<int> q;  //多个线程共同访问的队列

void producer()
{
    for (int i = 0; i < 100; i++)
    {
        q.push(i);
        //通知一个线程来取
        cv.notify_one();
    }
}

void consumer()
{
    int value;
    while (true)
    {
        //若q为空则等待
        cv.wait(ul, []() {
            return !q.empty();
            });
        value = q.front();
        std::cout << value << std::endl;
        q.pop();
    }
}

int main(int argc, char** args)
{
    std::thread th1(producer);  //线程1执行生产者，往队列q里放任务（这里用int替代）
    std::thread th2(consumer);  //线程2执行消费者，从队列q里取任务（这里用int替代）

    th1.join();
    th2.join();

    return 0;
}
```

## 10. 异步

---

https://www.cnblogs.com/qicosmos/p/3534211.html

### （1）std::async

> **获取子线程执行的返回值。**
>
> async是更方便的异步操作，不用自己创建子线程，还能通过`std::launch`指定线程启动的策略。
>
> 为啥要有async：对`package_task`、`std::future`使用的简化。不用再去拿package_task包装任务，再创建thread，再调用`get_future`获取`future`了。
>
> **首选async解决异步问题。**
>
> ```cpp
> #include <future>
> ```

* API

  ```cpp
  std::future = async(std::launch::async | std::launch::deferred, f, args...);
  
  //std::launch::async——调用async()时就开线程执行函数f（默认情况）
  //std::launch::deffered——调用async()时不开线程执行f，等调用future.get()或future.wait()时才开线程执行
  //f——任务
  //args——传进f的参数
  ```

  ```cpp
  #include <iostream> 
  #include <utility> 
  #include <atomic>
  #include <thread>
  #include <future>
  #include <chrono>
  
  int func()
  {
  	int data = 0;
  	for (int i = 0; i < 1000000000; i++)
  	{
  		data++;
  	}
  	return data;
  }
  
  int main()
  {
  	std::future<int> future_ret2 = std::async([]() {
  		auto start = std::chrono::steady_clock::now();
  		int a=0;
  		for (int i = 0; i < 1000000; i++)
  		{
  			a++;
  		}
  		auto end = std::chrono::steady_clock::now();
  		std::cout << "use:	" << std::chrono::duration_cast<std::chrono::microseconds>(end - start).count() << "ms\n" << std::endl;
  		return a;
  		});
  
  
  	auto status = future_ret2.wait_for(std::chrono::microseconds(2200));
  	if (status == std::future_status::timeout)
  	{
  		std::cout << "timeout.." << std::endl;
  	}
  	else if(status == std::future_status::ready)
  	{
  		std::cout << "done.." << std::endl;
  		std::cout << future_ret2.get() << std::endl;
  	}
  }
  ```

### （2）std::future

> **提供了一种访问异步操作结果的机制。搭配async或promise使用。**
>
> 因为一个异步操作我们是不可能马上就获取操作结果的，只能在未来future某个时候获取。
>
> 可通过枚举`future_status`查询任务执行的状态。
>
> 注意：给接左值引用的函数传future时，用`ref(future)`

* API

  ```cpp
  //阻塞调用线程，直到获取异步返回值
  get();
  //阻塞调用线程，直到超时，或者获取异步返回值
  future_status wait_for(std::chrono::microseconds(1000));
  
  //状态
  std::future_status::ready		//任务已完成
  std::future_status::timeout		//规定时间内未完成
  ```

  ```cpp
  //搭配async使用时：
  int main()
  {
  	std::future<int> future_ret2 = std::async([]() {
  		int a=0;
  		for (int i = 0; i < 1000000; i++)
  		{
  			a++;
  		}
  		return a;
  		});
  
  	auto status = future_ret2.wait_for(std::chrono::microseconds(100));	//wait_for阻塞执行，返回状态
  	if (status == std::future_status::timeout)	//100ms内没执行完任务
  	{
  		std::cout << "timeout.." << std::endl;
  	}
  	else if(status == std::future_status::ready)	//100ms内执行完
  	{
  		std::cout << "done.." << std::endl;
  		std::cout << future_ret2.get() << std::endl;	//get()获取返回值
  	}	
  }
  ```

### （3）std::promise

> **不同线程之间传递数据。**
>
> **`future.get()`会一直阻塞，直到另一个线程中的`promise.set_value()`调用赋值后。**

```cpp
std::promise 只能被设置一次值，如果尝试多次设置值，将会抛出异常。此外

#include <iostream>      
#include <functional>    
#include <thread>        
#include <future>         
#include <exception>    // std::exception, std::current_exception

void get_int(std::promise<int>& prom) {
    int x;
    std::cout << "Please, enter an integer value: ";
    std::cin.exceptions (std::ios::failbit);   // throw on failbit
    try {
        std::cin >> x;                         // sets failbit if input is not int
        prom.set_value(x);
    } catch (std::exception&) {
        prom.set_exception(std::current_exception());
    }
}

void print_int(std::future<int>& fut) {
    try {
        int x = fut.get();
        std::cout << "value: " << x << '\n';
    } catch (std::exception& e) {
        std::cout << "[exception caught: " << e.what() << "]\n";
    }
}

int main ()
{
    std::promise<int> prom;
    std::future<int> fut = prom.get_future();

    std::thread th1(get_int, std::ref(prom));
    std::thread th2(print_int, std::ref(fut));

    th1.join();
    th2.join();

    return 0;
}
```

### （4）std::packaged_task

> 别用这个。
>
> 还要自己创建thread，传进任务，不方便。async已经简化了操作。
>
> 流程：
>
> 对可调用对象（函数、lambda、bind()返回值）进行包装。包装后，可通过`get_future()`获取`std::future`，通过future获得返回值。

```cpp
std::packaged_task<int()> task([](){ return 7; });
std::thread t1(std::ref(task)); 	//注意：这里要传进 ref(task)，直接传task报错
std::future<int> f1 = task.get_future(); 
auto r1 = f1.get();
```

## 11. 线程池实现

---

> ![threadpool](https://pic2.zhimg.com/80/v2-c0855ad44ea989c4d2825a2e1d98aecd_1440w.webp)

线程池一般全局就1个，可以禁用拷贝构造和operator=()，同时使用单例模式封装。

```cpp
//无管理线程版本
#include <iostream> 
#include <mutex>
#include <queue>
#include <vector>
#include <functional>
#include <condition_variable>

class ThreadPool
{
public:
	ThreadPool(int num)
	{
		stopAll = false;	//线程池对象停止标志
		m_numThreads = num < 1 ? 1 : num;	//最少1个线程
		
		for (int i = 0; i < m_numThreads; i++)
		{
			m_threads.emplace_back([=]() {	//emplace_back移动语义，不拷贝
				while (true)	
				{
					std::unique_lock<std::mutex> ul(mtx);	//互斥锁，锁住对共享变量m_tasks的访问
					cv.wait(ul, [=]() {						//条件变量控制锁ul的释放、线程的阻塞和继续
						return !m_tasks.empty() || stopAll;	//任务队列非空或线程池停止则不阻塞，拿到ul
						});
					if (stopAll == true && m_tasks.empty())	//如果线程池停止线程立即return终止
					{
						return;
					}
					std::function<void()> task(std::move(m_tasks.front()));	//从头部取任务，移动语义减少拷贝，可调用对象包装器包装
					m_tasks.pop();	//任务队列弹出
					ul.unlock();	//任务队列访问结束，解锁放掉ul
					
                      std::cout << "threadID=" << std::this_thread::get_id() << ",start working..." << std::endl;
					task();	//线程执行任务
                      std::cout << "threadID=" << std::this_thread::get_id() << ",end working..." << std::endl;	//线程执行完任务日志输出
				}
				});
		}
	}
	ThreadPool(ThreadPool& another) = delete;	//禁用拷贝
	ThreadPool& operator=(ThreadPool& another) = delete;	//禁用operator=
	~ThreadPool()
	{
		//析构销毁线程池，释放资源
		std::unique_lock<std::mutex> ul(mtx);	//要访问共享变量stopAll（多个线程都访问了stopAll)，上锁
		stopAll = true;	//线程池停止标志置true
		ul.unlock();	//stopAll访问结束，解锁
		cv.notify_all();	//线程池要销毁了。通知所有线程全部不阻塞向下执行
		for (auto& item : m_threads)
		{
			item.join();	//等待全部线程return终止
		}
	}

	//主线程添加任务。生产者
	template<typename F, typename... Args>
	void enqueue(F&& f, Args&&... args)	//万能引用
	{
		auto task = std::bind(std::forward<F>(f), std::forward<Args>(args)...);	//f和args是万能引用需要完美转发，通过bind把参数绑定到函数f上
		//局部作用域，限定互斥锁ul锁定范围
		{
			std::unique_lock<std::mutex> ul(mtx);	//要访问共享变量m_tasks，上锁
			m_tasks.emplace(std::move(task));		//任务队列里放1个任务，移动语义避免拷贝
		}
		cv.notify_one();	//放了个任务，解除1个线程的阻塞向下执行任务
	}

private:
	std::mutex mtx;	//互斥量
	std::condition_variable cv;	//条件变量
	bool stopAll;
	int m_numThreads;	//线程数
	std::queue<std::function<void()>> m_tasks;	//任务队列。bind绑定了参数，所以function的模版类型是void()
	std::vector<std::thread> m_threads;	//线程容器
};


std::mutex mtxfunc;
int data;
void func()
{
	//std::unique_lock<std::mutex> ul(mtxfunc);
	for (int i = 0; i < 1000000; i++)
	{
		data++;
	}
	std::cout << "threadID="<<std::this_thread::get_id()<<", data= " << data << std::endl;
}
```

调用时有些注意点：

* 放入线程池的函数，可能本身也会访问共享变量。要加锁：

  ```cpp
  std::mutex mtxfunc;
  int data;
  void func()
  {
  	std::unique_lock<std::mutex> ul(mtxfunc);	//要加锁
  	for (int i = 0; i < 1000000; i++)
  	{
  		data++;
  	}
  	std::cout << "threadID="<<std::this_thread::get_id()<<", data= " << data << std::endl;
  }
  
  int main()
  {
  	auto start1 = std::chrono::steady_clock::now();
  	ThreadPool* tp = new ThreadPool(6);
  	tp->enqueue<void()>(func);	//类型是func函数的类型
  	tp->enqueue(func);	//自动推导类型
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	auto end1 = std::chrono::steady_clock::now();
  	delete tp;
  	std::cout << "MainThreadID=" << std::this_thread::get_id() << ", final data= " << data << std::endl;
  
  	auto start2 = std::chrono::steady_clock::now();
  	std::cout << "pool spend:" << std::chrono::duration_cast<std::chrono::microseconds>(end1 - start1).count() << "ms" << std::endl;
  	for (int i = 0; i < 6000000; i++)
  	{
  		data++;
  	}
  	auto end2 = std::chrono::steady_clock::now();
  	std::cout << "mainthread spend:" << std::chrono::duration_cast<std::chrono::microseconds>(end2 - start2).count() << "ms" << std::endl;
  }
  
  //pool spend:157ms
  //mainthread spend:12353ms
  ```

* 主线程可能不等线程池中线程执行结束就结束了。所以需要在主线程最后打印data前调用到线程池的析构，join()等待所有线程执行完。

  比如下面的代码就会有问题：

  ```cpp
  //子线程还没执行完，主线程已经执行到cout了
  //结果会先打印： final data，再子线程打印
  int main()
  {
  	ThreadPool* tp = new ThreadPool(2);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	
  	std::cout << "MainThreadID=" << std::this_thread::get_id() << ", final data= " << data << std::endl;
      delete tp;
  }
  ```

  ```cpp
  //和上面一样问题，主函数执行到cout，线程池还没执行到析构
  int main()
  {
  	ThreadPool tp(2);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	tp->enqueue(func);
  	
  	std::cout << "MainThreadID=" << std::this_thread::get_id() << ", final data= " << data << std::endl;
  }
  ```

## 12. 总结

---

* 锁解决的问题：

  多条线程访问（多或写）共享变量。**共享变量要放在临界区内访问。**

* 条件变量解决的问题：

  线程什么时候阻塞暂停、什么时候继续执行，条件变量控制有几条线程正在执行。

  条件变量需要一个互斥锁来使用，阻塞在条件变量上时，互斥锁被释放，其他线程可以进入执行。阻塞解除时，拿到互斥锁，向下执行。

* 



## 13. 协程

---

https://blog.csdn.net/github_18974657/article/details/108526591

> 协程就是一种特殊的函数，它可以在函数执行到某个地方的时候暂停执行，返回给调用者或恢复者（可以有一个返回值），并允许随后从暂停的地方恢复继续执行。注意，这个暂停执行不是指将函数所在的线程暂停执行，而是单纯的暂停执行函数本身。
>
> 那么，这种特殊函数有什么用呢？最常见的用途，就是将“异步”风格的编程“同步”化。
>
> C++20引入了协程。

# 五、模版

## 模板的二次编译

模板函数、模板类都要经过2次编译：1.检查语法是否有错误；2.根据调用模板处的具体数据类型，决定泛型类型。



## 函数模板

在函数模版里，`&&`是通用引用，不仅仅是右值引用。传入的是左值就接左值，传入右值就接右值。

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

### 使用模板要把声明和头文件写在一起写成hpp

上面的代码会报错，因为模板的2次编译，以及每个.cpp文件都是编译成单独.o目标文件再链接的。所以在.main文件中include"Person.h"文件后，main文件中只有模板类Person的声明，这样第一次编译时只检查语法错误，虽然在main文件中对Person类进行了特化\<int>。由于头文件中无函数定义，所以编译器会认为这里的函数实现在其他文件中，会在这里做一个符号代替函数，将找函数实现的工作交给连接器。

而编译Person.cpp时，只有定义，却无法获得特化的T具体类型。因此最后Person.o和main.o链接时，main.o中的符号会找不到。

**解决：模板函数、模板类的声明和实现不要分开写在不同文件中。可以用将声明和实现放在一个.hpp文件中。**

### 如果分开写成.h和.cpp，要把两个文件都include，`#include "test.cpp"`



## 特化

https://zhuanlan.zhihu.com/p/346400616



