当静态库改变时，需要将生成的新的静态库和目标程序.out文件重新链接，比较繁琐。但使用静态库的目标程序中已有所有需要的函数，所以可移植性较高。

使用动态库时，当动态库中发生了修改，只需要重新编译动态库文件，而无需将新生成的动态库和目标程序.out文件重新链接。开发的时候很方便。

## 1. 生成汇编文件

```shell
g++ -fPIC -c -o test.o test.c 	//-c生成汇编文件
```

![image-20211017183901353](https://i.loli.net/2021/10/17/xZrU6kFAS9IopRJ.png)

## 2. 生成动态库（共享库）

```shell
g++ -shared -o libtest.so test.o 
```

> -shared——表示生成共享库
>
> **libtest.so——共享库文件名是libtest.so，共享库名是test**

## 3. 链接动态库

```shell
g++ -o main.out main.cpp -L./ -ltest
```

***若直接按照该代码执行，将无法链接动态库。与静态库加入到.out文件不同，动态库需要被加载器加载到内存中。而加载器默认只会去/lib或/usr/lib等去搜索动态库。***

![image-20211017210219813](https://i.loli.net/2021/10/17/mc6AF3fiSQdTCZU.png)

![image-20211017210548617](https://i.loli.net/2021/10/17/EvfFQt5XdDUPh8r.png)

库的搜索路径十分重要

```shell
export LD_LIBRARY_PATH="./"
```

![image-20211017210728387](https://i.loli.net/2021/10/17/vCQB6EID3wLaFUj.png)



## 4. 判断程序有没有链接动态库

### （1）file——查看文件类型

```shell
file a.out 
```

![image-20211017210941858](https://i.loli.net/2021/10/17/3k9GutA1sfoUcLZ.png)

![image-20211017211109129](https://i.loli.net/2021/10/17/6GXuY4LZhlaBIqP.png)

### （2）ldd——查看.out文件链接了哪些动态库

```shell
ldd a.out
```

![image-20211017211242186](https://i.loli.net/2021/10/17/kuMRs8dStAX5J1B.png)

> libc.so.6——C函数库
>
> ld_linux-x86-64.so.2——加载器



## 5. g++默认先链接动态库

当同一目录下有**同名**的动态库和静态库，那么编译时会链接哪个库呢？

***g++默认先链接动态库，若需要链接静态库，需要指定-static选项。***

```shell
g++ -o main.out main.cpp -L./ -static -lxxxx
```



## 6. 静态库、动态库混合链接

![image-20211017212023978](https://i.loli.net/2021/10/17/Pi3GA6JflWRtjFn.png)

```shell
-Wl,-Bstatic指定后边跟着的库都是静态方式链接
-Wl,-Bdynamic指定后边跟着的库都是动态方式链接
```

