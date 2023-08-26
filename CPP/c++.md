## SourceInsight入门使用

https://blog.csdn.net/mjy520123/article/details/120297021



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
  extern "C" {  
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
  extern "C" {  
  	#include "test_extern_c.h"  
  } 
  //如果仅仅只使用1个函数ThisIsTest，就extern修饰该函数
  extern "C"{  
  	int ThisIsTest(int, int);            
  } 
  ```

## static

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

## register

用于定义寄存器变量，表示该变量被频繁使用，**可以**（不是一定存在寄存器中）存储在CPU的寄存器中，以提高程序的运行效率。

## auto

* 自动类型推断

  声明时就必须要初始化

* 占位符

  函数返回类型后置，该方法主要用于函数模板的返回值推导。

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

## decltype

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

函数名：常量指针，指向数组首元素，保存数组首元素的地址。

```c++
double *p;
double runoobAarray[10];

p = runoobAarray;
```



### （3）数组作为形参

**指针形式：**

```cpp
void printArray(int(*arr)[8]) {
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
    printArray(&a);
    printArray(&b);
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

传统的：

```cpp
void func(int arr[], int len)
{
    for (int i = 0 ; i < length; ++i){
        std::cout << arr[i] << std::endl;
    }
}
void func(int* arr, int len){}
```

**用数组指针或数组引用**作为函数参数，有如下优点：

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

1. 采用指针接收数组首地址的方式返回。

2. 因为返回的是局部变量的地址，因此数组应开辟在堆区以避免被系统回收。

   new数组或者将局部变量的数组声明为静态static。

3. 返回值除了包含指针，还应该包含数组长度。（sizeof(指向首地址的指针)=4而不是数组大小）。

### （5）遍历数组

```cpp
for(auto var : arr)
{
    std::cout<<var;
}
```

## 11. noexcept



## 12. 字符串

### （1）C风格的字符串

以`\0`结束的字符数粗。

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
- 引用必须在创建时被初始化。指针可以在任何时间被初始化。

当函数返回一个引用时，则返回一个指向返回值的隐式指针。这样，函数就可以放在赋值语句的左边



## 14. 时间

https://www.runoob.com/cplusplus/cpp-date-time.html

## 15. IO

cin/cout

**标准错误流cerr：**非缓冲，插入到cerr的流会立即输出。

```cpp
cerr<<"error message.."<<endl;
```

**标准日志流clog：**clog对象是缓冲的。这意味着每个流插入到 clog 都会先存储在缓冲区，直到缓冲填满或者缓冲区刷新时才会输出。

这些小实例，我们无法区分 cout、cerr 和 clog 的差异，但在编写和执行大型程序时，它们之间的差异就变得非常明显。所以良好的编程实践告诉我们，使用 cerr 流来显示错误消息，而其他的日志消息则使用 clog 流来输出。

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

### （1）构造函数的列表初始化：

```cpp
Line::Line( double len): length(len)
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
  
  CMyClass::CMyClass(int x, int y) : m_y(y), m_x(m_y)
  {
  };
  //初始化的顺序是先m_x再m_y。
  ```

  所以建议按照声明顺序来写初始化列表。

* 构造函数先执行初始化列表，再执行函数体内容。

### （2）继承

* 继承目的是为了复用代码，不要按照自然属性划分父类、子类，以功能划分。

  ```cpp
  class Rectangle: public Shape
  ```

  https://zhuanlan.zhihu.com/p/619347450

  多继承：一个子类有多个父类。

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

* 构造函数调用顺序：基类 > 成员类 > 派生类：

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

  ### （3）拷贝构造

```cpp
classname (const classname &obj) {
   // 构造函数的主体
}
```

C++ primer p406 ：拷贝构造函数是一种特殊的构造函数，具有单个形参，该形参（常用const修饰）是对该类类型的引用。当定义一个新对象并用一个同类型的对象对它进行初始化时，将显示使用拷贝构造函数。当该类型的对象传递给函数或从函数返回该类型的对象时，将隐式调用拷贝构造函数。

**C++支持两种初始化形式：**

拷贝初始化 **int a = 5;** 和直接初始化 **int a(5);** 对于其他类型没有什么区别，对于类类型直接初始化直接调用实参匹配的构造函数，拷贝初始化总是调用拷贝构造函数，也就是说：

```
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

深拷贝需要在拷贝构造里重新new新的堆内存。

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

 覆盖是存在类中，子类重写从基类继承过来的函数。被重写的函数不能是static的。必须是virtual的。但是函数名、返回值、参数列表都必须和基类相同（发生在基类和子类）

 重定义也叫做隐藏，子类重新定义父类中有相同名称的非虚函数 ( 参数列表可以不同 ) 。（发生在基类和子类）

## 19. 虚函数

**虚函数：** 是在基类中使用关键字 **virtual** 声明的函数。哪怕是父类引用指向子类对象，也会根据所调用的对象类型来选择调用的函数。类似C#中的父类virtual函数、子类override。

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

**总结：**

定义纯虚函数是为了实现一个接口，起到一个规范的作用，规范继承这个类的程序员必须实现这个函数

定义虚函数是为了允许用基类的指针来调用子类的这个函数。
