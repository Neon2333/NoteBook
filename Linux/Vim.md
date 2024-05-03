# 0. 常用

---

```bash
#配置vim
vi /etc/vim/vimrc
# 在末尾加上
set number
set autoindent
set cindent
set shiftwidth=4
set tabstop=4
set mouse=a
```

```bash
:set number 显示行号
:n 光标跳到第n行
G 跳到最后一行
gg 跳到第一行行首 
$ 移动到行末
0 移动到行首
i
a
shift+i 在行首插入
shift+a 在行末插入
o 在下方插入空白行
O 在上面插入空白行
w 下一个词首
b 上一个词首
de 删除一个词
ce 删除一个词并插入
D 删除本行中光标后的全部内容
yy 复制当前行
dd 删除当前行
ndd 删除n行
P 在光标前黏贴
p 在光标后黏贴（光标下一行黏贴）
u 撤销
ctrl-r 恢复
ctrl-u 上翻半页
ctrl-d 下翻半页
ctrl-insert 复制
ctrl-shift 黏贴
:a,bmc	a-b行代码剪切到m行下一行
```

### 复制词A替换词B

```bash
光标移动到aaa的开头，按 v 按e 按y

光标移动到aaa的开头，按 v 按e 按y

也就说，快速选中一个单词，按v按e即可
```

### 移动整行

```bash
dd 剪切行（将tab缩进也cut了）
移动光标
p在下一行黏贴
i 进入insert mode
tab/backspace 修改缩进
```

### 复制黏贴一部分

```bash
移动光标
y$ 复制当前行光标后的内容
p 粘贴到光标后
```

### 复制/移动多行

```bash
:a,bmc 将a~b行代码块剪切到c行的下一行
:a,btc 将a~b行代码块复制到c行的下一行
```

# 1. 模式

---

## （1）Normal模式

默认/按ESC

i——进入Insert模式，在当前光标所在字符左侧插入

a——进入Insert模式，在当前光标所在字符右侧插入

u——撤销

Ctrl-r——恢复

## （2）Visual可视模式

visual——Normal模式下按v，自由选中。按e选中到词尾

visual line——V，行选中

visual block——Ctrl-v，矩形块选中

通过jkhl选中代码行/块后，y为copy，p为paste

缩进——shift+>或<

## （3）命令行模式

Normal模式下按:

set number——显示行号

q——退出

w——保存

!——强制

wq——退出保存

wq!——强制退出保存

## （4）插入模式

i——光标前插入

a——光标后插入

# 2. 光标移动

Normal模式下：

j——光标向下移动

k——光标向上移动

h——光标向左移动

l——光标向右移动

Ctrl-d——向下翻页

Ctrl-u——向上翻页

num+j——光标往下跳转num行

num+k——光标向上跳转num行

w——下一个word头部

b——上一个word头部

e——下一个word的尾部

- word间——w/b跳转
- word内部——b/e跳转

0（数字零）——跳转到行首

$——跳转到行末

I——行首输入

A——行末输入

o——在光标所在行下方插入空白行

O——在光标所在行上方插入空白行

gg——跳转到文件首行

G——跳转到文件最后一行

:num——光标跳转到num行

Ctrl-u(up)——向上翻半页

Ctrl-d(down)——向下翻半页

Ctrl-f(forward)——向下翻一页

Ctrl-b(back)——向上翻一页

# 3. 拷贝与删除

Normal模式下：

yy——复制光标所在整行

y$——复制光标到行尾

dd——剪切/删除光标所在行

ndd——剪切/删除光标

D——剪切光标到行尾，删除光标到行末

num+yy——拷贝包括当前行在内的向下num行

num+dd——剪切包括当前行在内的向下num行

p——将复制/剪切内容黏贴到光标所在行的下一行（整行）；黏贴到光标右侧（非整行）

P——黏贴到光标上一行（整行），黏贴到光标左侧（非整行）

dw/de——剪切、删除word在光标之后的内容（dw删除word后的空格，de不删除word后的空格）

ce——删除光标所在word，同时进入Insert模式进行编辑

num+dw——删除包括光标在内的num个word

num+ce——删除包括光标在内的num个word，同时进入Insert模式进行编辑

:50,100t20——将50~100行的代码拷贝copy到20行的下一行

:50,100m20——将50~100行的代码移动move到20行的下一行

# 4. 多窗口

## 多文件

### :qa——关闭所有窗口

### 关闭当前窗口——:q

### 切换窗口——Ctrl-ww（Ctrl-w+jk/hl）

### vim -o file1 file2——上下分屏

### :sp file2

![image-20211006213317177](https://i.loli.net/2021/10/06/iOhPln2648SqvkF.png)



### vim -O file1 file2——左右分屏

### :vsp file2

![image-20211006213518751](https://i.loli.net/2021/10/06/RNl6gB4CyYV7uHp.png)



## 多窗口一个文件

### 多窗口打开的是同一文件，在任一窗口所做修改都会同步，主要用于查看/修改某文件的不同位置

### 上下分屏——:sp

### 左右分屏——:vsp

