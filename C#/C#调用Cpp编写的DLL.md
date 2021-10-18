# C#调用以C/C++编写的DLL



https://www.cnblogs.com/byvar/p/3914703.html

## 1. 导入

https://www.pianshen.com/article/89371200012/

**生成的.dll文件和.lib文件放入项目根目录下，以及/bin下的生成.exe的目录下。**

```C#
[DLLImport("Dlltest.dll")]
```



## 2. c++使用vector，C#无法对应数据类型

https://bbs.csdn.net/topics/250082890?list=1358739

![image-20211018004827370](https://i.loli.net/2021/10/18/gXpMYw8WTi51VfJ.png)

https://bbs.csdn.net/topics/70062716

https://blog.csdn.net/weixin_45993808/article/details/115483667?spm=1035.2023.3001.6557&utm_medium=distribute.pc_relevant_bbs_down.none-task-blog-2~all~baidu_landing_v2~default-1.nonecase&depth_1-utm_source=distribute.pc_relevant_bbs_down.none-task-blog-2~all~baidu_landing_v2~default-1.nonecase

## 3. C#调用Cpp DLL存在的问题

#### C#调用时不像C++调用，不能直接访问全局变量。所以最好在编写Cpp DLL时，若有需要，将对全局变量的读写，封装成get和set函数。

#### Cpp中函数接口若调用了cout，在C#中是无法调用控制台打印的

#### C#和Cpp存在变量类型的差异。因此Cpp中一些Cpp独有的类型如vector等不要暴露接口，传到C#中也会错误。

#### 总之，一个原则C#只能调用DLL暴露的函数。若一些需求实在需要访问Cpp中的一些数据结构、全局变量、逻辑代码，可以将所需的所有封装成函数传出，由C#调用。

#### 但是换个思路，将一个语言写成DLL被另一个语言调用不是好的做法。可以通过将Cpp处理后的数据写入文件，C#再读文件，这样进行数据交互。读写硬盘中文件速度受限的话，可以通过读写内存中数据的方法。（具体查询C#、Cpp读写内存中数据方法。）但也有人说这种方法很麻烦，可以直接用C++/CLI操作。

