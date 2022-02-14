# 1. matplotlib绘图的组成元素

* figure——窗口（画布）

  ```python
  plt.figure(figsize=(12, 6), dpi=100, facecolor="white")		##大小、分辨率、颜色
  ```

  >**figure** 的重复利用能大大节约时间，但是 **matplotlib** 维护的 **figure** 有数量上限。并且，不断的创建新的 **figure** 实例，很容易造成内存泄漏，而应合理的复用，能大大的提高运行速度。
  >
  >此外，在某些情况下，不清理 **figure** 将有可能造成在第一幅中 **plot** 的线再次出现在第二幅图中。
  >
  >```python
  >plt.cla() # 清除axes，即当前 figure 中的活动的axes，但其他axes保持不变。
  >plt.clf() # 清除当前 figure 的所有axes，但是不关闭这个 window，所以能继续复用于其他的 plot。
  >plt.close() # 关闭 window，如果没有指定，则指当前 window。
  >plt.close('all') #关闭所有 figure windows
  >```

* Axes——图形（所有图形元素都是Axes的实例）

* title——标题

* xlabel——横轴标签

* ylabel——纵轴标签

* xlim——横轴范围

  ```python
  plt.xlim(xmin, xmax)
  ```

* ylim——纵轴范围

* **坐标轴范围还可用axis设定**

  ```python
  plt.axis(xmin, xmax, ymin, ymax)
  ```

* plot——曲线

  ```python
  plt.plot(x,y,ls="-.",lw=2,c="c",label="plot figure")
  ```

  > ls——线型
  >
  > lw——线宽
  >
  > c——颜色
  >
  > label——该曲线的标签（显示在legend中）

* legend——图例

  先plot()后legend()

  ```python 
  plt.legend(loc = 'best', bbox_to_anchor = (num1, num2))
  ```

  > **loc——图例大概位置**
  >
  > | String       | Number |
  > | ------------ | ------ |
  > | upper right  | 1      |
  > | upper left   | 2      |
  > | lower left   | 3      |
  > | lower right  | 4      |
  > | right        | 5      |
  > | center left  | 6      |
  > | center right | 7      |
  > | lower center | 8      |
  > | upper center | 9      |
  > | center       | 10     |
  >
  > **bbox_to_anchor——微调图例位置**
  >
  >  bbox_to_anchor 被赋予的二元组中，num1 用于控制 legend 的左右移动，**值越大越向右边移动**，num2 用于控制 legend 的上下移动，**值越大越向上移动。**用于微调图例的位置。
  >
  > 

* grid——网格线

  ```python
  plt.grid(True, which = 'major', axis = 'both',ls = ':', lw = 1, c = 'y')
  ```

  > True/None——设置是否显示网格。设置None时若给了其他参数，则None失效
  >
  > which——'major'显示主刻度线，'minor'显示次刻度线
  >
  > axis——以哪个坐标轴的刻度生成网格

* 注释文本

  在图形的指定位置添加文本，用于对图形进行说明

  ```python
  plt.text(2.8, 0.3, s= 'sin(x)', weight = 'bold', color = 'blue')
  ```

* [annotate](https://zhuanlan.zhihu.com/p/32501335)——箭头指向+注释文本

  ```python
  from pylab import *
  plt.annotate(text ="(0,1)", xy = (0,1), xytext = (1,1.5), weight = 'bold', color = 'red', arrowprops = dict(arrowstyle = '-|>', color = 'black', connectionstyle = 'arc3, rad = 0.5'), bbox=dict(boxstyle='round,pad=0.5', fc='yellow', ec='k',lw=1 ,alpha=0.4))
  ```

  > * xy——箭头起点坐标
  >
  > * xytext——文本坐标
  >
  > * annotate中最重要的控制，全部由**arrowprops**这个变量完成，它是一个字典类型，可以控制箭头的诸多属性
  >
  > * bbox——text的bbox属性，注释文本外框
  >
  >   
  >
  > * connectionstyle——弯曲箭头arc3、angle3
  >
  >   ```python
  >   connectionstyle = 'arc3, rad = 0.5'
  >   ```
  >
  >   ![preview](https://s2.loli.net/2022/02/13/fqDcKE9NbenaBoP.png)
  >
  >   ```python
  >   connectionstyle = 'angle3, angleA=-90,angleB=0'
  >   ```
  >
  >   ![preview](https://s2.loli.net/2022/02/13/pMTfaXEHFLDZYur.png)

* [axhline](https://matplotlib.org/stable/api/_as_gen/matplotlib.axes.Axes.axhline.html)——水平参考线

  ```python
  Axes.axhline(y=0, xmin=0, xmax=1, color='green' , linestyle='--') ##省略xmin和xmax时为横穿整个坐标轴的线
  ```

* axvline——竖直参考线

  ```python
  Axes.axvline(x=0, ymin=0, ymax=1, color='green' , linestyle='--') ##省略ymin和ymax时为横穿整个坐标轴的线
  ```

* axhspan——水平矩形区域

  **color不能省略为c**

  ```python
  axhspan(xmin = 0.0, xmax = 1.0, ymin, ymax, color, alpha)	##水平矩形的ymin和ymax不能缺省。x缺省时矩形横穿整个坐标轴
  ```

* axvspan——竖直矩形区域

  ```python
  axvspan(xmin, xmax, ymin, ymax, color, alpha)	##竖直矩形的xmin和xmax不能缺省
  ```

  ![image-20220213112408640](https://s2.loli.net/2022/02/13/ol1XvSZqz8VNi7x.png)

* 基本plot流程

  ```python
  ## plot
  x = np.linspace(-10, 10, 100)	##[-10,10]分成100份
  siny = np.sin(x)
  cosy = np.cos(x)
  
  plt.figure(figsize = (12, 6), dpi = 100, facecolor = 'white')	##设置画布：尺寸12*6，分辨率100
  plt.title('sinx/cosx')
  plt.xlabel('x')
  plt.ylabel('y')
  # plt.xlim(-2*np.pi, 2*np.pi)
  plt.axis([-10, 10, -2, 2])		## 坐标轴范围
  plt.plot(x, siny, ls = '-', lw = 2, c = 'blue', label = 'sin(x)')	## 绘制曲线
  plt.plot(x, cosy, ls = '-', lw = 2, c = 'green', label = 'cos(x)')
  plt.legend(loc = 'upper right')		## 图例位置
  plt.grid(True, axis = 'both',ls = ':', c = 'y')	## 网格
  plt.axhline(y = 1.0, ls = '--', lw = 2, c = 'r')	## y=1.0位置水平线
  plt.axhline(y = -1.0, ls = '--', lw = 2, c = 'r')	## y=-1.0位置水平线
  plt.axvspan(xmin = 0.0, xmax = np.pi/2, color = 'grey', alpha = 0.4)	## [0.0, pi/2]竖直矩形
  plt.text(1.3, 0.3, s= 'cos(x)', weight = 'bold', color = 'green')		## (1.3,0.3)位置文本cos(x)
  plt.text(2.8, 0.3, s= 'sin(x)', weight = 'bold', color = 'blue')
  plt.annotate(text ="(0,1)", xy = (0,1), xytext = (1,1.5), weight = 'bold', color = 'red', arrowprops = dict(arrowstyle = '-|>', color = 'black',connectionstyle = 'arc3,rad = 0.5'))	## 箭头文本
  plt.show()
  ```




---

# 2. 基础统计函数和图形

### 设置可显示中文+处理坐标轴刻度为负数情况

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
mpl.rcParams["font.sans-serif"]=["SimHei"]	## 设置字体为SimHei，从而可以显示中文
mpl.rcParams["axes.unicode_minus"]=False	## 不使用默认的“Unicode minus”模式来处理坐标轴轴线的刻度标签是负数的情况，一般可以使用“ASCII hyphen”模式来处理坐标轴轴线的负刻度值的情况	
```

### 柱状图

```python
plt.bar(x,y,align="center",color="blue",width= 3, edgecolor = 'red', tick_label=["A","B","C","D","E"],alpha=0.6,hatch='///',label = '数据集1')
```

> x——可以是**数值**也可以是**字符串**
>
> ```python
> x = ['c', 'a', 'd', 'b']
> y = [1, 2, 3, 4]
> ## 此时柱状图会自动将x轴标签按照字符串顺序排序变为：a、b、c、d
> ```
>
> 也可以不用x用另外设定的**x轴标签**——**plt.xticks或tick_label**
>
> ```python
> plt.xticks(np.arange(4), ('A','B', 'C', 'D'), rotation=30) #rotation控制倾斜角度
> ## 此时横轴标签变为：A、B、C、D
> ```
>
> ```python
> ## tick_label直接作为plt.bar的参数
> ```
>
> **y轴刻度间距**——plt.yticks
>
> ```python
> plt.yticks(np.arange(0, 5, 0.2))	## 0~5，每隔0.2一个刻度
> ```
>
> **align**——柱体对齐方式：align='center'表示柱体中心点放在列表x的元素值处
>
> **edgecolor**——每个bar的边框颜色
>
> **hatch**——每个bar的填充图案以及密集程度
>
> > 关键字参数hatch可以有很多取值，例如**`'//'`、`'\\'`、`'|'`、`'-'`（斜线、竖线、横线）等**，每种符号字符串都是一种填充柱体的几何样式。而且，**符号字符串的符号数量越多**，柱体的几何图形的密集程度越高

### 条形图

x的值显示在y轴上，y的值显示在x轴上，tick_label是y轴标签

```python
plt.barh(x,y,align="center",color="c",tick_label=["A","B","C","D","E"], label = '班级A')
```

### 堆积柱状图

```python
## 设定bar()的bottom参数作为上方一个条形的底
plt.bar(x, y, align = 'center', color = 'blue', bottom = y1, label = '班级B');
```

![image-20220214110747882](https://s2.loli.net/2022/02/14/8vGInicK4ArYhqz.png)



### 堆积条形图

```python
## 设定barh的Left参数作为右侧的左边界
plt.barh(x,y,align="center",left=y1,color="#8da0cb",label="班级B")
```

![image-20220214111139131](https://s2.loli.net/2022/02/14/54mc1fnUE89aXbt.png)



### 并列柱状图和并列条形图

**并列的bar的x1坐标：x1 = x+bar_width**

```python
bar_width = 0.35
plt.bar(x,y,bar_width,color="c",align="center",label="班级A",alpha=0.5)
plt.bar(x+bar_width,y1,bar_width,color="b",align="center",label="　班　级B",alpha=0.5)
```

**刻度的横坐标x_ticks = x + bar_width/2**

```python
plt.xticks(x+bar_width/2,tick_label)
```

**demo**

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np
mpl.rcParams["font.sans-serif"]=["SimHei"]
mpl.rcParams["axes.unicode_minus"]=False
# some simple data
x = np.arange(5)
y = [6,10,4,5,1]
y1 = [2,6,3,8,5]
bar_width = 0.35
tick_label=["A","B","C","D","E"]
# create bar
plt.bar(x,y,bar_width,color="c",align="center",label="班级A",alpha=0.5)
plt.bar(x+bar_width,y1,bar_width,color="b",align="center",label="　班　级B",alpha=0.5)
# set x,y_axis label
plt.xlabel("测试难度")
plt.ylabel("试卷份数")
# set xaxis ticks and ticklabels
plt.xticks(x+bar_width/2,tick_label)
plt.legend()
plt.show()
```

### 直方图

和柱状图的区别：柱状图描述离散数据的分布，直方图描述连续数据的分布。图形上，柱状图每个bar间有间隙，直方图没有。

使用场景：展现数据在不同区间的分布特征

```python
bins = range(0,101,10)	#[0,101)间分10个区间
plt.hist(x,bins=bins,facecolor="#377eb8",edgecolor = 'black',rwidth=10,histtype = 'bar', orientation = 'vertical',stacked = False, density = False)
```

> **bins——bar分布的区间**
>
> facecolor——直方图颜色
>
> edgecolor——bar边框颜色
>
> **rwidth——**
>
> **histtype——直方图类型：‘bar’, ‘barstacked’, ‘step’, ‘stepfilled’**
>
> > bar——柱状
> >
> > step——折线（只有边框线条）
> >
> > ![image-20220214115913591](https://s2.loli.net/2022/02/14/W3bwujSafGo9ZYE.png)
> >
> > stepfilled
> >
> > ![image-20220214135209099](https://s2.loli.net/2022/02/14/BKebqpORliXWkAM.png)
>
> 
>
> **orientation——直方图的方向：默认vertical竖直、horizontal水平**
>
> **stacked——True绘制堆积直方图，False绘制并列直方图**
>
> **density——默认值为False, 表示用每个区间的数值个数来绘图，当取值为True时，柱子的高度为每个区间的频率**
>
> 

### 饼图

```python
labels = [] 	## 每个饼片的文字标签
vals = [0.15,0.35,0.25,0.25]		## 饼片的值（和为1）
colors = ["#377eb8","#4daf4a","#984ea3","#ff7f00"]		## 饼片的颜色
explode = (0.1,0,0,0)	## 饼片突出的程度
plt.pie(vals, explode = explode, labels = labels, autopct = "%3.1f%%", colors = colors, shadow = True)
```

> vals——饼图取值
>
> **explode——饼片分裂的程度。explode=None饼片不分裂。**
>
> labels——各饼片的标签
>
> **autopct——将各饼片对应vals的取值转成百分比**
>
> colors——各饼片的颜色
>
> shadow——饼片是否有阴影

### 内嵌环形饼图















