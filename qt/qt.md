https://blog.csdn.net/zhizhengguan/article/details/107567449

# 软件下载与安装

## 在线版

* 在线安装包网址：https://download.qt.io/official_releases/online_installers/#:~:text=qt-unified-windows-x86-online.exe

* [在线版本安装流程：先注册账号。安装包不分商业版、开源版，根据你的账号申请了哪个版本自动下载。若下载的是商业版，则重新注册一个账号申请开源版然后登录。](https://blog.csdn.net/weixin_45191386/article/details/128115635?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169112289516800185882901%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169112289516800185882901&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-2-128115635-null-null.142%5Ev92%5Ekoosearch_v1&utm_term=QT%E5%AE%89%E8%A3%85&spm=1018.2226.3001.4187)

* 在线版环境配置过程：https://blog.csdn.net/weixin_45112748/article/details/132101874

## 离线版

Qt5.12离线版下载网址: http://download.qt.io/archive/qt/5.12/5.12.2/

## QtCreator配置Kit

https://blog.csdn.net/LUCYcanFire/article/details/126402240

**依次配置：**

编译器：如果你安装的是mingw版本的qt，那就请选择mingw中的g++。如果是msvc版本的qt，那就请选择vs下的msvc。mingw版本和msvc版本的qt是不互通的，所以要选择对应版本编译器。

Debuggers：与编译器一致即可，g++则用GDB，msvc则找到CDB。

Cmake：与上面的方法一致，找到Cmake编译器，添加就行了。

配置完之后可以在Kit标签页给配置命名。