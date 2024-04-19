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

## 使用Vim

Preference->FakeVim

## 条件编译

```cpp
#define INT

#ifdef INT
//这里些int代码
#endif

ifdef DOUBLE
//这里写double代码    
#endif
```

# 2.  字符串

---

## QByteArray

## QString

QString是对QByteArray进一步封装。后者只是简单封装char*

QString不能直接转成char*，需要先转成QByteArray：

```cpp
QByteArray QString::toUtf8() const
```

## 字符串拼接

**注意：arg在QString()的后面。不是在字符串的后面**

```cpp
QString str = QString("(%1)....(%2)").arg("hhh").arg("zzz")	//hhh....zzz
qDebug()<<std;
```

QString的length()、size()、count()计算是字符的个数（包括汉字），内部编码是utf8

QByteArray计算出的是字节数。

```cpp
//add a qlabel
    QLabel* label_01 = new QLabel(this);
    label_01->setGeometry(400,400, 200,50);
    //add a button
    QPushButton* pushbutton_timer = new QPushButton(this);
    pushbutton_timer->setText("change pos");
    pushbutton_timer->setGeometry(300,300,100,100);
    //add a timer
    QTimer timer_01(this);
    timer_01.setInterval(1000);
    //click button cause position change of label and display it
    connect(pushbutton_timer, &QPushButton::clicked,pushbutton_timer,[=](){
        label_01->move(rand()%400, rand()%400);
        QString content = QString("pos:(%1),(%2)").arg(label_01->x()).arg(label_01->y());
        label_01->setText(content);
    });
```

# 3.QVariant

将内置类型封装，然后统一处理。

---

> QVariant这个类很神奇，或者说方便。很多时候，需要几种不同的数据类型需要传递，如果用结构体，又不大方便，容器保存的也只是一种数据类型，而QVariant则可以统统搞定。QVariant 这个类型充当着最常见的数据类型的联合。QVariant 可以保存很多Qt的数据类型，包括QBrush、QColor、QCursor、QDateTime、QFont、QKeySequence、 QPalette、QPen、QPixmap、QPoint、QRect、QRegion、QSize和QString，并且还有C++基本类型，如 int、float等。

## （1）封装标准类型
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

返回值Type是个枚举，查看文档。

### 2）将QVariant对象转换其他数据类型

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

## （2）封装自定义类型

```cpp
// 如果当前QVariant对象可用转换为对应的模板类型 T, 返回true, 否则返回false
bool QVariant::canConvert() const;
// 将当前QVariant对象转换为实际的 T 类型
T QVariant::value() const;
```

>除了标准类型, 我们自定义的类型也可以使用QVariant类进行封装, 被QVariant存储的数据类型需要有一个默认的构造函数和一个拷贝构造函数。**为了实现这个功能，首先必须使用Q_DECLARE_METATYPE()宏。通常会将这个宏放在类的声明所在头文件的下面**， 原型为：
>
>```cpp
>Q_DECLARE_METATYPE(Type)
>```
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
//信号名称timeout
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
> connect(const Object sender, &QObject::signal,const Object receiver, &QObject::method)
> ```
>
> 

## （1）内置信号、槽函数

查询文档，某个类找不到信号时，可以去父类中找一下。

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

**要在自定义类继承时加入`Q_OBJECT`宏**：

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

  不捕获外部变量时，看成仿函数。函数地址、函数指针、函数地址，仿函数用起来差不多。

  捕获外部变量时，用bind先绑定然后再用function包装：

  ```cpp
  int a=1;
  int b=2;
  auto f = [=](){
      return a+b;
  }
  //ff可用作函数指针
  auto ff = function<int()>(bind(f));	//bind返回仿函数
  ```
  
  
  
  **[=]按值捕获，得到的是只读拷贝，不能修改，想要修改变量副本要加`mutable`。但也不影响原来的变量值，改变的是副本。**
  
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

一个信号可以关联多个槽函数，connect多次，槽函数**执行顺序随机**，和注册顺序无关。

## （5）信号触发信号

就是收到信号不进行函数处理，而发送另一个信号：

receiver是接下来发送信号的对象

```cpp
A* a = new A();
connect(this->pushbutton, &QPushButton::click, a, &A::test);
//pushbutton的信号没有被handle，而是触发了A的test信号
```

## （6）信号和槽的关联可断开

```cpp
disconnect(sender, signal, receiver, slot);
```

# 8. QWidget

---

> **QDialog、QMainWindow、所有控件类的父类。**

## （1）常用API

```cpp
//设定位置
void setGeometry(int x,int y,int w,int h);	
void setGeometry(const QRect&)

//移动位置
void move(int x,int y);		
void move(const QPoint&);

//设定尺寸
void resize(int w,int h);	
void resize(const QRect&);	
    
//设定最大
void setMaximumSize(const QSize&);
void setMaximumSize(int maxw, int maxh);
void setMaximumHeight(int maxh);
void setMinimumHeight(int minh);

//最小尺寸
void setMinimumSize(const QSize&);
void setMinimumSize(int maxw, int maxh);
//设定固定尺寸
void setFixedSize(const QSize&);
void setFixedSize(int w,int h);
void setFixedHeight(int h);
void setFixed

//同时设定尺寸、位置
QRect frameGeometry()	//获取x,y,w,h信息，包括边框
const QRect& Geometry() const	//不包括边框
    
//获取尺寸
QSize size() const
int height() const;
int width() const;
int maximumHeight() const;
int minimumHeight() const;
int maximumWidth() const;
int minimumWidth() const;

//设定图标
QIcon::QIcon(const QString iconPath);
void setWindowIcon(const QIcon& icon);
//获得图标
QIcon windowIcon() const;

//窗口标题
void setWindowTitle(const QString& title);
//得到窗口标题
QString windowTitle();

//使窗口使能
void setEnabled(bool);
//判断窗口是否使能
bool isEnabled() const;

//窗口
[slot]	bool QWidget::close()
[slot]	void QWidget::show()	//不阻塞
    	void QWidget::exec()	//阻塞
[slot]	void QWidget::hide()
[slot]	void QWidget::showFullScreen()
[slot]	void QWidget::showMaximized()
[slot]	void QWidget::showMinimized()
    
//点击右键发射信号。传参：鼠标位置pos（相对于当前窗口）。这个参数
[signal] void QWidget::customeContextMenuRequested(const QPoint& pos);
```

```cpp
//自动窗口位置demo
QRect rect = this->frameGeometry();
move(rect.topLeft() + QPoint(10,20));	//向右移动10，下移动20
```

## （2）添加右键菜单

* 菜单对象

  ```cpp
  QMenu menu;
  ```

* 菜单里选项

  ```cpp
  QAction* addAction(QString& text);
  ```

* 菜单显示

  ```cpp
  exec(QCursor::pos());	//在鼠标位置处（相对于整个屏幕）显示
  exec(somewidget.maptoGlobal(QPoint(x,y)));
  ```

  

```cpp
//设定右键菜单策略：按下右键会发射信号customeContextMenuRequested(const QPoint& pos)
QWidget::setContextMenuPolicy(Qt::CustomContextMenu);
connect(this, QWidget::customContextMenuRequested,this,[=](){
    QMenu menu;
    menu.addAction("apple");
    menu.addAction("orange");
   	menu.addAction("peach");
    menu.exec(QCursor::pos());
});
```

# 9. QDialog

https://subingwen.cn/qt/qt-base-window/#3-QDialog%E7%9A%84%E5%AD%90%E7%B1%BB

> 对话框

## （1）常用API

调用accept()、reject()、done(int)后不但会影响exec()返回值，还会触发信号。

```cpp
//模态显示（阻塞）。点击选项后，dialog消失，然后exec返回值
[slot]	int exec();		
//调用后exec()返回Qialog::Accepted
[slot]	void accept()	
//调用后exec()返回Qialog::Rejected  
[slot]	void reject()	
[slot]	void done(int r)	//传入r多少，exec返回多少。特别的，传入1返回Qialog::Accepted，传入0返回Qialog::Rejected  

signal:
//done执行完后，会自动触发finished信号，其中参数result和r相同
//特别的，r传入1触发accepted，传入0触发rejected
finished(int result)	
accepted()
rejected()
```

# 10. QMessage

---

> 就是MessageBox

关注Static Public Members

# 11. QFileDialog

---

> 打开文件

常用静态成员：

```cpp
//只返回目录路径
QString getExistingDirectory()
//返回文件路径
QString QFileDialog::getOpenFileName()
//获取打开多个文件的路径
QStringList getOpenFileNames()
//获取open的文件路径，然后write
QString getSaveFileName()
```

| -               | -                                                           |
| --------------- | ----------------------------------------------------------- |
| caption         | 标题                                                        |
| dir             | 默认打开的目录                                              |
| filter          | 过滤满足条件的文件：`Images(*.png *.jpg);;TextFiles(*.tet)` |
| selectedFileter | 默认filter                                                  |

# 12. QFontDialog

---

> 选字体



# 13. QColorDialog

---

> 选颜色

# 14. QInputDialog

---

> 输入框、下拉输入框

| -         | -                  |
| --------- | ------------------ |
| title     | 标题               |
| label     | 提示信息           |
| value     | 默认值             |
| decimals  | 小数点后几位       |
| ok        | 输入是否成功       |
| **items** | **下拉菜单**       |
| editable  | 下拉菜单是否可编辑 |
| step      | 步长               |

# 15. 

---













# 16. 操作数据库

---

> 连接数据库服务器，增删改查。
>
> 编译Qt提供的**数据库驱动**编译成动态库。要和使用的**编译器kit**保持一致。

在这里查看、修改编译器kit使用：

![image-20240419201636130](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240419201636130.png)

**添加sql模块**：

打开`.pro`文件，在`QT+=`后面添加`sql`

```cpp
QT	+= core gui sql
```

**常用类：**

```cpp
QSqlDatabase	//通过这个类增、删、复制、关闭数据库
QSqlQuery		//数据库查询类
QSqlError		//数据库操作失败，通过这个类获取相关的错误信息    
QSqlRecord		//数据库记录
QSqlField		//数据库字段
QSqlQueryModel	//对QSqlQuery结果的封装，作为视图类（QTableView）的数据源
```

**流程：**

* 创建数据库实例
* 连接数据库
* 执行操作
* 关闭连接

## （1）QSqlDatabase类

* 创建数据库实例：

  ```cpp
  //创建数据库实例
  [static] QSqlDatabase::addDatabase(QString& type, QString& connectionName);//type-数据库类型，connectionName-实例名
  
  //克隆实例，必须指定另一个实例名进行区分
  [static] QSqlDatabase::cloneDatabase(QSqlDatabase& another, QString& connectionName);
  
  //删除实例
  [static] void removeDatabase(QString& connectionName);
  
  //获取所有实例名
  [static] QStringList connectionNames();	
  
  //判断实例是否存在
  [static] bool contains(QString& connectionName);
  
  //根据实例名获取实例对象
  [static] QSalDatabase database(QString& connectionName, bool open=true);	//open=true指定返回实例的连接是打开还是关闭
  
  //返回当前可用的数据库驱动名
  QStringList drivers();
  ```

* 指定连接信息：

  ```cpp
  //指定连接名
  void setDatabaseName(QString& name);
  //指定主机名
  void setHostName(QString& host);
  //指定用户名
  void setUserName(QString& usr);
  //指定密码
  void setPassword(QString& pwd);
  //设定端口号
  void setPort(int port);	//MySQL默认3306
  //连接
  bool open();
  //判断当前数据库实例是否连接
  bool isOpen();
  //获取连接失败的原因信息
  QSqlError lastError();
  ```

* demo

  ```cpp
  QStringList dblist = QSqlDatabase::drivers();
  qDebug()<<dblist;
  
  //创建实例，设置连接参数
  QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");
  db.setHostName("localhost");
  db.setUserName("root");
  db.setPassword("root");
  db.setPort(3306);
  
  //连接
  if(db.isOpen())
  {
   	qDebug()<<"succeed..";
  }
  else
  {
      qDebug()<<"失败原因："<<db.lastError().text();
  }
  ```

  





















