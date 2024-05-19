 可视化调试：

gdb -tui a.out

b a.cpp:linenum





1.编译

g++ -g test.cpp -o test.out

g为了避免No Debugging Symbols found，生成有Debugging Symbols的可执行文件

 

2.  打断点

b main

 

b path/test.cpp

 

\3.  开始debugging

r

 

\4.  按过程调试

n （next）

 

\5.  按语句调试，进入函数

s（step into）

 

\6.  info b——显示所有断点

 

7.删除断点——d [num]

 

\8.  到达某断点后继续执行到下个断点

c

 

9.bt

 

\10.  watch——监视某变量

watch i

 

\11.  info r——所有寄存器的值

 

\12. 查看变量值

p——按照变量定义的类型打印

/x——用16进制显示变量

/t——二进制显示

如： p/x i

​                               

 

\13. layout src——可视化代码调试

 

Ctrl+x，a关掉界面

layout asm——汇编窗口

layout splict——同时查看源码、汇编

layout regs——显示寄存器窗口



si——汇编界面的单步

 

\14. 调试带有参数argv的程序

方法1： gdb启动时候加参数

 

gdb --args ./main aaaa bb

 

方法2：

 

gdb main  //先启动起来

 

(gdb)run aaaa bb

 

方法3

 

gdb main  //先启动起来

 

(gdb)set args aaaa bb

 

(gdb)run //或者start

 

(gdb)show args

 

\15. 查看源码

list ：简记为 l ，其作用就是列出程序的源代码，默认每次显示10行。

list 行号：将显示当前文件以“行号”为中心的前后10行代码，如：list 12

list 函数名：将显示“函数名”所在函数的源代码，如：list main

list ：不带参数，将接着上一次 list 命令的，输出下边的内容。

 