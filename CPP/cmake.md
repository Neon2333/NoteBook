#   1. Makefile

---

指定编译、链接规则的脚本。

## 1）编译指令

```bash
g++ -E aaa.cc -o aaa.i	#预编译
g++ -S aaa.i -o aaa.s	#编译
g++ -c aaa.s -o aaa.o	#汇编
g++ aaa.o -o aaa		#链接

#可写成：
g++ -o aaa.o -c aaa.cpp
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
#库名都要以lib开头

# 生成静态库
ar rsvc libmath_functions.a math_functions.o

# 生成动态链接库
g++ -shared -fPIC -o libmath_functions.so math_functions.o  	#-shared表示生成动态库，-fPIC生成位置无关代码

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

# 2. Cmake安装

---

跨平台、开源，自动构建工具。

不是包管理工具。

Makefile不跨平台，cmake会根据编译器类型来确定是否生成Makefile，大部分情况会生成。

不要改cmake生成的Makefile。

**大型项目不要用cmake。**

## （1）Windows下使用cmake

Windows下默认的cpp编译器（generator）是MSVC。你可以自己安装MinGW（gcc、clang）。

**设定编译器：**可以通过参数设定cmake不使用默认的MSVC：

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

建议不要用make执行cmake生成的Makefile，使用cmake自己的指令。

**一般把CMakeLists.txt文件放在工程目录下**，使用时，先创建一个叫build的文件夹（这个并非必须，**因为cmake需要指定CMakeLists.txt所在的目录下**，例如**cmake ..** 指定在CMakeLists.txt在当前目录的上一级目录。cmake后会生成很多编译的中间文件以及makefile文件，所以一般建议新建一个新的目录build，专门用来编译），然后执行：

```cmake
cd build 
cmake .. 	#因为CMakeLists.txt在build的上一级目录，cmake ..指定在build的上级目录运行cmake
make	
#也可用cmake --build . 
```

* Cmake语法编写CmakeList.txt文件:

  ```cmake
  cmake_minmium_required(VERSION 3.20)	#指定最低版本
  
  project(Hello)	#指定项目名称
  
  add_executable(Hello Hello.cpp)		#指定依赖
  ```

* **cmake -B build**——创建build目录，并且在build目录下生成Makefile及其他文件。

  省略了：

  ```cmake
  mkdir build
  cd build
  cmake ..
  ```

* **cmake --build build**——生成项目

# 3. Cmake语法

---

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

变量都是传值使用，函数内修改变量不会改变函数外部同名变量。

## （7）宏

会读就行。自己不要写。

调用宏相当于将宏定义中的代码段先直接替换。随后再执行代码。

```cmake
macro(Test Var)
	set(Var "new value")
	message("var=${Var}")
endmacro()

set(Var "first value")
message("var=${Var}")
Test("value")
message("var=${Var}")
#var=first value
#var=
```

# 4. 搜索文件

---

https://www.bilibili.com/video/BV14s4y1g7Zj/?p=5&spm_id_from=pageDriver&vd_source=3b08e97e50222fa2ec22737f6dcb2202

```cmake
aux_source_directory(目录 变量)	#将目录下的源文件名存储在变量里
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/ SRC)	
```

```cmake
file(GLOB SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)	#查找当前CMakeLists文件目录的/src下的所有.cpp文件
```

# 5. 制作静态库/动态库及其使用

> **源文件很多，就生成动态库使用。**
>
> **源文件比较少，生成静态库使用。**打包在可执行文件中，发布的时候就一个可执行文件就可以，方便。缺点是生成的可执行文件比较大。

* 步骤：

  搜索源文件

  指定头文件目录

  指定库生成目录

  生成库

## （1）生成

```cmake
# 生成库
#生成时库名称会自动改为libXXX.a或libXXX.so
# ${SRC}是生成库文件所需的源文件
# 默认是STATIC
add_library(库名称 STATIC ${SRC})	#静态库
add_library(库名称 SHARED ${SRC})	#动态库。动态库可执行
```

![image-20240505133431804](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240505133431804.png)

## （2）指定库生成路径

宏`LIBRARY_OUTPUT_PATH`

```cmake
# 指定生成库文件路径
# 使用宏
set(LIBRARY_OUTPUT_PATH /home/xxx/lib)
```

## （3）使用库文件

> **先指定静态库，再生成可执行文件。**
>
> **.h头文件是编译时必须的，lib是链接时需要的，dll是运行时需要的**。
>
> 　　在`cmake`语法中，`link_libraries`和`target_link_libraries`是很重要的两个链接库的方式，虽然写法上很相似，但是功能上有很大区别：
>
> - `link_libraries`用来链接静态库，`target_link_libraries`用来链接导入库，即按照`头文件 + .lib(动态库导入库) + .dll(动态库)`方式隐式调用动态库的`.lib`导入库
> - `link_libraries`用在`add_executable`之前，`target_link_libraries`用在`add_executable`之后

**发布库时要发布库文件+头文件。**

头文件里有库文件里的函数的声明。

静态库会被打包到可执行文件app中，动态库不会被打包。

```cmake
link_directories(${CMAKE_CURRENT_SOURCE_DIR}/pathxxx1 ${CMAKE_CURRENT_SOURCE_DIR}/pathxxx2)	#指定库的路径

link_libraries(libxxx1.a libxxx2.a ...)	#链接静态库。这样add_executable时就会链接库文件

target_link_libraries(target PUBLIC/PRIVATE 动态库1 动态库2 ...)	#链接动态库，放在CMakeLists的最后
```

# 6. 生成可执行文件

> 根目录下的CMakeLists

* 步骤：

  搜索源文件

  ```cmake
  file(GLOB SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)	#搜索main.cpp
  ```

  指定头文件目录

  指定库文件目录

  链接静态库（link_libraries)

  指定可执行文件生成目录（bin)

  ```cmake
  set(EXECUTABLE_OUTPUT_PATH /hone/xxx/code)	#指定可执行文件输出路径
  ```

  链接动态库（target_link_libraries)

  生成可执行文件

# 7. 子目录CMakeLists

> 每个模块放在子目录里，每个模块子目录添加子CMakeLists.txt用于编译模块，生成库文件供其他库或main调用。
>
> **在根目录下的父CMakeLists.txt中以`add_subdirectory(子CMakeLists所在目录)	`包含子目录，从而关联子CMakeLists。**
>
> **把所有子模块的子目录都包含进去。**
>
> 子CMakeLists.txt可以拿到父CMakeLists.txt中定义的变量并使用，反过来不行。
>
> 子模块从`${PROJECT_SOURCE_DIR}/include`拿头文件
>
> 生成路径设定为`${PROJECT_SOURCE_DIR}/lib`

```cmake
add_subdirectory()
```

![image-20240505111906101](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240505111906101.png)

# 7. 自定义模块依赖自定义模块

> 自定义模块编译成库文件被链接，它所依赖的模块也要编译成库
>
> `link_libraries`放在add_library()或add_exectuable()之前
>
> `target_link_libraries`放在之后

* 模块A依赖模块B、C（分别编译成静态库：libxxx1.a，libxxx2.a...)，模块A最后编译成静态库

  ![](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240505123749874.png)

  ```cmake
  aux_source_directory(./ SRC)
  link_directories()	#添加库目录
  link_libraries(libxxx1.a libxxx2.a ...)	#链接静态库（指定名称，可以全称libxxx.a，也可以掐头去尾写xxx）
  add_library(xxx STATIC ${SRC})	# 生成静态库xxx
  ```

* 模块A依赖模块B、C（分别编译成静态库：libxxx1.a，libxxx2.a...)，模块A最后编译成动态库

  ```cmake
  link_directories()	#添加库目录
  link_libraries(libxxx1.a libxxx2.a ...)	#链接静态库（指定名称，可以全称libxxx.a，也可以掐头去尾写xxx）
  add_library(xxx SHARED 源文件)	# 生成动态库xxx
  ```

* 模块A依赖模块B，模块B、C（分别编译成动态库：libxxx1.so、ilbxxx2.so...），模块A最后编译成静态库

  ![image-20240505123841395](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240505123841395.png)

  ```cmake
  link_directories()	#添加库目录
  add_library(xxx STATIC 源文件)	# 生成动态库xxx
  target_link_libraries(xxx PRIVATE xxx1 xxx2)	#链接libxxx1.so、libxxx2.so
  ```

* 模块A依赖模块B，模块B、C（分别编译成动态库：libxxx1.so、ilbxxx2.so...），模块A最后编译成动态库

  ```cmake
  link_directories()	#添加库目录
  add_library(xxx SHARED 源文件)	# 生成动态库xxx
  target_link_libraries(xxx PRIVATE xxx1 xxx2)	#链接libxxx1.so、libxxx2.so
  ```

```cmake
# /src/module1
aux_source_directory(. SRC)
include_directories(${PROJECT_SOURCE_DIR}/include)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
link_directory(${PROJECT_SOURCE_DIR}/lib)	#所有module生成的库都放在${PROJECT_SOURCE_DIR}/lib
add_library(module2Lib STATIC ${SRC})
target_link_libraries(module2Lib PRIVATE module1Lib)
```

# 8.命令总结

```cmake
cmake_minimun_required(VERSION )	#设置cmake最低版本

project(App CXX)	#项目名、使用C++

file(GLOB SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)	#查找源文件

set(EXECUTABLE_OUTPUT_PATH /hone/xxx/code)	#指定可执行文件输出路径

add_executable(app main.cpp) #生成可执行文件

add_library(库名称 STATIC/SHARED 源文件1 源文件2 ...)	#生成库文件，默认是静态库

set(LIBRARY_OUTPUT_PATH /home/xxx/lib)	#指定库文件生成路径

include_directories(${PROJECT_SOURCE_DIR}/include)	#添加头文件路径。影响范围最大，一般放在最外层CmakeLists.txt影响全局。

target_include_directories (App PUBLIC "${PROJECT_BINARY_DIR}" "${PROJECT_SOURCE_DIR}/include")	#添加头文件路径。影响范围自定义，关键字PUBLIC/PRIVATE。

link_directories(${CMAKE_CURRENT_SOURCE_DIR}/pathxxx1 ${CMAKE_CURRENT_SOURCE_DIR}/pathxxx2)	#指定自己制作的库的路径（系统提供的库自己能找到）

link_libraries(libxxx1.a libxxx2.a ...)	#只能链接静态库。放在add_exectuable()或add_library()之前

# 动态库、静态库都能链接。
# 要放在add_exectuable()或add_library()后面
# target是需要链接动态库的目标（可以是源文件、动态库、可执行文件） 
target_link_libraries(target PUBLIC/PRIVATE 动态库1 动态库2 ...)	#链接动态库(默认PUBLIC，使用PUBLIC即可)

```

| A PUBLIC B  | 表示当前的依赖项B，可被A以及所有依赖A的获得             |
| ----------- | ------------------------------------------------------- |
| A PRIVATE B | 表示当前的依赖性B，只能被A获得，不可被依赖于A的其他获得 |

* 包含头文件`include_directories`和`target_include_directories`的选择

  `target_include_directories()`的功能完全可以使用 `include_directories()`实现。但是我还是建议使用 `target_include_directories()。`

  **`include_directories(header-dir)` 是一个全局包含，向下传递。什么意思呢？就是说如果某个目录的 CMakeLists.txt 中使用了该指令，其下所有的子目录默认也包含了`header-dir` 目录。**

  上述例子中，如果在顶层的 cmake-test/CMakeLists.txt 中加入：

  ```cmake
  include_directories(hello-world)
  include_directories(hello-world/hello)
  include_directories(hello-world/world)
  ```

  那么整个工程的源文件在编译时**都**会增加：

  ```bash
  -I hello-world -I hello-world/hello -I hello-world/world ...
  ```

  各级子目录中无需使用 `target_include_directories()` 或者 `include_directories()`了。如果此时查看详细的编译过程（`make VERBOSE=1`）就会发现编译过程是一大坨，很不舒服。

  当然了，在**最终子目录**的 CMakeLists.txt 文件中，使用 `include_directories()` 和 `target_include_directories()` 的效果是相同的。

# 9. 通过FetchContent管理第三方库

---

> cmake 3.11版本及以上可使用
>
> 好用的包管理器
>
> **cmake脚本运行编译项目阶段**从外部下载依赖库源码，而不需事先下载到本地或作为项目的一部分提交到版本控制。
>
> 项目根目录下建`demo/ext`目录存放第三方库
>
> 注意：提示找不到FetchContent时，可能是构建文件夹build内有之前cmake的缓存。如果之前构建过项目，可能会有一些旧的CMake缓存文件影响构建。可以尝试删除CMake缓存（通常位于构建目录中的`CMakeCache.txt`文件）并重新运行CMake。

## （1）工作原理

* 声明依赖

  通过`FetchContent_Declare()`函数声明外部项目，指定项目位置（如Git仓库）、标签、分支、提交等

* 下载依赖

  通过`FetchContent_MakeAvailable()`下载声明的依赖到项目的子目录中

* 使用依赖

  依赖下载完成后，外部项目的源码就可以像项目内的代码一样被使用

## （2）使用流程

* 引入FetchContent模块

  ```cmake
  # CMakeLists.txt头部写这行命令
  include(FetchContent)
  ```

* 声明外部依赖的名称、位置等信息

  ```cmake
  FetchContent_Declare(
  	外部库名称	#库名字
  	GIT_REPOSITORY https://urlofProjectOnGithub		# 仓库地址
  	GIT_TAG 2.6.x	# 库版本（tag)
  	SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/ext/库名称 # 指定库下载到当前目录下新建ext的【库名称文件夹】下
  )
  ```

* 让第三方库可用

  ```cmake
  FetchContent_MakeAvaliable(外部库名称)
  ```

* 链接库中的某个模块到项目中

  ```cmake
  add_excutable(App main.cpp)
  target_link_libraries(App PRIVATE 外部库::子模块)
  ```

## （3）项目demo

```bash
tree demo	#查看目录树形结构
```

* 创建项目下各目录

  ```bash
  demo
  ├───build		# cmake的输出文件
  ├───cmake		# cmake文件夹，存放 .cmake文件
  ├───ext		# spdlog等第三方库的存放目录
  ├───include		# 头文件路径
  └───src
          main.cc      # 主项目源文件
  └───CMakeLists.txt # top directory下的cmake配置文件
  ```

* 在cmake目录下，创建负责第三方库的cmake脚本文件：`外部库名称.cmake`

  可以不写.cmake脚本，直接把脚本内容放到CMakeLists.txt文件。

  ```cmake
  include(FetchContent)
  FetchContent_Declare(
  	外部库名称	#库名字
  	GIT_REPOSITORY https://urlofProjectOnGithub		# 仓库地址
  	GIT_TAG 2.6.x	# 库版本（tag)
  	SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/ext/库名称 # 指定库下载到当前目录下新建ext的【库名称文件夹】下
  )
  FetchContent_MakeAvailable(库名称)
  ```

  ```bash
  demo
  │   
  ├───build
  ├───cmake
  │       外部库名称.cmake	# 新增的文件
  │       
  ├───ext
  ├───include
  └───src
          main.cc
  └───CMakeLists.txt
  ```

* 在最上层目录的`demo/CMakeLists.txt`中引入`外部库名称.cmake`脚本

  把下面的代码加在`CMakeLists.txt`后面

  ```cmake
  # -----------------------------使用外部库1------------------------------------------------------
  set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake;${CMAKE_MODULE_PATH}")
  
  # 引入spdlog.cmake, 
  include(spdlog)	# 只填写文件名。
  
  # 项目中使用spdlog
  target_link_libraries(App PRIVATE spdlog::spdlog)
  # ----------------------------------------------------------------------------------------
  ```

* 下载子模块源码

  ```bash
  cmake --B build
  cmake --build build
  ```

# 9. demo

---

https://blog.csdn.net/weixin_45448662/article/details/132654732

* 常用宏：

  ```cmake
  ${CMAKE_CURRENT_SOURCE_DIR}	
  
  ${PROJECT_SOURCE_DIR}
  ```

* 查看项目结构

  ```cmake
  tree -L 1	#查看第一级目录
  tree -L 2	#查看第二级目录
  tree src	#查看src目录结构
  ```

## （1）项目结构1

> bin
>
> build
>
> cmake
>
> include
>
> > module1.h
>
> lib
>
> src
>
> > common
> >
> > module1
> >
> > > module1.cpp
> > >
> > > CMakeLists.txt
> >
> > module2
> >
> > > module2.cpp
> > >
> > > CMakeLists.txt
> >
> > ...
>
> ext
>
> > 三方库1
> >
> > 三方库2
> >
> > ...
>
> main.cpp
>
> CMakeLists.txt

| -目录          | -说明                                               |
| -------------- | --------------------------------------------------- |
| bin            | 可执行文件生成目录                                  |
| build          | cmake编译目录                                       |
| cmake          | 存放.cmake脚本，通过include(xxx.cmake)引入          |
| include        | module1、module2等的头文件，以及lib中其他库的头文件 |
| lib            | 库文件                                              |
| src            | 源码                                                |
| src/common     | 存放所有module都要使用的一些公共的类                |
| /src/module1   | 模块1源码                                           |
| /src/module2   | 模块2源码                                           |
| ext            | 通过FetchContent管理的三方库目录                    |
| CMakeLists.txt | 顶级CMakeLists.txt                                  |

## （2）项目结构2

与结构1的区别：

每个module的头文件不集中起来放在`${PROJECT_SOURCE_DIR}/include`下，而是直接放在module子目录下。

这样根目录的CMakeLists用`include_directories()`包含头文件时，需要把所有module子目录都包含。

通过`add_subdirectory()`包含所有module子目录使用子CMakeLists。

![image-20240505124144144](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240505124144144.png)

## （3）示例

demo01使用结构1

> bin
>
> include
>
> > cat.h
>>>
> > dog.h
>
> > bird.h
>
> lib
> 
> > libcat.so
>>
> src
>
> > animal
> >
> > > dog.cpp
>> >
> > > bird.cpp
>> >
> > > CMakeLists.txt
> 
> ext
>
> > spdlog
>
> CMakeLists.txt
>
> main.cpp

```cpp
//cat.h
# pragma once
#include<string>

class cat
{
    public:
        std::string bark();
};
```

```cpp
//cat.cpp
#include "cat.h"

std::string cat::bark()
{
    return "meow";
}
```

```cpp
//dog.h
//dog.cpp
//bird.h
//bird.cpp
//代码相同
```

```cpp
//main.cpp
#include<iostream>
#include "dog.h"
#include "cat.h"
#include "bird.h"
#include <spdlog/spdlog.h>	//外部库::子模块的头文件

int main(int argc, char** argv)
{
    cat c;
    std::cout<<c.bark()<<std::endl;  
    dog d;
    std::cout<<d.bark()<<std::endl;
    bird b;
    std::cout<<b.bark()<<std::endl;
    
    spdlog::info("i love c++");	//三方库spdlog，调用info()函数
    return 0;
}
```

```cmake
# cmake/spdlog.cmake
include(FetchContent)
FetchContent_Declare(	
						spdlog	#库名字
						GIT_REPOSITORY https://github.com/gabime/spdlog.git	# 仓库地址
						GIT_TAG v1.x # 库版本
						SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/ext/spdlog # 指定库下载地址
						)
FetchContent_MakeAvailable(spdlog)
```

```cmake
#animal/CMakeLists.txt
cmake_minimum_required(VERSION 3.28.3)
project(App CXX)
set(CMAKE_CXX_STANDARD 11)

aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/ SRC)	
#file(GLOB SRC ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)	

include_directories(${PROJECT_SOURCE_DIR}/include)	#指定头文件
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)	#指定库生成目录
# 模块animal没有依赖其他模块
add_library(Animal STATIC ${SRC})	
```

```cmake
#demo01/CMakeLists.txt
cmake_minimum_required(VERSION 3.28.3)
project(App CXX)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

set(SRC ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp)	# 搜索源文件main.cpp

target_include_directories(App PUBLIC ${PROJECT_SOURCE_DIR}/include) # 指定头文件路径
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)	#指定可执行文件生成目录
add_subdirectory(${PROJECT_SOURCE_DIR}/animal)	#添加子目录/animal

link_directories(${PROJECT_SOURCE_DIR}/lib)	#指定库目录
link_libraries(Animal)			#链接静态库Animal
add_executable(App ${SRC})		#生成可执行文件

#target_link_libraries(App PRIVATE cat)		#链接动态库libcat.so

# ------------------------------------------------------------------------------------------------
include(FetchContent)	#使用FetchContent
# -----------------------------使用外部库spdlog------------------------------------------------------

FetchContent_Declare(spdlog
        GIT_REPOSITORY https://github.com/gabime/spdlog.git
        GIT_TAG v1.4.1)
FetchContent_MakeAvailable(spdlog)
target_link_libraries(App PRIVATE spdlog::spdlog)
# ----------------------------------------------------------------------------------------
# ---------------------------使用外部库json----------------------------------------------------

FetchContent_Declare(json
        GIT_REPOSITORY https://gitee.com/slamist/json.git
        GIT_TAG v3.7.3)
FetchContent_MakeAvailable(json)
target_link_libraries(mjson PRIVATE nlohmann_json::nlohmann_json)
# --------------------------------------------------------------------------------------------

```

```bash
# 在根目录demo01/下，执行脚本
cmake -B build	
cmake --build build
# 或是这么些
mkdir build
cd build 
cmake ..
make -j4
```

# 10. Cmake条件编译

---

通过对宏赋不同的值，编译不同的文件。

使用`-D`参数指定宏的不同值：

```bash
`cmake -B build -D USE_CAT=ON`
```

project

> animal
>
> > dog.h
> >
> > dog.cpp
> >
> > cat.h
> >
> > cat.cpp
> >
> > cat2.h
> >
> > cat2.cpp
> >
> > CMakeLists.txt（子CMakeLists.txt）
>
> CMakeLists.txt（父CMakeLists.txt）
>
> main.cpp

```cmake
#子CMakeLists.txt
option(USE_CAT2 "compile cat" OFF)	#宏USE_CAT默认值设为OFF

if(USE_CAT2)
	set(SRC cat1.cpp)
else()
	set(SRC cat2.cpp)
endif()

add_library(Animal dog.cpp ${SRC})	#生成静态库

#若使用USE_CAT2，则向AnimalLib相关代码中声明要使用宏USE_CAT2
if(USE_CAT2)
	target_compile_definitions(AnimalLib PRIVATE "USE_CAT2")
endif()
```

* `target_compile_definitions()` 函数用于为指定的 CMake 目标添加编译定义。编译定义是传递给编译器的宏定义，它们可以影响代码的编译方式。

```c++
//cat.h
#include "cat.h"
#ifdef USE_CAT2
    #include "cat2.h"
#endif

std:string barking()
{
    #ifdef USE_CAT2
    	return cat2::barking();	//cat2 bark..
    #else
    	return "cat bark.."
    #endif
}
```

```bash
cmake -B build	#默认USE_CAT2=OFF
# cat bark..

rm -rf build

cmake -B build -DUSE_CAT2=ON
#cat2 bark..
```

