# 1. Makefile

指定编译、链接规则的脚本。

## 1）编译指令

```bash
g++ -E aaa.cc -o aaa.i	#预编译
g++ -S aaa.i -o aaa.s	#编译
g++ -c aaa.o -o aaa.s	#汇编
g++ aaa.o -o aaa		#链接
```

```bash
#优化选项
-o，同o1
-o1，不影响编译速度的前提下，尽量采用一些优化算法降低代码大小和可执行代码的运行速度
-o2,该优化选项会牺牲部分编译速度，提高目标代码的运行速度（推荐）
-o3，不推荐
#参数
-g，表示生成包含调试信息的可执行文件。可进行gdb调试。会增加可执行文件大小，所以最后打包时不加-g。
-W，在编译中开启一些额外的警告（warning）信息。-Wall，将所有的警告信息全开。
-l, 指定所使用到的库。
-L，默认在/usr/bin中搜索链接库。-L增加搜索链接库目录。可用冒号分割路径。
-I, 指定头文件所在的文件夹
```

```bash
# 生成静态库
ar rsvc math_functions.o libmath_functions.a 

# 生成动态链接库
g++ math_functions.o -shared -fPIC -o libmath_functions.so 	#-shared表示生成动态库，-fPIC生成位置无关代码

# 链接静态库
g++ main.cpp -o main -L. -l math_functions -static

# 或者链接动态链接库
g++ main.cpp -o main -L. -l math_functions
```

## 2）make

```bash
make	#执行Makefile脚本
make -j4	#以4线程执行脚本。线程数以CPU核心数的2倍为宜
```



## 3）赋值指令

* =，使用”=”进行赋值，变量的值是整个makefile中最后被指定的值

  ```bash
  VIR_A = A
  VIR_B = $(VIR_A) B
  VIR_A = AA
  #经过上面的赋值后，最后VIR_B的值是AA B，而不是A B。在make时，会把整个makefile展开，拉通决定变量的值
  ```

* :=，就是普通意义上的赋值
* ?=，设定变量的默认值。若变量未赋值，则赋值等号后的值
* +=，添加。

## 4）小项目Makefile

标签下不仅仅可以是编译指令，也可以是bash指令。

**make指令将会运行 all标签下指定的编译。**

aaa1:aaa1.cc表示aaa1依赖aaa1.cc。

**make clean指令将执行clean标签下的命令。**

**make aaa3指令将执行aaa3标签下的命令。**

命令需要以[TAB]键开始。

```bash
all:aaa1 aaa2 aaa3 aaa4 aaa5	#1行写不下可以在一行末尾加上\

aaa1:aaa1.cc
	g++ -o aaa1 aaa1.cc	#行首必须是一个Tab，不能是4个空格
aaa2:aaa2.cc
	g++ -o aaa2 aaa2.cc	
aaa3:aaa3.cc
	g++ -o aaa3 aaa3.cc	
aaa4:aaa4.cc
	g++ -o aaa4 aaa4.cc	
aaa5:aaa5.cc
	g++ -o aaa5 aaa5.cc	

clean:
	rm -f *.o
	rm -f aaa1 aaa2 aaa3 aaa4 aaa5
```

## 5）使用变量

类似宏定义。

定义变量，都是字符串格式。以`$()`使用。

**.PHONY**修饰的目标就是只有规则没有依赖。

* 使用变量替代在编译指令中**多次重复出现**的、**很长一串**的或是**需要修改**的参数。

  ```bash
  all:aaa1 aaa2 aaa3 aaa4 aaa5	
  
  CC:g++
  RMF:rm -f
  aaa1:aaa1.cc
  	$(CC) -o aaa1 aaa1.cc	
  aaa2:aaa2.cc
  	$(CC) -o aaa2 aaa2.cc	
  aaa3:aaa3.cc
  	$(CC) -o aaa3 aaa3.cc	
  aaa4:aaa4.cc
  	$(CC) -o aaa4 aaa4.cc	
  aaa5:aaa5.cc
  	$(CC) -o aaa5 aaa5.cc	
  
  clean:
  	$(RMF) *.o
  	$(RMF) aaa1 aaa2 aaa3 aaa4 aaa5
  ```

* 使用通配符简化编译指令

  > %.c   %.o表示任意的.c或.o
  >
  > *.c  *.o表示所有的.c或.o
  >
  > $@  目标文件的集合
  >
  > $^    所有的依赖文件的集合
  >
  > $<    所有依赖文件的第一个文件，若有多个目标文件，依次编译依赖文件

## 6）单目标文件模板

```bash
target=main   //想要生成的可执行文件名称可自定义，此处为main
src=$(wildcard *.c)
deps=$(wildcard *.h)
obj=$(patsubst %.c,%.o,$(src))

$(target):$(obj)
  gcc $^ -o $@ -Wall

%.o:%.c $(deps)
  gcc -c $< -o $@ -Wall

.PHONY:clean
clean:
  -rm -rf $(target) $(obj) 
```

## 7）多目标模板

依赖关系的表示却十分简单，即使有几百个依赖关系也同样适用，所以这种写法对大型项目的开发是非常有好处的。

```bash
target1=server        //服务器运行目标文件server
target2=client        //客户端目标文件client
src=$(wildcard *.c)   //用wildcard函数找到所有.c文件，server.c、client.c、wrap.c
deps=$(wildcard *.h)   //用wildcard函数找到所有的.h文件，wrap.h
obj=$(patsubst %.c,%.o,$(src))    #用patsubst函数将所有.c文件替换成.o文件。
								#为什么这里的patsubst函数不生效？？？？
#obj = $(src:%.c=%.o)

all:$(target1) $(target2)    //目标文件server和client，多个目标文件一定形成此依赖关系

$(target1):server.o wrap.o  //server的依赖为server.o和wrap.o
  gcc $^ -o $@ -Wall   //利用gcc编译器生成可执行文件server
  
$(target2):client.o wrap.o  //client的依赖为client.o和wrap.o
  gcc $^ -o $@ -Wall  //利用gcc编译器生成可执行文件client

%.o:%.c $(deps)  //任意一个.o中间目标文件的依赖是其对应的.c文件，如：client.o的依赖为client.c
  gcc -c $< -o $@ -Wall   //根据目标文件编译的需求依次将依赖编译成对应的中间目标文件

.PHONY:clean  //伪文件，需要在终端输入make clean才会调用
clean:
  -rm -rf $(target1) $(target2) $(obj)   //删除所有的目标文件以及中间目标文件，用于重新编译。
```

```bash
target1=server        
target2=client        
src=$(wildcard *.c)   
deps=$(wildcard *.h)   
obj=$(patsubst %.c,%.o,$(src))
#obj = $(src:%.c=%.o)
CC=g++

all:$(target1) $(target2)    

$(target1):server.o wrap.o  
	$(CC) $^ -o $@ -Wall   
  
$(target2):client.o wrap.o  
	$(CC) $^ -o $@ -Wall  

%.o:%.c $(deps)  
	$(CC) -c $< -o $@ -Wall   

.PHONY:clean 
clean:
	rm -rf $(target1) $(target2) *.o   
```

---

# 2. Cmake安装

跨平台、开源，自动构建工具。

不是包管理工具。

Makefile不跨平台，cmake会根据编译器类型来确定是否生成Makefile，大部分情况会生成。

不要改cmake生成的Makefile。

**大型项目不要用cmake。**

## （1）Windows下使用cmake

Windows下默认的cpp编译器（generator）是MSVC。你可以自己安装MinGW（gcc、clang）。

可以通过参数设定cmake不使用默认的MSVC：

> cmake -G "MinGW Makefiles"

不要去看Windows下的cmake底层相关。

## （2）Linux下使用cmake

### 1）安装

* Source distribution是源码，需要编译：

  ```bash
  // 进入解压好的cmake文件夹
  cd cmake-3.22.4
  
  // 编译安装
  ./bootstrap
  make -j4	// 4线程
  sudo make install
  
  cmake --version
  ```

* 安装完后不要将此文件夹删除，方便卸载：

  ```bash
  sudo make uninstall
  ```

* 添加到环境变量

  ```bash
  终端输入以下命令回车：ln -sf cmake安装的位置/bin/* /usr/local/bin/
  
  注解：如果发生 没有权限的问题
  
  请使用 sudo ln -sf /home/************
  如果还是不行 请使用root权限来添加软链接。 sudo su 输入密码 后续再次执行ln ********命令即可
  ```
  

### 2）使用

CMake主要是编写CMakeLists.txt文件，然后用cmake命令将CMakeLists.txt文件转化为make所需要的makefile文件，最后用make命令编译源码生成可执行程序或共享库（so(shared object)）。

**一般把CMakeLists.txt文件放在工程目录下**，使用时，先创建一个叫build的文件夹（这个并非必须，**因为cmake命令指向CMakeLists.txt所在的目录**，例如**cmake ..** 表示CMakeLists.txt在当前目录的上一级目录。cmake后会生成很多编译的中间文件以及makefile文件，所以一般建议新建一个新的目录build，专门用来编译），然后执行：

```cmake
cd build 
cmake .. 
make	#也可用cmake --build . 
```

* Cmake语法编写CmakeList.txt文件:

  ```cmake
  **cmake_minmium_required(VERSION 3.20)**——指定最低版本
  
  **project(Hello)**——指定项目名称
  
  **add_executable(Hello Hello.cpp)**——指定依赖
  ```

* **cmake -B build**——创建build目录，并且在build目录下生成Makefile及其他文件。

  省略了：

  ```cmake
  mkdir build
  cmake ..
  ```

* **cmake --build build**——生成项目

# 3. Cmake语法

## （1）message

```cmake
message("hello")
message(hello)
message([[hello]])
# 获取Cmake中的信息
message(${CMAKE_VERSION})
#打印环境变量值
message($ENV{PATH})	
```

## （2）变量

* 变量命名区分大小写

* **变量都是按照字符串存储**

* 变量获取：`${val}`

  ```cmake
  message("value=${val}")
  ```

### 1）Set方法

```cmake
#给valname设定单个值
set(valname value)
message(${valname})
#给list赋值
set(listName val1 val2 val3)
set(listName val1;val2;val3)
message(${valname})
```

例：

```cmake
set(ENV{CXX} "g++")
message($ENV{CXX})
```

### 2）unset

```cmake
unset(ENV{CXX})
```

### 3）List

操作list。

```cmake
#赋值
list(APPEND listName val1 val2 val3)
message(${listName})
```

`list(操作 操作对象 存储操作结果)`

```cmake
#获取长度
list(LENGTH listName len)
message(${len})
```

```cmake
#获取下标
list(FIND listName val2 index)
message(${index})
```

```cmake
#删除元素
list(REMOVE_ITEM listName val2)
message(${listName})
```

```cmake
#插入
list(INSERT listName 2 val4)	#val1 val2 val4 val3
```

```cmake
#翻转
list(REVERSE listName)
```

```cmake
#排序
list(SORT listName)
```

## （3）if

```cmake
if(condition)
	...
elseif()
	...
else()
	...
endif()
```

例：

```cmake
set(VARBOOL)

if(VARBOOL)
	message(TRUE)
else()
	message(FALSE)
```

```cmake
if(NOT VARBOOL)
	
if(VARBOOL OR VARBOOL)

if(VARBOOL AND VARBOOL)

if(1 LESS 2)

if(1 EQUAL "1")
```

## （4）循环

```cmake
foreach(VAR RANGE 3)
	message(${VAR})
endforeach()
```

```cmake
#常用
set(MYLIST 1 2 3)
foreach(VAR IN MYLIST 4 f)
	message(${VAR})
endforeach()
#1234f
```

## （5）函数

```cmake
cmake_minimum_required(VERSION 3.20)

function(MyFunc FirstArg)
	message("function's name:${CMAKE_CURRENT_FUNCTION}")
	message("firstArg=${FirstArg}")
	set(FirstArg "new value")
	message("new firstArg=${FirstArg}")
	#还有通用的访问参数的方法
	message("firstArg=${ARGV0}")
	message("secondArg=${ARGV1}")
	message("thirdArg=${ARGV2}"
endfunction()

set(FirstArg aaa)
MyFunc(${FirstArg} "bbb")
#function's name:MyFunc
#firstArg=aaa
#new firstArg=bbb
#firstArg=aaa
#secondArg=bbb
#thirdArg=
```

## （6）作用域









