# 1. Qt安装

---

https://blog.csdn.net/zhizhengguan/article/details/107567449

## 在线版

* 在线安装包网址：https://download.qt.io/official_releases/online_installers/#:~:text=qt-unified-windows-x86-online.exe

* [在线版本安装流程：先注册账号。安装包不分商业版、开源版，根据你的账号申请了哪个版本自动下载。若下载的是商业版，则重新注册一个账号申请开源版然后登录。](https://blog.csdn.net/weixin_45191386/article/details/128115635?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169112289516800185882901%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169112289516800185882901&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-2-128115635-null-null.142%5Ev92%5Ekoosearch_v1&utm_term=QT%E5%AE%89%E8%A3%85&spm=1018.2226.3001.4187)

* 在线版环境配置过程：https://blog.csdn.net/weixin_45112748/article/details/132101874

## 离线版

Qt5.12离线版下载网址: http://download.qt.io/archive/qt/5.12/5.12.2/

## QtCreator配置Kit

https://blog.csdn.net/LUCYcanFire/article/details/126402240

**依次配置：**

* 编译器：

  如果你安装的是mingw版本的qt，那就请选择mingw中的g++。如果是msvc版本的qt，那就请选择vs下的msvc。mingw版本和msvc版本的qt是不互通的，所以要选择对应版本编译器。

* Debuggers：

  与编译器一致即可，g++则用GDB，msvc则找到CDB。

* Cmake：

  与上面的方法一致，找到Cmake编译器，添加就行了。

配置完之后可以在Kit标签页给配置命名。

# 2.  字符串

---

## QByteArray

## QString

QString是对QByteArray进一步封装。后者只是简单封装char*

QString不能直接转成char*，需要先转成QByteArray：

```cpp
QByteArray QString::toUtf8() const
```

字符串拼接：

```cpp
QString str = QString("(%1)....(%2)").arg("hhh").arg("zzz")	//hhh....zzz
qDebug()<<std;
```

QString的length()、size()、count()计算是字符的个数（包括汉字），内部编码是utf8

QByteArray计算出的是字节数。



# 3.QVariant

---

> QVariant这个类很神奇，或者说方便。很多时候，需要几种不同的数据类型需要传递，如果用结构体，又不大方便，容器保存的也只是一种数据类型，而QVariant则可以统统搞定。QVariant 这个类型充当着最常见的数据类型的联合。QVariant 可以保存很多Qt的数据类型，包括QBrush、QColor、QCursor、QDateTime、QFont、QKeySequence、 QPalette、QPen、QPixmap、QPoint、QRect、QRegion、QSize和QString，并且还有C++基本类型，如 int、float等。

## （1）标准类型
将标准类型转换为QVariant类型

```cpp
// 这类转换需要使用QVariant类的构造函数, 由于比较多, 大家可自行查阅Qt帮助文档, 在这里简单写几个
QVariant::QVariant(int val);
QVariant::QVariant(bool val);
QVariant::QVariant(double val);
QVariant::QVariant(const char *val);
QVariant::QVariant(const QByteArray &val);
QVariant::QVariant(const QString &val);
......
    
// 使用设置函数也可以将支持的类型的数据设置到QVariant对象中
// 这里的 T 类型, 就是QVariant支持的类型
void QVariant::setValue(const T &value);
// 该函数行为和 setValue() 函数完全相同
[static] QVariant QVariant::fromValue(const T &value);
// 例子:
#if 1
QVariant v;
v.setValue(5);
#else
QVariant v = QVariant::fromValue(5);
#endif

int i = v.toInt();          // i is now 5
QString s = v.toString();   // s is now "5"
// 该函数的返回值是一个枚举类型, 可通过这个枚举判断出实际是什么类型的数据
Type QVariant::type() const;
返回值Type的部分枚举定义, 全部信息可以自行查阅Qt帮助文档
```

### 1）判断 QVariant中封装的实际数据类型

```cpp
// 该函数的返回值是一个枚举类型, 可通过这个枚举判断出实际是什么类型的数据
Type QVariant::type() const;
```

返回值Type的部分枚举定义：

### 2）将QVariant对象转换为实际的数据类型

```cpp
// 如果要实现该操作, 可以使用QVariant类提供的 toxxx() 方法, 全部转换可以参考Qt帮助文档
// 在此举列举几个常用函数:
bool QVariant::toBool() const;
QByteArray QVariant::toByteArray() const;
double QVariant::toDouble(bool *ok = Q_NULLPTR) const;
float QVariant::toFloat(bool *ok = Q_NULLPTR) const;
int QVariant::toInt(bool *ok = Q_NULLPTR) const;
QString QVariant::toString() const;
......
```

## （2）自定义类型

```cpp
// 如果当前QVariant对象可用转换为对应的模板类型 T, 返回true, 否则返回false
bool QVariant::canConvert() const;
// 将当前QVariant对象转换为实际的 T 类型
T QVariant::value() const;
```

>除了标准类型, 我们自定义的类型也可以使用QVariant类进行封装, 被QVariant存储的数据类型需要有一个默认的构造函数和一个拷贝构造函数。为了实现这个功能，首先必须使用Q_DECLARE_METATYPE()宏。通常会将这个宏放在类的声明所在头文件的下面， 原型为：
>
>```cpp
>Q_DECLARE_METATYPE(Type)
>```
>
>

使用的具体步骤如下:

* 注册自定义类型

  ```cpp
  // *.h
  struct MyTest
  {
      int id;
      QString name;
  };
  // 自定义类型注册
  Q_DECLARE_METATYPE(MyTest)
  ```

* 封装自定义类型变量

  ```cpp
  MyTest t;
  t.name = "张三丰";
  t.num = 666;
  // 值的封装
  QVariant vt = QVariant::fromValue(t);
  
  // 值的读取
  if(vt.canConvert<MyTest>())
  {
      MyTest t = vt.value<MyTest>();
      qDebug() << "name: " << t.name << ", num: " << t.num;
  }
  ```

# 4. 日期时间

---

## （1）QDate

## （2）QTime

## （3）QDateTime

# 5. 图形类

---

## （1）QPoint

## （2）QLine

## （3）QSize

## （4）QRect

# 6. 定时器QTimer

---

## （1）常用API

```cpp
//构造
QTimer(QObject* parent);
//启动
start()
start(int msec)	//毫秒
//停止
stop()
//设定间隔
setInterval(int msec)
//信号
void timeout();
//设置定时器精度
void setTimerType(Qt::TimeType atype)
//判定是否正在运行
bool isActive() 
//设定只触发一次
void setSingleShot(bool singleShot)	//true为只触发一次
bool isSingleShot()
```

* 精度

  | -                   | -                             |
  | ------------------- | ----------------------------- |
  | Qt::preciseTimer    | 毫秒级                        |
  | Qt::CoarseTimer     | 1ms精度误差5%范围内，默认精度 |
  | Qt::VeryCoarseTimer | 精度1s，粗糙                  |

  

# 7. 事件处理机制-信号槽

---

> 信号：
>
> 槽函数：
>
> connect()：属于QObject类的函数，将槽函数注册到信号上：
>
> ```cpp
> connect(const Object sender, &QObject::signal,const Object recv, &QObject::method)
> ```
>
> 

## （1）内置信号、槽函数

查询某个类找不到信号时，可以去父类中找一下。

```cpp
this->setWindowTitle("mainwindow");
    QPushButton* pushbutton_01 = new QPushButton(this);
    pushbutton_01->setGeometry(30,30,100,100);
    pushbutton_01->setText("close");
    connect(pushbutton_01, &QPushButton::clicked, this, [=](){this->close();});
```

## （2）自定义信号

自定义信号、槽，都不能修改官方的类。所以继承官方的类，然后扩展，编写自己的信号、槽函数。

**一般继承QObject类。**

要在自定义类继承时加入`Q_OBJECT`宏：

```cpp
class MyMainWindow:MainWindow
{
    Q_OBJECT
    ...
}
```

* 类的成员函数

  返回值void

  只要声明不要实现

  用`signal`声明

  可重载

  ```cpp
  class A
  {
  signals:
      void test();
      void test(QString str);
  }
  ```

* 发送自定义信号

  因为是自定义的信号，系统不会自己发送。需要手动发送。

  加不加`emit`都行，只是便于阅读。

* **传入信号的参数，会被传递到槽函数中。**

  所以槽函数的参数列表要完全保持一致。（信号传递的参数个数只能>=槽函数参数个数）

## （3）自定义槽函数

* 用`slots`声明

  ```cpp
  class B
  {
  public slots:
      QString getStr(QString str)
      {
          return str;
      }
  }
  ```

* lambda表达式作为槽函数

  不捕获外部变量时看成仿函数。函数地址、函数指针、函数地址，仿函数用起来差不多。

  **[=]按值捕获，变量副本在lambda函数体内是只读，不能修改，想要修改变量副本要加`mutable`。但也不影响原来的变量值，改变的是副本。**

  所谓外部变量就是lambda函数体外部的变量。

  ```cpp
  //定义
  []()->void{
      QDebug()<<"hello";
  }
  //调用
  [](){
      QDebug()<<"hello";	//->可省略
  }();
  //捕获外部变量
  [=]按值捕获		
  [&]按引用捕获	
  [aaa]		只捕获aaa（按值），其他变量不捕获
  [&aaa]		只捕获aaa（按引用），其他变量不捕获
  ```

  ```cpp
  [=](int m, QString str)mutable{
      a++;		//a=a+1，发生了写操作
      QDebug<<str;
  }(1, "hello");
  ```

  

## （4）关联多个槽函数

一个信号可以关联多个槽函数，connect多次，槽函数执行顺序随机，和注册顺序无关。

## （5）信号触发信号

就是收到信号不进行函数处理，而发送另一个信号：

```cpp
A* a = new A();
connect(this->pushbutton, &QPushButton::click, a, &A::test);
//pushbutton的信号没有被handle，而是触发了A的test信号
```

## （6）信号和槽的关联可断开

```cpp
disconnect(sender, signal, receiver, slot);
```

