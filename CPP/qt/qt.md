# 0. 杂项

---

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

## 快捷键

`工具->选项->环境->键盘`

在搜索栏里搜索关键字，可以自己设置：

> 移动行-搜moveline
>
> 复制行-copy
>
> 补全-complete
>
> 删除一行-deleteline

| -                        | -                        |
| ------------------------ | ------------------------ |
| 添加头文件               | atl+enter                |
| 由函数声明快速生成函数体 | alt+enter                |
| f4                       | 跳转头文件、源文件       |
| ctrl-鼠标左键/f2         | 跳转到函数定义           |
| F5                       | 开始调试                 |
| F10                      | 单步前进                 |
| F11                      | 单步进入函数             |
| ///                      | 回车后自动创建下一行注释 |
| ctrl+space               | 补全                     |
| ctrl-ins                 | 复制行                   |
| 移动行                   | alt-up、alt-down         |
| 按住alt                  | 同步输入                 |
| ctrl-l                   | 删除一行                 |

## 没用到的参数不要报错

```cpp
Q_UNUNSED(a);	//变量a没用到，编译器报错，用这个宏编译器就不提醒了
```

## 在项目里创建子文件夹

https://blog.csdn.net/y_q_m/article/details/106293218



# 1. Qt安装

---

https://blog.csdn.net/zhizhengguan/article/details/107567449

## 在线版（用这个）

* [在线安装包网址](https://mirrors.tuna.tsinghua.edu.cn/qt/official_releases/online_installers/)

* [在线版本安装流程：先注册账号。安装包不分商业版、开源版，根据你的账号申请了哪个版本自动下载。若下载的是商业版，则重新注册一个账号申请开源版然后登录。](https://blog.csdn.net/appup/article/details/132367320)

* [在线版环境配置过程](https://blog.csdn.net/weixin_45112748/article/details/132101874)

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

## （1）QByteArray

## （2）QString

QString是对QByteArray进一步封装。后者只是简单封装char*

QString不能直接转成char*，需要先转成QByteArray：

```cpp
QByteArray QString::toUtf8() const
```

## （3）字符串拼接

占位符：`%1`

**注意：arg在QString()的后面。不是在字符串的后面**

```cpp
QString str = QString("%1....%2").arg("hhh").arg("zzz")	//hhh....zzz
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

## （4）字符串转int/double

* int转QString

  ```cpp
  int a = QString::number(10);
  ```

* QString转int

  ```cpp
  QString str = "123";
  int a = str.toInt();
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

```cpp
int right() const noexcept	//右边界的x坐标
```



# 6. 定时器

---

## （1）QTimer类

```cpp
#include <QTimer>
```

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

## （2）QObject类的timerEvent事件

```cpp
#include <QTimerEvent>

virtual void timerEvent(QTimerEvent* ev) override;
```

QObject的子类都有这个事件。

**所有启动的定时器都会触发`timerEvent()`事件。**

```cpp
int id = QObject::startTimer(int interval);	//启动定时器
```

```cpp
void QObject::killTimer(int id);	//关闭定时器
```

通过`QObject::startTimer()`启动定时器，返回定时器的id。

通过`QObject::killTimer()`关闭定时器。

所有定时器在`timerEvent()`中通过判断当前是哪个定时器触发`timerEvent`事件，进行逻辑处理：

```cpp
m_timer_loop = startTimer(1000);
m_timer_msg = startTimer(3000);


if(m_timer_loop == event->timerId())
    {
        qDebug()<<"timer_loop="<<m_timer_loop<<"event_id="<<event->timerId();

        m_pixmap.load(QString(":/%1").arg((m_index++)%3));
        update();
    }
    else if(m_timer_msg == event->timerId())
    {
        qDebug()<<"timer_msg="<<m_timer_msg<<"event_id="<<event->timerId();
        QMessageBox::information(this, "EVA", "ayanamirei");
    }
```

# 7. 信号槽

---

> 信号：
>
> 槽函数：
>
> connect()：属于`QObject`类的函数，将槽函数注册到信号上：
>
> ```cpp
> connect(const Object sender, &QObject::signal,const Object receiver, &QObject::method)
> ```
>
> **只有QObject的子类才能connect。**

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

  **返回值void**

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

  **加不加`emit`都行，只是便于阅读。**

* **传入信号的参数，会被传递到槽函数中。**

  所以槽函数的参数列表要完全保持一致。（信号传递的参数个数只能>=槽函数参数个数）

## （3）自定义槽函数

* 用`public slots:`声明

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

一个信号要触发多个函数的时候，可以分开写。

## （5）信号触发信号

就是收到信号不进行函数处理，而发送另一个信号：

receiver是接下来发送信号的对象

```cpp
A* a = new A();
connect(this->pushbutton, &QPushButton::click, a, &A::test);
//pushbutton的信号没有被handle，而是触发了A的test信号
```

## （6）传递参数

clicked这种自带信号本身不带参数，所以一般在其handler里发送自定义的信号传参。

**信号传递的参数类型若不支持，需要通过`qRegisterMetaType()`函数进行注册。**

`main.cpp`中添加：

```cpp
qRegisterMetaType<QVector<int>>("QVector<int>");	//注册QVector<int>类型
```

## （7）信号和槽的关联断开

```cpp
disconnect(sender, signal, receiver, slot);
```

# 8. qt事件处理函数

---

事件处理函数名称：`xxxEvent`，去`protected virtual`里找

一些内置的信号和对应的处理函数。

要声明为`protected`

**只要事件产生了，对应的事件处理函数handler就会被框架自动调用。若想要自行定义函数的功能。用子类继承然后override。但，override以后，父类的该函数体内的行为将不执行了，所以再调用一下父类的的事件处理函数。**

```cpp
//MainWindow.h
MainWindow:public QMainWindow
{
protected:
	virtual void closeEvent(QCloseEvent* ev) override;	//窗口关闭事件handler
	virtual void resizeEvent(QResizeEvent* ev) override;	//窗口尺寸改变handler
}

//MainWindow.cpp
MainWindow:QMainWindow	//定义类继承自QMainWindow
{
    //override类QMainWindow的closeEvent函数
    void MainWindow::closeEvent(QCloseEvent* ev)	//参数是事件，父类是QEvent
    {
        int ret = QMessageBox::question(this, "提问", "你要关闭窗口吗？");
        if(ret==QMessageBox::Yes)
        {
            ev->accept();	//QEvent的函数accept()，接受事件
        }
        else
        {
            ev->ignore();	//忽视事件，向上层窗口传递事件	
        }
        QMainWindow::closeEvent(ev);	//调用父类默认的closeEvent事件处理
    }
    
    void  MainWindow::resizeEvent((QResizeEvent* ev)
   	{
   		qDebug()<<"oldsize="<<ev->oldSize()<<"size="<<ev->size();
        QMainWindow::resizeEvent(ev);
   	}                              
}    

```

## （1）鼠标事件

| -鼠标常用函数                | -                        |
| ---------------------------- | ------------------------ |
| 鼠标按下相对于整个屏幕的坐标 | QMouseEvent::globalPos() |
| 鼠标按下相对于当前控件的坐标 | MouseEvent::pos()        |
| 鼠标按下的按钮               | MouseEvent::button()     |
|                              |                          |
|                              |                          |
|                              |                          |

* QWidget的事件：

```cpp

    
virtual void mousePressEvent(QMouseEvent* ev) override;
virtual void mouseMoveEvent(QMouseEvent* ev) override;

//注意参数是QEvent而不是QMouseEvent
virtual void enterEvent(QEvent* ev);	//只鼠标触碰到边缘时触发一次。进入内部后不会多次触发。
virtual void leaveEvent(QEvent* ev);
```

```cpp
void rei::mousePressEvent(QMouseEvent *ev)
{
    if(ev->button() == Qt::LeftButton)  //判断按下的是左键
    {
        mousePosInRei = ev->globalPos() - this->geometry().topLeft();   //记录鼠标按下时在图片内的相对位置
    }
}

void rei::mouseMoveEvent(QMouseEvent *ev)
{
    //位与运算：判断左键参与移动
    if(ev->buttons() && Qt::LeftButton) 
    {
        this->move(ev->globalPos() - this->mousePosInRei);

    }
}
```

## （2）重绘事件

```cpp
virtual void paintEvent()
```





# 9. 容器

---

## （1）QVector

* 不仅仅提供拷贝构造，还支持移动构造。

* 取数据

  取数据可用：`at()`、`operator[]`，前者返回const T&，后者返回T&。

  **at()很快**，不发生深拷贝。

  所以：

  只是读取数据而不修改，用`at()`

  需要修改数据用`[]`

 



# 10. 程序执行时间

---

```cpp
#include <QElapsedTimer>

QElapsedTimer* elapsetimer = new QElapsedTimer();
elapsetimer->start();
bubbleSort(this->vec);
qDebug()<<"耗时:" <<elapsetimer->elapsed()<<" ms";
```



# 11. 随机数

---

```cpp
#include <QRandomGenerator>

QRandomGenerator* QRandomGenerator::global();	//返回一个QRandomGenerator指针对象
int randnum = int bounded(int highest);	//设定上限。可为负值
int randnum = int bounded(int lowest, int highest);	//设定上下限。可为负值
```

# 12. 获取屏幕尺寸

---

`QDesktopWidget`类，但已被弃用。

```cpp
QDesktopWidget* wt = QApplication::desktop();
QRect screen = wt->geometry();

//通过scrren获取屏幕尺寸
int rightX = screen.right();	//返回右边界横坐标
int leftX = screen.left();
int topY = scrren.top();
int bottomY = screen.botton();
```

```cpp
#include <QGuiApplication>
#include <QScreen>

[static] QList<QScreen*> QGuiApplication::screens();
QRect screen = QGuiApplication::screens().first()->geometry();
```

# 13. IO操作

---





# 14. 图片

---

## （1）QPixmap

* API

  ```cpp
  QPixmap pixmap;
  //载入图片
  pixmap.load(path);
  
  //加载图片后，图片尺寸就可获得
  pixmap.size();
  //获取图片data
  pixmap.data();
  ```

##  （2）QPainter

```cpp
void SimpleExampleWidget::paintEvent(QPaintEvent *)
{
    QPainter painter(this);		//指定画在什么上面this
    painter.setPen(Qt::blue);	//画笔颜色
    painter.setFont(QFont("Arial", 30));	//字体颜色
    painter.drawText(rect(), Qt::AlignCenter, "Qt");	//画布范围rect()
}
```

## （3）Image



# 15. 添加资源

---

* 项目名右键

  Qt

  Qt Source File

  输入资源名

* 右键qrc文件`OPen in Editor`

  Add Prefix添加前缀`/`

  Add Files

* 文件上右键`Copy Path`复制路径
* 可选中文件，在`Alias`中指定别名

# 16. 自定义控件

---

 ## （1）在designer添加自定义控件

拖一个父类，然后右键`提升为`，填写自定义控件类名。

![image-20240421014917937](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240421014917937.png)

## （2）自定义按钮

* QPainter绘图
* **update()触发paintEvent事件。重绘不会导致重新调用构造函数。**
* enterEvent-鼠标进入事件
* leaveEvent-鼠标离开事件
* mousePressEvent-鼠标点击事件
* paintEvent-重绘事件
* 自定义控件，可通过emit发射自定义信号
* res添加资源文件。可给文件修改别名`Alias`。

```cpp
//myButton.h

#ifndef MYBUTTON_H
#define MYBUTTON_H

#include <QWidget>


class myButton : public QWidget
{
    Q_OBJECT
public:
    explicit myButton(QWidget *parent = nullptr);
    explicit myButton(QWidget *parent = nullptr, int w = 100, int h = 50);	//构造

protected:
    void enterEvent(QEvent *event); //QWidget的内置事件处理：鼠标进入事件

    void leaveEvent(QEvent* event); //鼠标离开事件

    void mousePressEvent(QMouseEvent *event);   //鼠标点击事件

    void paintEvent(QPaintEvent *event);    //重绘事件
    
     void timerEvent(QTimerEvent *event);    //定时器事件处理

private:
    QPixmap m_pixmap;
    int m_timer_loop;	//图片变化定时器id
    int m_timer_msg;	//弹窗定时器id
    int m_index = 0;

signals:
    void clicked();	//自定义信号clicked
};

#endif // MYBUTTON_H
```

```cpp
//myButton.cpp
#include "mybutton.h"
#include <QPainter>

myButton::myButton(QWidget *parent)
    : QWidget{parent}
{
    this->setFixedSize(400,500);	//myButton固定大小
    m_pixmap.load("://pics/88861cbe3e2276f8830499ede37ce892_r.jpg");	//初始载入图片
    m_timer_loop = startTimer(1000);
    m_timer_msg = startTimer(3000);
    qDebug()<<"construct once";
}

myButton::myButton(QWidget *parent, int w, int h)
    : QWidget{parent}
{
    this->setFixedSize(w, h);	//myButton大小
    m_pixmap.load("://pics/88861cbe3e2276f8830499ede37ce892_r.jpg");	//初始载入图片
    m_timer_loop = startTimer(1000);
    m_timer_msg = startTimer(3000);
    qDebug()<<"construct once";
}

void myButton::enterEvent(QEvent *event)
{
    m_pixmap.load("://pics/v2-586102f656d80f55378acb6c938816c4_r.jpg");	//鼠标进入载入新图片
    update();   //触发paintEvent()
    QWidget::enterEvent(event);  //voerrideWidget的enterEvent后，调用Widget默认的enterEvent()
}

void myButton::leaveEvent(QEvent *event)
{
    m_pixmap.load("://pics/88861cbe3e2276f8830499ede37ce892_r.jpg");
    update();
    QWidget::leaveEvent(event);
}

void myButton::mousePressEvent(QMouseEvent *event)
{
    m_pixmap.load(":/pics/v2-854298e5ee5f811b943b4591185a62e0_r.jpg");
    update();
    emit clicked(); //发出自定义信号clicked
    QWidget::mousePressEvent(event);
}

void myButton::paintEvent(QPaintEvent *event)
{
    Q_UNUSED(event);
    //调用update()会自动触发paintEvent事件
    QPainter p(this);
    p.drawPixmap(this->rect(),m_pixmap);
}

//startTimer开启的定时器都会触发timerEvent事件
void myButton::timerEvent(QTimerEvent *event)
{
    //通过event->timerId()获取当前是哪个定时器触发了事件
    if(m_timer_loop == event->timerId())
    {
        qDebug()<<"timer_loop="<<m_timer_loop<<"event_id="<<event->timerId();

        m_pixmap.load(QString(":/%1").arg((m_index++)%3));
        update();
    }
    else if(m_timer_msg == event->timerId())
    {
        qDebug()<<"timer_msg="<<m_timer_msg<<"event_id="<<event->timerId();
        QMessageBox::information(this, "EVA", "ayanamirei");
    }

}
```

```cpp
//MainWindow
#ifdef __customButton__
#include"mybutton.h"

#ifdef __customButton__

    mybutton2 = new myButton(this);
    connect(this->mybutton2, &myButton::clicked, this, [=](){
        QMessageBox::information(this, "title", "mybutton clicked..");
    });

#endif

//如果通过designer添加
//打开ui文件，拖一个父类控件，也就是拖一个Widget
//右键提升为。在子类名称中填写自定义类名：myButton
 connect(ui->button01, &myButton::clicked, this, [=](){
        QMessageBox::information(this, "title", "mybutton clicked..");
    });
```

# 17. 数据库

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

## （0）编译MySQL驱动

* [安装MySQL](https://dev.mysql.com/downloads/installer/)，已安装跳过。

  通过remove可卸载之前已安装版本。

  但还需要：注册表`regedit`中在`\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services`找到之前mysql版本的服务名，一般为mysql或mysql80，删除，重启。从而在`services.msc`中清除该服务。

  

  > ![image-20240422152623790](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240422152623790.png)
  >
  > **需要选中要安装的组件，才能弹出【Advanced Options】，修改安装路径。**
  >
  > 根据需要设定下root密码。一路next。
  >
  > ![](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240422152920187.png)

* 查看Qt安装目录下是否有`源码目录Src`，编译驱动是基于这个目录进行的。**没有安装的去qt官网下载(Source)或者通过qt在线安装工具，对你的qt添加Source组件：**

* 将下面2个目录添加到环境变量：

  ```bash
  D:\Qt\5.15.2\mingw81_64\bin
  D:\Qt\Tools\mingw810_64\bin
  ```

* 打开qt安装目录，进入`D:\Qt\5.15.2\Src\qtbase\src\plugins\sqldrivers （根据你自己的qt安装目录进行调整)`，在这个目录输入`ctrl-l`，`cmd`打开命令行窗口。

* 编译：

  > 目录写成`/`分隔。
  >
  > 如果之前编译失败，在`sqldrivers`目录下点击按时间顺序排列，把最新的、编译产生的文件全删掉。执行下面的qmake重新编译。

  ```cmd
  qmake -- MYSQL_INCDIR="D:/MySQL/include" MYSQL_LIBDIR="D:/MySQL/lib"
  //向qmake指定MySQL的include目录和库目录
  
  //mysql显示yes则成功
  ```

  ![image-20240422160925811](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240422160925811.png)

  ```cmd
  mingw32-make sub-mysql 和 mingw32-make install进行编译和安装。
  ```

  在目录`Src\qtbase\src\plugins\sqldrivers\plugins\sqldrivers`下出现2文件：

  > qsqlmysql.dll
  >
  > qsqlmysql.dll.debug

* 使用驱动：

  把上面2个文件，拷贝到当前项目使用的编译器的目录下，如：`mingw81_64/plugins/sqldrivers`（使用mingw_64编译器）

  把`mysql/lib`下的`libmysql.dll`拷贝到编译器的bin目录下

* **qtcreator的.pro文件**

  ```cpp
  QT += sql
  ```

  ```cpp
  #include"QSqlDatabase"
  
  QStringList dblist = QSqlDatabase::drivers();
  qDebug()<<dblist;
  ```

## （1）连接数据库

[`QSqlDatabase类`](https://doc.qt.io/qt-5/qsqldatabase.html)

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
  //设定要连接的s库名
  void setDatabaseName(QString& dbName);
  //连接
  bool open();
  //判断当前数据库实例是否连接
  bool isOpen();
  //获取最后一个错误
  QSqlError lastError();
  ```

## （2）增删改查

[`QSqlQuery`类](https://doc.qt.io/qt-5/qsqlquery.html)

```cpp
//构造：
//指定sql语句：sqlquery-调用的sql语句，db-数据库实例
QSqlQuery(const QString& sqlquery, QSqlDatabase db);
//执行sql
bool exec();
//QSqlQuery里没有指定sqlquery时，调用有参数的exec()，将sql传入
bool exec(const QString& sqlquery);
```

INSERT、DELETE、UPDATE，执行增删改语句需要判断sql是否执行成功。

* SELECT语句得到的结果保存在QSqlQuery实例里，取记录：

```cpp
//遍历完返回false。查询结果为空。
bool next();
//返回第一条记录
bool first();
//返回最后一条记录
bool last();
```

* 取记录的字段值：

  返回值是QVariant。
  
  **用QVariant封装数据，然后用type()获取Type，用toxxxx转成对应类型数据。**

```cpp
//按index取值。index从0开始
QVariant value(int index);
//按字段名取值
QVariant value(const QString& fieldname);
```

## （3）基本流程demo

```cpp
QStringList dblist = QSqlDatabase::drivers();
qDebug()<<dblist;

//创建实例，设置连接参数
QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");
db.setHostName("localhost");
db.setPort(3306);
db.setUserName("root");
db.setPassword("root");
db.setDatabaseName("sys");

//连接
if(db.open())
{
 	qDebug()<<"succeed..";
}
else
{
    qDebug()<<"失败原因："<<db.lastError().text();
}

  db.transaction();
    QSqlQuery query;
    QString sqlquery = "SELECT * FROM name";
    if(query.exec(sqlquery))
    {
        while(query.next())
        {
            qDebug()<<query.value("name").toString();
        }
    }

	db.transaction();
    QString sqlinsert = "INSERT INTO name VALUES('101','qqq')";

   if(query.exec(sqlinsert))
   {
       db.commit();
       qDebug()<<"insert succeed..";
   }
   else
   {
       db.rollback();
       qDebug()<<"insert failed..";
   }

	//关闭连接
   db.close();	
   qDebug()<<db.isOpen();	//false
```

## （4）错误信息

`QSqlError`类

```cpp
//获取最后一个错误
QSqlError QSqlDatabase::lastError();
//调用text()方法，获取错误信息
QString QSqlError::text();
```

## （5）事务

* API

  ```cpp
  //开启事务
  bool QSqlDatabase::transaction();	
  //提交事务
  bool QSqlDatabase::commit();
  //回滚
  bool QSqlDatabase::rollback();
  ```

* demo

  ```cpp
  QString sql = "INSERT INTO person VALUES(9,20,'xxx')";
  db.transaction();	//开启事务
  QSqlQuery query;
  if(query.exec(sql))	//判断语句是否执行成功
  {
      db.commit();	//提交
  }
  else
  {
      db.rollback();	//回滚
  }
  ```

# 18. 多线程

---

> UI线程负责**事件处理、窗口显示数据的更新**
>
> 子线程负责业务逻辑，**不能更新UI中的数据显示**
>
> **线程间传递数据，可通过信号的参数**

## （1）方法1—别用这个

> **继承QThread类**，封装要多线程执行的代码，**override其run()**函数编写任务。调用`start()`启动线程。
>
> 自定义finished(int num)信号，在run()结束时调用用来传参。

* API

  ```cpp
  QThread类
  //判断线程是否执行完
  bool QThread::isFinshed();	
  //判断线程是否正在执行
  bool QThread::isRunning();
  //给线程设置优先级
  void QThread::setPriority(Priority p);	//Priority是枚举。查文档
  //获取当前线程优先级
  Priority QThread::Priority();	
  //线程退出函数
  void QThread::exit();	//调用这个函数后线程不会立刻退出，因为还有任务没执行完
  //等待线程执行完。一般都是先调用exit()再调用wait()，搭配使用。
  bool QThread::wait();
  ```
  
   ```cpp
   //线程执行完毕后会发出这个信号
   [signal] void QThread::finished();	
   ```
  
  ```cpp
  [slot] void start(Priority p);	//线程启动。自动执行run()函数。
  [slot] void terminate();	//线程直接终止（一般不用。后面不用wait）
  ```
  
  ```cpp
  //返回指向当前运行线程的指针
  [static] QThread* QThread::currentThread();		
  //线程休眠
  [static] void QThread::msleep(mesc);	//毫秒
  sleep();							//秒
  usleep();							//微秒
  ```
  
  ```cpp
  //handler
  virtual protected void QThread::run() override
  {}
  ```
  
  * demo
  
  ```cpp
  //generator.h
  
  #ifndef GENERATOR_H
  #define GENERATOR_H
  
  #include <QObject>
  #include <QThread>
  
  class Generator : public QThread
  {
      Q_OBJECT
  public:
      explicit Generator(QObject *parent = nullptr);
  protected:
      virtual void run() override;	//override QThread的run
  private:
      int m_num;
      QVector<int> m_vec;		//存储随机生成的数字
  
  signals:
      void finished(QVector<int>);	//自定义run()执行完后发送的信号
  public slots:
      void recvNum(int num);			//接收num的槽函数。因为run()不能接收参数。
  
  };
  
  #endif // GENERATOR_H
  ```
  
  ```cpp
  //generator.cpp
  
  #include "generator.h"
  
  Generator::Generator(QObject *parent)
      : QThread{parent}
  {
  }
  
  void Generator::recvNum(int n)
  {
      //获取生成个数
      this->m_num=n;
      //启动子线程
      this->start();
  }
  
  void Generator::run()
  {
      for(int i=0;i<m_num;i++)
      {
          m_vec.push_back(rand()%10000);
      }
  
      emit finished(m_vec);	//发射子线程执行完的信号
  
  }
  ```
  
  ```cpp
  //bubblesort.h
  
  #ifndef BUBBLESORT_H
  #define BUBBLESORT_H
  
  #include <QObject>
  #include <qthread.h>
  #include <QVector>
  #include <QElapsedTimer>
  
  class BubbleSort : public QThread
  {
      Q_OBJECT
  public:
      explicit BubbleSort(QObject *parent = nullptr);
      void bubbleSort(QVector<int>& vec);
      QVector<int> vec;
  
  signals:
      void finished(QVector<int> sortedVec);
  
  public slots:
      void run() override;
      void recvNums(QVector<int> vec);	//接收参数。因为run()不能接收参数。
  };
  
  #endif // BUBBLESORT_H
  ```
  
  ```cpp
  //bubblesort.cpp
  
  #include "bubblesort.h"
  #include <QDebug>
  
  BubbleSort::BubbleSort(QObject *parent)
      : QThread{parent}
  {}
  
  void BubbleSort::recvNums(QVector<int> vec)
  {
      this->vec=std::move(vec);	//接收待排序数组
      this->start();			//启动子线程。自动执行run()
  }
  
  //冒泡排序函数
  void BubbleSort::bubbleSort(QVector<int>& vec)
  {
      int n=vec.count();
      for (int i = 0; i < n - 1; i++) {
          for (int j = 0; j < n - i - 1; j++) {
              if (vec.at(j) > vec.at(j+1)) {
  				//at()只读
                  // int temp = vec.at(j);
                  // vec.at(j)=vec.at(j+1);
                  // vec.at(j+1)=temp;
                  int temp=vec[j];
                  vec[j]=vec[j+1];
                  vec[j+1]=temp;
              }
          }
      }
  }
  
  void BubbleSort::run()
  {
      //子线程
      QElapsedTimer* elapsetimer = new QElapsedTimer();	
      elapsetimer->start();	//启动计时
      bubbleSort(this->vec);	//排序
      emit finished(this->vec);	//发射自定义finished信号
      qDebug()<<"耗时:" <<elapsetimer->elapsed()<<" ms";
  }
  ```
  
  ```cpp
  //MainWindow.h
  
  #ifndef MAINWINDOW_H
  #define MAINWINDOW_H
  
  #include <QMainWindow>
  #include"mybutton.h"
  QT_BEGIN_NAMESPACE
  namespace Ui {
  class MainWindow;
  }
  QT_END_NAMESPACE
  
  class MainWindow : public QMainWindow
  {
      Q_OBJECT
  
  public:
      MainWindow(QWidget *parent = nullptr);
      ~MainWindow();
  signals:
      void transNum(int num);
  
  private slots:
      void on_pushButton_01_clicked();
  
  private:
      Ui::MainWindow *ui;
      myButton* mybutton2;
  };
  #endif // MAINWINDOW_H
  ```
  
  ```cpp
  //MainWindow.cpp
  
  #include <Bubblesort.h>
  #include"generator.h"
  
  #define __multithread__
  
  #ifdef __multithread__
  
  
      Generator* gen = new Generator();
      //点击按钮把num传给gen。在recvNum中启动子线程
      connect(this, &MainWindow::transNum, gen, &Generator::recvNum);
      //bubblesort nums
      BubbleSort* bubble = new BubbleSort();
      connect(gen, &Generator::finished, bubble, &BubbleSort::recvNums);
      connect(bubble, &BubbleSort::finished, this, [=](QVector<int> vec){
          for(auto it=vec.begin();it!=vec.end();it++)
          {
              ui->listWidget_2->addItem(QString::number(*it, 10));
          }
      });
  
  
      //gen中子线程结束后，发送自定义finished信号传参给this
      connect(gen, &Generator::finished, this, [=](QVector<int> vec){
          for(auto it=vec.begin();it!=vec.end();it++)
          {
              ui->listWidget->addItem(QString::number(*it, 10));
          }
  
  
      });
  #endif
  
  void MainWindow::on_pushButton_01_clicked()
  {
      //按钮事件不带参数，所以自定义一个transNum信号传参
      emit transNum(ui->lineEdit->text().toInt());
  }
  ```
  

## （2）方法2—继承QObject、moveToThread

> **任务类继承`QObject`，**任务函数是普通函数。
>
> 调用任务类对象的`moveToThread(QThread*)`移动到子线程中，调用子线程对象的`start()`启动子线程。
>
> **此时任务函数未执行。**手动调用任务类的任务函数，此时函数运行在子线程上。
>
> 所以子线程可以在任务函数执行前先start()

```cpp
class MyTask:public QObject
{
    void work1(int num)
    {
        ....
    }
    
    void work2(int num)
    {
        ....
    }
    
    void work3(int num)
    {
        ....
    }
}

int main()
{
    //任务类和QThread都不能有父对象
    QThread* thread1= new QThread();
    
    MyTask* task = new MyTask();
    MyTask2* task2 = new MyTask2();
   	MyTask3* task3 = new MyTask3();
    
    task->moveToThread(thread1);
    task2->moveToThread(thread2);
    task3->moveToThread(thread3);
    
    //注意thread1在this上定义，所以接收对象是this
    connect(button_threadstart, QPUshButton::clicked, this, [=](){
        thread1->start();	//先启动线程1
    });
   //在thread1上运行work1
    task->work1(9);	
    //OK,task2在thread2上运行
    connect(button, QPushButton::clicked, task2, &MyTask2::work);	
    
    //error，task3在this所在的主线程上执行。因为信号接受者是this，不是task3，task3在thread3上。
    connect(button, QPushButton::clicked, this, [](){
        task3->work();	
    });
}
```

### 注意点：

* 任务类是QObject的子类。
* 任务类创建时不能指定父类。
* 一个任务类可以封装多个函数。
* 一个线程可以接收多个任务对象。调用多个任务函数时，任务函数在子线程上线性调用。
* 方法1中`run()`函数不能带参数。而方法2任务类封装的任务函数可以有参数，直接传参就能用。很方便。

* 方法1，所有的任务都要放在run()里。方法2，可能封装多个任务函数，把逻辑分开。
* 任务函数是在任务类所在线程上执行的。

### 销毁线程

* 创建QThread时指定父对象。父对象销毁时会自动先销毁子对象。

* 通过父对象销毁时发出的`destroyed()`信号

  **局部变量不能在析构函数内销毁。析构函数负责销毁成员变量，若写成成员变量则可在析构内delete。**

  ```cpp
  connect(this, &MainWindow::destroy, this, [=](){
      thread1->quit();	//等价于thread->exit(0)，退出线程返回0
      thread1->wait();
      thread1->deletelater();	//封装了 delete thread1
      
      task->deletelater();	//销毁任务类对象
  });
  ```

### demo

```cpp
 //quicksort
    textEdit_03 = new QTextEdit(this);
    textEdit_03->setGeometry(550,50,200,500);

    _QuickSort* quick = new _QuickSort;
    QThread* thread1 = new QThread;
    quick->moveToThread(thread1);

	//this接收finished信号，thread1启动
    connect(gen,&Generator::finished, this, [=](){
         thread1->start();
    });
	//quick接收finished信号，调用task()
    //connect(gen, &Generator::finished, quick, &_QuickSort::task);
	connect(thread1, &QThread::finished, quick, &_QuickSort::task);	


	//下面的写法错误!!!
	//因为thread1定义在this（MainWindow)上，信号传给quick，thread1接收不到
	/*
	connect(gen, &Generator::finished, quick, [=](QVector<int> vec){
        thread1->start();
        quick->task(vec);
    });
	*/

    connect(quick, &_QuickSort::finished, this, [=](QVector<int> vec){
        for(auto it=vec.begin();it!=vec.end();it++)
        {
            textEdit_03->append(QString::number(*it, 10));
        }
    });

	//释放资源时机：MainWindow销毁时
    connect(this,&MainWindow::destroyed,this,[=](){
        thread1->quit();
        thread1->wait();
        thread1->deleteLater();

        quick->deleteLater();
    });
```

## （3）使用线程池—继承QRunnable、run()

> 好处：
>
> 自动释放资源
>
> 线程的管理由线程池完成，节约资源

```cpp
#include <QThreadPool>
```

```cpp
//获取Qt提供的全局线程池对象（不用自己创建）
static QThreadPool* QThreadPool::globalInstance();
//设置线程池最大线程数（可选）
void setMaxThreadCount(int maxThreadCount);
//获取最大线程数
int maxThreadCount() const;
//设定任务在线程上处理完后自动释放资源
void QRunnable::setAutoDelete(bool);
//把任务封装成Runnable对象，给线程池执行。若池中没有空闲线程，会放在任务队列等待线程执行
void QThreadPool::start(QRunnable* runnable, int priority=0);
//封装任务
//public
[pure virtual] public void QRunnable::run();	
继承QRunnable类和QObject类，用任务override这个函数
```

### demo

```cpp
class BubbleSort:public QObject,public QRunnable
{
public:
    void run() override;
}
```



# 19. Qt网络通信

---





# 20. 程序打包

## （1）流程

* 在release下编译程序

* 拷贝可执行文件、资源文件

* 需要动态库：

  通过`编译器kit/bin`下的`windeployqt`工具自动将需要的动态库拷贝到发布文件夹下。

  在发布文件夹下`ctrl-l`输入cmd打开输入

  ```powershell
  windeployqt ./demo.exe
  ```

  完成。

* 想要打包成安装版本：

  下载Inno Setup

  https://www.bilibili.com/video/BV1J5411T7or/?p=2&spm_id_from=pageDriver&vd_source=3b08e97e50222fa2ec22737f6dcb2202

![image-20240426171735894](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240426171735894.png)

指定用户安装默认目录：

选custom，输入C:\，让用户可以自行选择安装目录。

![image-20240426171807676](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240426171807676.png)

添加可执行文件和动态库：

动态库添加，选择Add folder，选Y包含所以子目录。

![image-20240426172037524](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240426172037524.png)

询问可执行文件是否要关联一些文件格式（某些程序需要，比如：音乐、视频播放器），不需要就不勾选。

![image-20240426172201225](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240426172201225.png)

当前生成脚本的存储目录、安装包名、安装包图标、安装密码

![image-20240426172439246](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240426172439246.png)

## （2）打包MySQL程序到没有安装MySQL的主机

连不上MySQL：

https://blog.csdn.net/angiehelen/article/details/120214695



# 0. 控件常用参数设置

---

```cpp

```



# 1. QWidget

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

//同时设定尺寸、位置
QRect frameGeometry()	//获取x,y,w,h信息，包括边框
const QRect& Geometry() const	//不包括边框
    
//获取尺寸
QSize size() const;
QRect rect() const;
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

# 2. QMainWindow

## （1）API

```cpp
//标题
setWindowTitle(QString);

```





## （2）UI常用设置

### 窗口去边框

```cpp
setWindowFlags(windowFlags() | Qt::FramelessWindowHint);
```

### 窗口透明

```cpp
setAttribute(Qt::WA_TranslucentBackground);
```

### 窗口最大化

```cpp
showMaximized();
//窗口最大化后，内部的子控件需要手动调用show()才能显示
widget->show();
```

### 设置某个控件在窗口的中心

```cpp
//设置某个控件的位置在父控件的中心点
wt.move((this->width() - wt->width())/2,(this->height() - wt->height())/2);
```

# 3. QDialog

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

## （2）



# 3. QMessageBox

---

关注Static Public Members

```cpp
QMessageBox::information()
```

# 4. QFileDialog

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

# 5. QFontDialog

---

> 选字体



# 6. QColorDialog

---

> 选颜色

# 7. QInputDialog

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

# 多行文本显示TextEdit

https://blog.csdn.net/XZ2585458279/article/details/97820501

## （1）API

```cpp
//设置文本
setText();
//设置多行文本框的文本内容
setPlainText()	
//返回多行文本框的文本内容
toPlainText()
//设置多行文本框的内容为HTML文档，HTML文档是描述网页的
setHtml()	
//返回多行文本框的HTML文档内容
toHtml()
//清除多行文本框的内容
clear()	
```















