# 1. Makefile

指定编译、链接规则的脚本。

## 1）编译指令

```bash
g++ -E aaa.cc -o aaa.i	#预编译
g++ -S aaa.i -o aaa.s	#编译
g++ -c aaa.o -o aaa.s	#汇编
g++ aaa.o -o aaa		#链接
```

## 2）小项目Makefile

标签下不仅仅可以是编译指令，也可以是bash指令。

make指令将会运行 all标签下指定的编译。

aaa1:aaa1.cc表示aaa1依赖aaa1.cc。

make clean指令将执行clean标签下的命令。

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

## 3）使用变量

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

## 4）单目标文件模板

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

## 5)多目标模板

依赖关系的表示却十分简单，即使有几百个依赖关系也同样适用，所以这种写法对大型项目的开发是非常有好处的。

```bash
target1=server        //服务器运行目标文件server
target2=client        //客户端目标文件client
src=$(wildcard *.c)   //用wildcard函数找到所有.c文件，server.c、client.c、wrap.c
deps=$(wildcard *.h)   //用wildcard函数找到所有的.h文件，wrap.h
obj=$(patsubst %.c,%.o,$(src))    //用patsubst函数将所有.c文件替换成.o文件
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



# 2. Cmake

