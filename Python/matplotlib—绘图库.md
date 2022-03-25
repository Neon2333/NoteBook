# [官网文档](https://matplotlib.org/stable/tutorials/)

# 1. matplotlib绘图的组成元素

https://zhuanlan.zhihu.com/p/231994155

![image-20220216090236653](https://s2.loli.net/2022/02/16/XTMRE4awAvjzZtS.png)



![image-20220216090311314](https://s2.loli.net/2022/02/16/7i9CwFNBE8yp5kI.png)

### figure——窗口（画布）

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

### Axes——图形（所有图形元素都是Axes的实例）

### title——标题

### xlabel——横轴标签

### ylabel——纵轴标签

### xlim——横轴范围

```python
plt.xlim(xmin, xmax)
```

### ylim——纵轴范围

### **坐标轴范围还可用axis设定**

```python
plt.axis(xmin, xmax, ymin, ymax)
```

### plot——曲线

```python
plt.plot(x,y,ls="-.",lw=2,c="c",label="plot figure")
```

> ls——线型
>
> ![](https://s2.loli.net/2022/02/28/XGAlNM3wvbBFt1m.png)
>
> lw——线宽
>
> c——颜色
>
> **label——该曲线的标签（显示在legend中），若在legend中指定了labels，该label会被覆盖。**

```python
import matplotlib.pyplot as plt

a = np.linspace(0, 2 * np.pi, 50)
b = np.sin(a)
plt.plot(a,b)
mask = b >= 0			
plt.plot(a[mask], b[mask], 'bo')		## 绘制满足条件mask的图
mask = (b >= 0) & (a <= np.pi / 2)
plt.plot(a[mask], b[mask], 'go')		
plt.show()
```

![image-20220228151527836](C:/Users/eivision/AppData/Roaming/Typora/typora-user-images/image-20220228151527836.png)



### legend——**图例**

先plot()后legend()

```python 
plt.legend(wedges, labels, loc = 'best', bbox_to_anchor = (num1, num2, num3, num4), ncol = 2, title = '图例标题', edgecolor = 'black', facecolor = 'pink', shadow = True, fancybox = True)
```

> **wedges——饼片实例列表。就可以将饼片外部的文本标签放在图例中，而饼片的数值标签仍然放在饼片内部**
>
> **labels——文本标签。若缺省则显示pie等绘图函数中的labels，优先级大于plot中的label。**
>
> > 可用matplotlib自带TeX功能实现对数学表达式的编辑
> >
> > ```python
> > plt.legend(x, y, label = r'$sin(x)$')
> > ## r表示使用TeX解析字符串
> > ## 两个$$之间的字符串将以斜体输出
> > ```
>
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
> bbox_to_anchor——是一个四元元组，且使用Axes坐标系统。也就是说，第1个元素代表距离画布左侧的**x轴长度的倍数**的距离；第2个元素代表距离画布底部的**y轴长度的倍数**的距离；第3个元素代表x轴长度的倍数的**线框长度**；第4个元素代表y轴长度的倍数的**线框宽度**。
>
> ncol——设定图例条目的列数
>
> title——图例的标题
>
> edgecolor——图例边框颜色
>
> facecolor——图例填充颜色

### grid——网格线

```python
plt.grid(True, which = 'major', axis = 'both',ls = ':', lw = 1, c = 'y')
```

> True/None——设置是否显示网格。设置None时若给了其他参数，则None失效
>
> which——'major'显示主刻度线，'minor'显示次刻度线
>
> axis——以哪个坐标轴的刻度生成网格

### marker——（标记）点的形状

```python
plt.plot(x, y, color='r', marker='o', linestyle='dashed')		## 点形状为圆圈标记circle marker
```

![image-20220228113711329](https://s2.loli.net/2022/02/28/aMgyS5v76XAGkln.png)

```python
ax[0,0].scatter(x,y*1.5,marker=r"$\clubsuit$",c="#fb8072",s=500)
ax[0,1].scatter(x,y-2,marker=r"$\heartsuit$",s=500)
ax[1,0].scatter(x,y+7,marker=r"$\diamondsuit$",s=500)
ax[1,1].scatter(x,y-9,marker=r"$\spadesuit$",c="#8dd3c7",s=500)
```

![image-20220228135616301](https://s2.loli.net/2022/02/28/zA8yWs7UCXbdvkc.png)



### text——注释文本

在图形的指定位置添加文本，用于对图形进行说明

```python
plt.text(2.8, 0.3, s= 'sin(x)', weight = 'bold', color = 'blue')
```

### [annotate](https://zhuanlan.zhihu.com/p/32501335)——箭头指向+注释文本

```python
from pylab import *
plt.annotate(text ="(0,1)", xy = (0,1), xytext = (1,1.5), weight = 'bold', color = 'red', arrowprops = dict(arrowstyle = '-|>', color = 'black', connectionstyle = 'arc3, rad = 0.5'), bbox=dict(boxstyle='round,pad=0.5', facecolor='yellow', edgecolor='k', lw=1, alpha=0.4))
```

> * **xy——箭头起点坐标**
>
> * **xytext——文本坐标**
>
> * annotate中最重要的控制，全部由**arrowprops**这个变量完成，它是一个字典类型，可以控制箭头的诸多属性
>
> * **bbox——text的bbox属性，注释文本的外框**
>
> * **connectionstyle——弯曲箭头arc3、angle3**
>
>   ```python
>    connectionstyle = 'arc3, rad = 0.5'
>   ```
> 
>   ![preview](https://s2.loli.net/2022/02/13/fqDcKE9NbenaBoP.png)
>
>   ```python
>    connectionstyle = 'angle3, angleA=-90,angleB=0'
>   ```
> 
>   ![preview](https://s2.loli.net/2022/02/13/pMTfaXEHFLDZYur.png)

### [axhline](https://matplotlib.org/stable/api/_as_gen/matplotlib.axes.Axes.axhline.html)——水平参考线

```python
Axes.axhline(y=0, xmin=0, xmax=1, color='green' , linestyle='--') ##省略xmin和xmax时为横穿整个坐标轴的线
```

### axvline——竖直参考线

```python
Axes.axvline(x=0, ymin=0, ymax=1, color='green' , linestyle='--') ##省略ymin和ymax时为横穿整个坐标轴的线
```

### axhspan——水平矩形区域

**color不能省略为c**

```python
axhspan(xmin = 0.0, xmax = 1.0, ymin, ymax, color, alpha)	##水平矩形的ymin和ymax不能缺省。x缺省时矩形横穿整个坐标轴
```

### axvspan——竖直矩形区域

```python
axvspan(xmin, xmax, ymin, ymax, color, alpha)	##竖直矩形的xmin和xmax不能缺省
```

![image-20220213112408640](https://s2.loli.net/2022/02/13/ol1XvSZqz8VNi7x.png)

### 颜色代码

![img](https://s2.loli.net/2022/02/15/cbfGKRd2vDBWq9w.png)

### [填充区域](https://blog.csdn.net/HHG20171226/article/details/101650909?spm=1001.2101.3001.6650.5&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-5.pc_relevant_paycolumn_v3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-5.pc_relevant_paycolumn_v3&utm_relevant_index=10)

#### fill

和plot功能类似，可绘图

```python
import numpy as np
import matplotlib.pyplot as plt
x = np.linspace(0, 5 * np.pi, 1000)
y1 = np.sin(x)
y2 = np.sin(2 * x)

plt.fill(x, y1, color = 'darkorange', alpha = 0.4, label="$ y = sin(x) $")
plt.fill(x, y2, color = 'violet', alpha = 0.4, label="$ y = sin(2x) $")

plt.legend(loc='best')
plt.show()
```

![image-20220216143314887](https://s2.loli.net/2022/02/16/qcCAV29UbRxZTM1.png)

#### fill_between

作用是填充两条水平曲线`y=f(x)`之间的区域

**简单的填充所围区域：x规定横轴范围，y1和y2之间所围。**

```python
plt.fill_between(x, y1, y2 = 0, where = None)
```

> x——横轴范围，必须参数
>
> **y1、y2——两条水平曲线。可以是数值，数值就是一条水平线**
>
> where——条件，去掉一些区域

```python
x = np.linspace(-2*np.pi, 2*np.pi, 100)
y1 = np.sin(x)
y2 = np.cos(x)
plt.plot((x.min(), x.max()), (0, 0))	## 绘制x=0水平线
plt.plot(x, y1, color = 'blue', label = '$sin(x)$')
plt.plot(x ,y2, color = 'red', label = '$cos(x)$')
# plt.fill_between(x, y2, y1, where = (x>0)&(x<np.pi/2), color = 'darkorange', alpha = 0.4)
plt.fill_between(x, y1, y2, where = (y1<y2), color = 'violet', alpha = 0.4)
plt.legend(loc = 'best')
plt.show()
```

![image-20220216152809781](https://s2.loli.net/2022/02/16/FObPjABkyhmLKN6.png)

```python
x = np.linspace(-2*np.pi, 2*np.pi, 100)
y1 = np.sin(x)
y2 = np.cos(x)
plt.plot((x.min(), x.max()), (0, 0))
plt.plot(x, y1, color = 'blue', label = '$sin(x)$')
plt.plot(x ,y2, color = 'red', label = '$cos(x)$')
plt.fill_between(x, y2, y1, where = (x>0)&(x<np.pi/2), color = 'darkorange', alpha = 0.4)
# plt.fill_between(x, y1, y2, where = (y1<y2), color = 'violet', alpha = 0.4)
plt.legend(loc = 'best')
plt.show()
```

![image-20220216152914036](https://s2.loli.net/2022/02/16/vVAeZrdl5NPMUTj.png)

```python
x = np.linspace(-2*np.pi, 2*np.pi, 100)
y1 = np.sin(x)
y2 = np.cos(x)
plt.plot((x.min(), x.max()), (0, 0))
plt.plot(x, y1, color = 'blue', label = '$sin(x)$')
plt.plot(x ,y2, color = 'red', label = '$cos(x)$')
# plt.fill_between(x, y2, y1, where = (x>0)&(x<np.pi/2), color = 'darkorange', alpha = 0.4)
# plt.fill_between(x, y1, y2, where = (y1<y2), color = 'violet', alpha = 0.4)
plt.fill_between(x, y1, 0, where = (x>0)&(x<2*np.pi), color = 'violet', alpha = 0.5)
plt.legend(loc = 'best')
plt.show()
```

![image-20220216153149026](https://s2.loli.net/2022/02/16/pVKIlfHyq3xtFTJ.png)

```python
x = np.linspace(-2*np.pi, 2*np.pi, 100)
y1 = np.sin(x)
y2 = np.cos(x)
plt.plot((x.min(), x.max()), (0, 0))
plt.plot(x, y1, color = 'blue', label = '$sin(x)$')
plt.plot(x ,y2, color = 'red', label = '$cos(x)$')
# plt.fill_between(x, y2, y1, where = (x>0)&(x<np.pi/2), color = 'darkorange', alpha = 0.4)
# plt.fill_between(x, y1, y2, where = (y1<y2), color = 'violet', alpha = 0.4)
plt.fill_between(x, y1, 0.5, where = (x>0)&(x<np.pi), color = 'violet', alpha = 0.5)
plt.legend(loc = 'best')
plt.show()
```

![image-20220216153635452](https://s2.loli.net/2022/02/16/SMuAmTvrI8HLK9E.png)

```python
x = np.linspace(-2*np.pi, 2*np.pi, 100)
y1 = np.sin(x)
y2 = np.cos(x)
plt.plot((x.min(), x.max()), (0, 0))
plt.plot(x, y1, color = 'blue', label = '$sin(x)$')
plt.plot(x ,y2, color = 'red', label = '$cos(x)$')
# plt.fill_between(x, y2, y1, where = (x>0)&(x<np.pi/2), color = 'darkorange', alpha = 0.4)
# plt.fill_between(x, y1, y2, where = (y1<y2), color = 'violet', alpha = 0.4)
plt.fill_between(x, y1, 0.5, where = (y1>0.5)&(x>0)&(x<np.pi), color = 'violet', alpha = 0.5)
plt.legend(loc = 'best')
plt.show()
```

![image-20220216153726394](https://s2.loli.net/2022/02/16/TBlArND3MvGydtq.png)



#### fill_betweenx

填充两条垂直曲线`x=f(y)`之间的区域

```python
import matplotlib.pyplot as plt
import numpy as np
y = np.arange(0.0, 2, 0.01)
x1 = np.sin(2 * np.pi * y)
x2 = 1.2 * np.sin(4 * np.pi * y)

fig, [ax1, ax2, ax3] = plt.subplots(1, 3, sharey=True, figsize=(6, 6))

ax1.fill_betweenx(y, 0, x1)
ax1.set_title('between (x1, 0)')

ax2.fill_betweenx(y, x1, 1)
ax2.set_title('between (x1, 1)')
ax2.set_xlabel('x')

ax3.fill_betweenx(y, x1, x2)
ax3.set_title('between (x1, x2)')
```

![image-20220216154019845](https://s2.loli.net/2022/02/16/Oq9bW3XY1Feg2al.png)





### 基本plot流程

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
> plt.xticks(np.arange(4), ('A','B', 'C', 'D'), rotation=30) #np.arange指定刻度位置ticks，rotation控制倾斜角度
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

https://blog.csdn.net/captain811/article/details/79248912?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ELandingCtr%7EHighlightScore-4.queryctrv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ELandingCtr%7EHighlightScore-4.queryctrv2

https://zhuanlan.zhihu.com/p/38160291

饼图通过将一个圆饼按照分类的占比划分成多个区块，整个圆饼代表数据的总量，每个区块（圆弧）表示该分类占总体的比例大小，所有区块（圆弧）的加和等于 100%。

```python
labels = ["面粉","砂糖","奶油","草莓酱","坚果"] 	## 每个饼片的文字标签
weight = [0.15,0.35,0.25,0.25]		## 饼片的权重（和为1）
colors = ["#377eb8","#4daf4a","#984ea3","#ff7f00"]		## 饼片的颜色
explode = (0.1,0,0,0)	## 饼片突出的程度

wedges, texts, autotexts = plt.pie(weight, labels = labels, explode = explode, autopct = "%3.1f%%", colors = colors, shadow = True, startangle = 45, radius = 0.7, labeldistance = 1.1, pctdistance = 0.7, textprops = dict('fontsize' = 13, 'color' = 'black'), wedgeprops = {'linewidth':0.3, 'edgecolor':'white'}, frame = True, center = (1,1))

## 图例函数“plt.legend(wedges,elements)”，我们就可以将饼片外部的文本标签放在图例中，而饼片的数值标签仍然放在饼片内部。函数legend()的参数wedges和elements分别表示饼片实例列表和文本标签列表
plt.legend(wedges,weight,fontsize=12,title="配料表",loc="center left",bbox_to_anchor=(0.91, 0, 0.3, 1))

plt.axis('equal')	## 让显示的饼图保持圆形，需要添加axis保证长宽一样
```

> weight——饼图饼片权重值
>
> **explode——饼片分裂的程度。explode=None饼片不分裂。**
>
> labels——各饼片的标签
>
> **autopct——将各饼片对应weight的取值转成百分比**
>
> colors——各饼片的颜色
>
> shadow——饼片是否有阴影
>
> startangle——第一个饼片的起始角度是以x轴为起点逆时针旋转45°的
>
> radius——饼图半径大小
>
> labeldistance——设置各**扇形标签（图例）**与圆心的距离，半径的倍数
>
> **pctdistance——百分比标签距离圆心的距离，半径的倍数**
>
> **textprops——设置饼图中文本的属性，如字体大小、颜色等**
>
> **wedgeprops——设置饼图内外边界的属性，如边界线的粗细、颜色等**
>
> ```python
> wedgeprops= {'lw':1, 'edgecolor':'w'}
> ```
>
> frame——设置是否显示饼图背后的图框。如果设置为True的话，需要同时控制图框x轴、y轴的范围和饼图的中心位置
>
> > center = (1,1)
> >
> > 
>
> center——饼图中心位置。默认是原点

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
mpl.rcParams["font.sans-serif"]=["SimHei"]
mpl.rcParams["axes.unicode_minus"]=False
elements = ["面粉","砂糖","奶油","草莓酱","坚果"]
weight = [40,15,20,10,15]
colors = ["#1b9e77","#d95f02","#7570b3","#66a61e","#e6ab02"]
wedges, texts, autotexts = plt.pie(weight,autopct="%3.1f%%",textprops=dict(color="w"),colors=colors)
## 函数legend()的参数wedges和elements分别表示饼片实例列表和文本标签列表，而且这两个参数要一起配合使用才可以将饼片外部的文本标签放置在图例内部
plt.legend(wedges,elements,fontsize=12,title="配料表",loc="center left",bbox_to_anchor=(0.91, 0, 0.3, 1))	
plt.setp(autotexts, size=15, weight="bold")
plt.setp(texts,size=12)
plt.title("果酱面包配料比例表")
plt.show()
```

### 内嵌环形饼图

将多个饼图进行嵌套，从而实现内嵌环形饼图的可视化效果。这样，就可以进行多组定性数据比例分布的比较。

由多个饼图叠加

绘制**外层饼图、内层饼图、中心空白区域、设置饼图边界线颜色、粗细**

```python
## 嵌套饼图
import matplotlib as mpl
import matplotlib.pyplot as plt
fig = plt.figure(figsize=(12,8), facecolor = 'cornsilk')
mpl.rcParams["font.sans-serif"]=["SimHei"]
mpl.rcParams["axes.unicode_minus"]=False
labels1 = "A难度水平","B难度水平","C难度水平","D难度水平"
labels2 = ['A', 'B', 'C', 'D']

students1 = [0.35,0.15,0.20,0.30]
colors1 = ['orange', 'limegreen', 'blue', 'violet']
explode = (0,0,0,0)

# 绘制外层饼图
wedges1, texts1, autotexts1 = plt.pie(students1,explode=explode,
labels=labels1,
autopct="%3.1f%%",
startangle=45,
shadow=False,
colors=colors1,pctdistance = 0.85, labeldistance = 1.1,radius = 0.7, wedgeprops= {'lw':1, 'edgecolor':'w'})

# 绘制内层饼图
students2 = [0.3, 0.2, 0.25, 0.25]
colors2 = ['orange', 'limegreen', 'blue', 'violet']
wedges2, texts2, autotexts2 = plt.pie(students2,explode=explode,labels=labels2,autopct="%3.1f%%",startangle=45,shadow=False,colors=colors2,pctdistance = 0.85, labeldistance = 1.1, radius = 0.5, wedgeprops= {'lw':1, 'edgecolor':'w'})	## wedgeprops设置边界线颜色才可将饼图区分开

# 绘制中心空白饼图
plt.pie(x=[1], colors = 'w', radius = 0.3)

plt.legend(wedges1, labels1, loc = 'upper right', title = '外环', edgecolor = 'red', facecolor = 'pink', fontsize =10, ncol = 2)
# plt.legend(wedges2, labels2, loc = 'lower right', title = '内环', edgecolor = 'red', facecolor = 'pink', fontsize =10)

plt.title("title", style = 'oblique', size = 'xx-large', color = 'c', family="Comic Sans MS")

plt.table([students1, students2], cellLoc='center', colWidths=[0.4, 0.4, 0.4, 0.4], colLabels=labels1, colColours=colors1, rowLabels=['外环数据', '内环数据'], rowLoc='center', loc = 'bottom')

plt.show()
```

![image-20220216104128160](https://s2.loli.net/2022/02/16/aC7DREPqnBsJd64.png)

### 箱线图

* 四分位数

  是统计学中分位数的一种，即把所有[数值](https://baike.baidu.com/item/数值/2013853)由小到大[排列](https://baike.baidu.com/item/排列/7804523)并分成四等份，处于三个分割点[位置](https://baike.baidu.com/item/位置/36166)的数值就是四分位数。第三四分位数与第一四分位数的[差距](https://baike.baidu.com/item/差距/1855729)又称四分位距IQR（IQR = Q3 - Q1）。与[方差](https://baike.baidu.com/item/方差)、[标准差](https://baike.baidu.com/item/标准差)一样，**表示统计资料中各变量分散情形**。

  - **第一四分位数**(Q1)，又称“较小四分位数”，等于该样本中所有数值由小到大排列后第25%的数字。
  - **第二四分位数**(Q2)，又称“[中位数](https://baike.baidu.com/item/中位数)”，等于该样本中所有数值由小到大排列后第50%的数字。
  - **第三四分位数**(Q3)，又称“较大四分位数”，等于该样本中所有数值由小到大排列后第75%的数字。

* 四分位距

  QD = IQR / 2

* 组成

  箱体的组成部分有：箱体、箱须和离群值，其中，箱体主要由第一四分位数、中位数和第三四分位数组成，箱须又分为上箱须和下箱须。

  上箱须 = Q3 + whis * IQR

  下箱须 = Q1 - whis * IRQ

  在上箱须、下箱须范围以外的数据为离群值

  ![image-20220216112114255](https://s2.loli.net/2022/02/16/EbOY8kyDqXSguPF.png)

```python
bplot = plt.boxplot(testList,whis=whis,
widths=width,
sym="o",
labels=labels,
patch_artist=True,
vert = True, showfliers = True, notch = False)
```

> **whis——箱须位置（数据离群范围）**
>
> widths——箱体宽度
>
> sym——离群数据点表示图形
>
> **patch_artist——是否给箱体上色（要给箱体上色，需要设为True）**
>
> vert——箱体方向：True为竖向，False为横向
>
> showfliers——是否显示离群值
>
> notch——箱体是否V型。True时为V型。

```python
## 我们要对箱线图的返回值进行操作，这个返回值是一个字典数据结构，由于需要对箱体添加颜色，所以使用键“boxes”来调出键值“bplot["boxes"]”。最后，使用内置函数zip()生成元组列表zip(bplot["boxes"],colors)，使用for循环对每个箱体进行颜色填充
colors = ["#1b9e77","#d95f02"]
for patch,color in zip(bplot["boxes"],colors):
patch.set_facecolor(color)
```

```python
## 竖向箱线图
import matplotlib as mpl
mpl.rcParams["font.sans-serif"]=["FangSong"]
mpl.rcParams["axes.unicode_minus"]=False
testA = np.random.randn(5000)
testB = np.random.randn(5000)
testList = [testA,testB]
labels = ["随机数生成器AlphaRM","随机数生成器BetaRM"]
colors = ["#1b9e77","#d95f02"]
whis = 1.6
width = 0.35
bplot = plt.boxplot(testList, whis=whis, widths=width, sym="+", labels=labels, patch_artist=True,vert = True, showfliers = True)
for patch,color in zip(bplot["boxes"],colors):
    patch.set_facecolor(color)
plt.ylabel("随机数值")
plt.title("生成器抗干扰能力的稳定性比较")
plt.grid(axis="y",ls=":",lw=1,color="gray",alpha=0.4)
plt.show()
```

```python
## 横向箱线图
import matplotlib as mpl
mpl.rcParams["font.sans-serif"]=["FangSong"]
mpl.rcParams["axes.unicode_minus"]=False
testA = np.random.randn(5000)
testB = np.random.randn(5000)
testList = [testA,testB]
labels = ["随机数生成器AlphaRM","随机数生成器BetaRM"]
colors = ["#1b9e77","#d95f02"]
whis = 1.6
width = 0.35
bplot = plt.boxplot(testList, whis=whis, widths=width, sym="+", patch_artist=True,vert = False, showfliers = True)
for patch,color in zip(bplot["boxes"],colors):
    patch.set_facecolor(color)
plt.xlabel("随机数值")
plt.yticks(ticks= [1, 2], labels= labels, rotation = 90)	## 这里不把labels放到boxplot中，因为文字方向无法旋转。所以用yticks手动设置刻度标签
plt.title("生成器抗干扰能力的稳定性比较")
plt.grid(axis="y",ls=":",lw=1,color="gray",alpha=0.4)
plt.show()
```

![image-20220216134222345](https://s2.loli.net/2022/02/16/rV58WEjflpM7gJF.png)

![image-20220216134254370](https://s2.loli.net/2022/02/16/2tkam3RXJH4UpVf.png)



### 误差棒图



---

# 3. 图例、刻度

### 图例标签斜体

可用matplotlib自带TeX功能实现对数学表达式的编辑

```python
plt.legend(x, y, label = r'$sin(x)$')
## r表示使用TeX解析字符串
## 两个$$之间的字符串将以斜体输出
```

### 图例线框阴影

```python
plt.legend(x, y, label = r'$sin(x)$', shadow = True)
```

### 图例线框圆角、直角

```python
plt.legend(x, y, label = r'$sin(x)$', shadow = True, fancybox = True)
```

### 标题

```python
plt.title("title", loc = 'left', style = 'oblique', size = 'xx-large', color = 'c', family="Comic Sans MS")
```

> loc——'left'、'right'、'center'
>
> style——字体风格
>
> family——字体类别

---

## 4. 画布figure上添加表格

```python
plt.table(cellText, cellLoc="center", colWidths=[0.1]*4, colLabels=colLabels, colColours=colColors, rowLabels=rowLabels, rowLoc="center", loc="bottom")
```

> cellText——表格中文本
>
> cellLoc——表格中文本在表格中的位置
>
> colWidths——列宽
>
> colLabels——列名称
>
> colColours——列名称颜色
>
> rowlabels——行名称
>
> rowLoc——行名称文本在表格中的位置
>
> loc——表格在figure中的位置

```python
plt.table([students1, students2], cellLoc='left', colWidths=[0.4, 0.4, 0.4, 0.4], colLabels=labels1, colColours=colors1, rowLabels=['外环数据', '内环数据'], rowColours=['orange', 'limegreen'], rowLoc='right', loc = 'bottom')

```

![image-20220216104054745](https://s2.loli.net/2022/02/16/yibIABEQUTKls7m.png)

---

## 5. 坐标轴位置

在matplotlib的图中，默认有四个轴（**图的4个边框**），两个横轴（“top”、“bottom”）和两个竖轴（“left”、“right”），可以通过`ax = plt.gca()`方法获取，gca是“get current axes”的缩写。

**先将显示的坐标图的上边框和右边框去掉，即设置它们的显示方式为不显示：**

```python
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')
```

**将坐标图的下边框和左边框作为坐标系的x轴和y轴，并调整坐标轴的位置:**

```python
ax.spines['bottom'].set_position(('data',0))	## 表示将x轴设置在y=0处。
ax.spines['bottom'].set_position(('axes',0.5))	## 表示将x轴设置在y轴范围的50%处。
```

> **“data”表示通过值来设置坐标轴的位置，“axes”表示以百分比的形式设置轴的位置。**

**demo**

```python
fig,axx = plt.subplots(1,2)

x1 = np.linspace(-2*np.pi, 2*np.pi, 100)
y1 = np.sin(x1)
y2 = np.cos(x1)
ax=axx[0]
ax.set_xlim(-2*np.pi, 2*np.pi)
ax.spines['top'].set_color('none')
ax.spines['right'].set_color('none')
ax.spines['bottom'].set_position(('data', 0))
ax.spines['left'].set_position(('data', 0))
ax.plot(x1, y1)
ax.plot(x1, y2)

bx=axx[1]
x2 = np.linspace(-2*np.pi, 2*np.pi, 100)
y3 = np.tan(x2)
y4 = np.arctan(x2)
bx.set_xlim(-2*np.pi, 2*np.pi)
bx.spines['top'].set_color('none')
bx.spines['right'].set_color('none')
bx.spines['bottom'].set_position(('data', 0))
bx.spines['left'].set_position(('data', 0))
bx.plot(x2, y3)
bx.plot(x2, y4)

plt.show()
```

![image-20220228134710420](https://s2.loli.net/2022/02/28/RePYkDzOWJ4mnhL.png)





---

## 6. 坐标轴刻度

语句“ax.tick_params()”是通过调用实例ax的实例方法进行刻度样式设置的。同时，通过调用模块pyplot中的函数也可以实现刻度样式的设置工作。具体而言，模块pyplot中的刻度样式的设置是通过函数tick_params()实现的，即可以执行语句“plt.tick_params()”来进行刻度样式的设置。前者是matplotlib的面向对象的操作方法，后者是调用模块pyplot的API的操作方法，这是两种不同思想的操作模式，虽然使用pyplot模块绘制图表非常方便，但是要想使图表有更多的调整和定制化展示，还是应该使用matplotlib的面向对象的操作方法。

### 使用Matplotlib的面向对象方法

> **figure——画布实例**
>
> **axes——一个图表实例**
>
> **xaxis——x轴实例，yaxis——y轴实例**
>
> **add_axes()——添加子axes，参数是一个坐标轴位置和大小的四元列表**
>
> **fig.add_subplot(111)——分割画布，添加子axes**
>
> **MultipleLocator(val)——一格为一个val（主副刻度都可用）**
>
> **AutoMinorLocator(n)——设置副刻度，按照将主刻度一格分为几格副刻度划分**
>
> **ax.set_xlim——X坐标轴范围**
>
> **ax.set_title( 'title test',fontsize=12,color='r')——设置标题**
>
> **set_xlabel——X坐标轴标签**
>
> **ax.xaxis.set_major_locator——主刻度位置**
>
> **ax.xaxis.set_minor_locator——副刻度位置**
>
> **ax.xaxis.set_ticks_position——设置刻度相对于坐标轴的位置**
>
> **ax.xaxis.set_minor_formatter——副刻度格式**
>
> **ax.tick_params——刻度样式**
>
> **ax.set_xticklabels——刻度是文字**
>
> 

```python
from matplotlib.ticker import MultipleLocator, AutoMinorLocator, FormatStrFormatter
## 画布未分割
plt.figure(figsize = (7,6),dpi = 100)
ax = plt.gca()	## 获取坐标轴
## 画布分割
fig = plt.figure(figsize=(8,8))
ax = fig.add_subplot(111) 
```

```python
## 设置坐标轴范围
ax.set_xlim(0, 10)
ax.set_ylim(0, 100)
```

```python
## 坐标轴标签

## boxstyle用于给xlabel和ylabel加文本框
ax.set_xlabel("我是X轴",fontsize = 14,color = 'b',alpha = 0.7,bbox=dict(boxstyle='round,pad=0.5', fc='yellow', ec='blue',lw=1 ,alpha=0.7)) 
ax.set_ylabel("我是Y轴",fontsize = 14,color = 'b',alpha = 0.7,bbox=dict(boxstyle='round,pad=0.5', fc='yellow', ec='blue',lw=1 ,alpha=0.7))
```

```python
## 设置主刻度位置
ax.xaxis.set_major_locator(MultipleLocator(1.0))	## MultipleLocator(1.0)表示刻度间隔1.0
ax.yaxis.set_major_locator(MultipleLocator(1.0))

## 设置副刻度位置
ax.xaxis.set_minor_locator(MultipleLocator(0.25))	## 副刻度一格0.25
ax.yaxis.set_minor_locator(AutoMinorLocator(4))		## 自动设置副刻度，一格分为4小格
```

```python
## 副刻度刻度格式
## 主刻度自动有数值标注，但副刻度需要手动设置数值格式
ax.xaxis.set_minor_formatter(FormatStrFormatter('%0.2f'))		## 小数点后两位float
```

```python
## 刻度线样式：线宽、线长、颜色

## 主刻度
ax.tick_params("x",which='major',length=15,width=2.0,colors="r",direction = 'in')   ## direction指定刻度线朝向内还是外，in; out; inout
ax.tick_params("y",which='major',length=5,width=1.0,colors="r",direction = 'in')
ax.tick_params(which='major',length=15,width=2.0,colors="r",direction = 'in')	## 省略指定具体的x/y轴，则同时设置2个坐标轴

## 副刻度
ax.tick_params(which='minor',length=5,width=1.0,labelsize=6, labelcolor='0.25',direction = 'inout')	## 同时设置x/y轴的副刻度
```

```python
## 设定刻度标签是文字
ax.set_xticklabels(['A','B','C','D','E','F','G'])
ax.set_yticklabels(['鉴','图','化','视','可','注','关'],family = 'SimHei',fontsize = 14)

## 刻度标签样式：颜色、字体、旋转
for ticklabel in ax.xaxis.get_ticklabels():
ticklabel.set_color("slateblue")
ticklabel.set_fontsize(18)
ticklabel.set_rotation(30)

for ticklabel in ax.yaxis.get_ticklabels():
ticklabel.set_color("slateblue")
ticklabel.set_fontsize(18)
ticklabel.set_rotation(30)

## 刻度标签相对于坐标轴的位置
ax.xaxis.set_ticks_position("bottom")		## x轴刻度在x轴下方
ax.yaxis.set_ticks_position("left")			## y轴刻度在y轴左侧
```

```python
from matplotlib.ticker import MultipleLocator, FormatStrFormatter, AutoMinorLocator
fig = plt.figure(figsize=(8,8))

ax = fig.add_subplot(111)
ax.set_xlim(0, 10)
ax.set_ylim(0, 100)

ax.set_xlabel("X",fontsize = 14,color = 'b',alpha = 0.7,bbox=dict(boxstyle='round,pad=0.5', fc='yellow', ec='blue',lw=1 ,alpha=0.7))
ax.set_ylabel("Y",fontsize = 14,color = 'b',alpha = 0.7,bbox=dict(boxstyle='round,pad=0.5', fc='yellow', ec='blue',lw=1 ,alpha=0.7))

ax.xaxis.set_major_locator(MultipleLocator(1.0))	## MultipleLocator(1.0)表示刻度间隔1.0
ax.yaxis.set_major_locator(MultipleLocator(5.0))
ax.xaxis.set_minor_locator(MultipleLocator(0.25))	## 副刻度一格0.25
ax.xaxis.set_minor_formatter(FormatStrFormatter('%0.2f'))		## 小数点后两位float

ax.tick_params("x",which='major',length=5,width=1.0,colors="lightgreen",direction = 'in')  
ax.tick_params("y",which='major',length=5,width=1.0,colors="r",direction = 'in')
ax.tick_params(which='minor',length=5,width=1.0,labelsize=5, labelcolor='0.25',direction = 'inout')	## 同时设置x/y轴的副刻度

for ticklabel in ax.xaxis.get_ticklabels():
    ticklabel.set_color("slateblue")
    ticklabel.set_fontsize(12)
    ticklabel.set_rotation(30)
for ticklabel in ax.yaxis.get_ticklabels():
    ticklabel.set_color("slateblue")
    ticklabel.set_fontsize(12)

plt.show()
```

### 使用pyplot函数

#### 坐标轴刻度范围

```python
plt.xlim(xstart, xend)
plt.ylim(ystart, yend)
```

> xstart——刻度的起点
>
> xend——刻度的终点
>
> 起点、终点不具有大小关系的限制，xstart > xend也可，坐标轴刻度将**由大到小**。

#### 坐标轴刻度位置、标签

```python
plt.xticks(ticks, labels, **kwargs)
```

> ticks——刻度位置。传递一个空列表会删除所有xticks。
>
> labels——刻度标签
>
> kwargs——刻度标签外观

```python
rotation = 20	##旋转20度
rotation = 'vertical'	## 标签竖直
```

```python
>>> locs, labels = xticks()  # Get the current locations and labels.
>>> xticks(np.arange(0, 1, step=0.2))  # Set label locations.
>>> xticks(np.arange(3), ['Tom', 'Dick', 'Sue'])  # Set text labels.
>>> xticks([0, 1, 2], ['January', 'February', 'March'],rotation=20)  # Set text labels and properties.
>>> xticks([0, 1, 2], ['January', 'February', 'March'],rotation='vertical')  # Set text labels and properties.
>>> xticks([])  # Disable xticks.
```



---

## 7.箭头指向+注释文本

### 箭头注释

#### 普通

```python
ax.annotate("maximum",xy=(np.pi/2,1.0),xycoords="data",
xytext=((np.pi/2)+0.15,0.8),textcoords="data",
weight="bold",color="r",
arrowprops=dict(arrowstyle="->",
connectionstyle="arc3",
color="r"))
```

> **xycoords:xy的坐标系统，参数值data表示与折线图使用相同的坐标系统。**
>
> **textcoords:xytext的坐标系统。**

#### 圆角文本框

```python
## ax.annotate加一个bbox=dict(boxstyle = 'round')属性
round
square
```

#### 箭头弧度

```python
arrowprops = dict(arrowstyle="-|>",
connectionstyle="angle,angleA=0,angleB=90,rad=10",
color="r")
```

### 无箭头文本

#### 圆角文本框

```python
plt.text(6,0, "Matplotlib", size=30, rotation=30.,
bbox=dict(boxstyle="round",
ec="#8968CD",
fc="#FFE1FF"))		
```

#### 水印效果

```python
## 将text和plot的alpha都调小，凸显文本的透明
```



---

## 8. 画布分割

==**title设置子区标题，suptitle设置总标题**==

### （1）等分划分子区

subplot(C,R,P)，那么这三个整数就表示在C行、R列的网格布局上，子区subplot()会被放置在第P个位置上，即为将==被创建的子区编号，子区编号从1开始，起始于右上角，序号依次向右递增。==

==子区的编号从1开始。==

==P只表示编号，不代表实际有几个子区，根据编号确定子区的位置。==

#### 每个子区一个图

* 一次创建有若干若干**子区**的fig

  ```python
  fig,ax = plt.subplots(nrows, ncols)
  ```

  > **nrows,ncols——创建有几行几列子区的fig**
  >
  > **fig——画布对象**
  >
  > **ax——子区对象数组，nrows行，ncols列**

  ```python
  fig, ax = plt.subplots(1,2,sharey=True)		## 一次创建有1行2列子区的画布，返回fig画布实例、ax是子区实例的集合
  
  ax1 = ax[0]				## 第1个子区
  ax1.plot(x,y,"k--",lw=2)
  ax1.set_title(’折线图’)
  ax1.grid(ls=":",lw=1,color="gray",alpha=0.8)
  
  ax2 = ax[1]				## 第2个子区
  ax2.scatter(x,y,s=10,c="skyblue",marker="o")
  ax2.set_title("散点图")
  ```

  如果我们想要改变子区边缘相距画布边缘的距离和子区边缘之间的高度与宽度的距离，可以调用函数subplots_adjust(*agrs,**kwargs)进行设置。

  ```python
  import matplotlib.pyplot as plt
  import numpy as np
  # fig = plt.figure()
  fig,ax = plt.subplots(2,3,sharey=True)
  x = np.linspace(0.0,2*np.pi)
  y = np.cos(x)*np.sin(x)
  # ax1 = fig.add_subplot(211)
  ax1 = ax[0,0]				## 第1行第1列子区
  ax1.margins(0.03)
  ax1.set_title("11111", fontsize =12)
  ax1.plot(x,y,ls="-",lw=2,color="b")
  # ax2 = fig.add_subplot(223)
  ax2 = ax[0,1]				## 第1行第2列子区
  ax2.margins(0.7,0.7)
  ax2.set_title("22222", fontsize =12)
  ax2.plot(x,y,ls="-",lw=2,color="r")
  # ax3 = fig.add_subplot(224)
  ax3 = ax[0,2]				## 第1行第2列子区
  ax3.margins(x=0.1,y=0.3)
  ax3.set_title("33333", fontsize =12)
  ax3.plot(x,y,ls="-",lw=2,color="g")
  
  ax4 = ax[1,0]				## 第2行第1列子区
  ax4.margins(0.03)
  ax4.set_title("44444", fontsize =12)
  ax4.plot(x,y,ls="-",lw=2,color="b")
  # ax2 = fig.add_subplot(223)
  ax5 = ax[1,1]				## 第1行第2列子区
  ax5.margins(0.7,0.7)
  ax5.set_title("55555", fontsize =12)
  ax5.plot(x,y,ls="-",lw=2,color="r")
  # ax3 = fig.add_subplot(224)
  ax6 = ax[1,2]				## 第2行第3列子区
  ax6.margins(x=0.1,y=0.3)
  ax6.set_title("66666", fontsize =12)
  ax6.plot(x,y,ls="-",lw=2,color="g")
  
  fig.suptitle("main title",fontsize=25)
  plt.show()
  ```

  ![image-20220224113419260](https://s2.loli.net/2022/02/24/dgru7aSKjRNF3pM.png)

* 一个子区一个子区添加

  ```python
  ax1 = plt.subplot(221)
  ```

  子区函数subplot(211)和子区函数subplot(212)代表首先在画布上分隔出一个2行1列的画布格式，然后在一个2行1列的画布格式上分别绘制图形1和图形2。

  先确定当前在哪个画布上绘制，再设置坐标轴和绘制。

  ```python
  ax1 = plt.subplot(221)
  ax1.title()
  ax1.scatter()
  bx2 plt.subplot(222)
  ax2.title()
  ax2.plot()
  ax3 = plt.subplot(223)
  ax3.title()
  ax3.hist()
  ax4 = plt.subplot(224)
  ax4.title()
  ax4.pie()
  ```

  

#### 合并等分画布的若干子区

```python
ax1 = plt.add_subplot(121)
```

```python
ax1 = plt.add_subplot(121)	## 1行2列布局的第1个子区的位置
ax1.margines(x=0.03, y=0.1)	## 曲线两侧空白的区域（以图线所占区间的比例表示，x=0.03表示x轴方向左右空白区域是0.03倍图形的横轴区间）
ax1.plot()
ax2 = plt.add_subplot(122)	## 2行2列布局的第2个子区的位置
ax2.plot()
ax3 = plt.add_subplot(124)	## 1行2列布局的第4个子区的位置
ax3.plot()
```

![image-20220224100954127](https://s2.loli.net/2022/02/28/8FzrufJdwK6tDWh.png)

```python
import matplotlib.pyplot as plt
import numpy as np
fig = plt.figure()
x = np.linspace(0.0,2*np.pi)
y = np.cos(x)*np.sin(x)
ax1 = fig.add_subplot(211)		
ax1.margins(0.03)
ax1.set_title("11111", fontsize =12)		## 子标题
ax1.plot(x,y,ls="-",lw=2,color="b")
ax2 = fig.add_subplot(223)
ax2.margins(0.7,0.7)
ax2.set_title("22222", fontsize =12)
ax2.plot(x,y,ls="-",lw=2,color="r")
ax3 = fig.add_subplot(224)
ax3.margins(x=0.1,y=0.3)
ax3.set_title("33333", fontsize =12)
ax3.plot(x,y,ls="-",lw=2,color="g")
fig.suptitle("main title",fontsize=25)		## 总标题
plt.show()
```

![image-20220224101117495](https://s2.loli.net/2022/02/24/YRgMLvT8rDVh5if.png)

### （2）跨越固定网格布局

将fig以网格划分，从而**更精细地指定子区的位置和尺寸。**

```python
plt.subplot2grid((2,3),(1,0),rowspan=1, colspan=3)
```

> (2,3)——将fig划分为2行3列
>
> (1,0)——表示当前子区的在第2行第1列网格所在的子区（==网格编号从0开始==）
>
> rowspan——子区所占的网格行数
>
> colspan——子区所占的网格列数

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np
mpl.rcParams["font.sans-serif"]=["SimHei"]
mpl.rcParams["axes.unicode_minus"]=False
# set subplot(23,1-2)

plt.subplot2grid((2,3),(0,0),colspan=2)
x = np.linspace(0.0,4.0,100)
y = np.random.randn(100)
plt.scatter(x,y,c="c")
plt.title("散点图")		## 设置子区标题

# set subplot(233)
plt.subplot2grid((2,3),(0,2), colspan=1)
plt.title("空白绘图区域")		## 设置子区标题

# set subplot(23,4-6)
plt.subplot2grid((2,3),(1,0),colspan=3,rowspan=1)
x = np.linspace(0.0,4.0,100)
y1 = np.sin(x)
plt.plot(x,y1,lw=2,ls="-")
plt.xlim(0,3)
plt.grid(True,ls=":",c="r")
plt.title("折线图")			## 设置子区标题
# set figure title
plt.suptitle("subplot2grid()函数的实例展示",fontsize=25)	## 设置总标题
plt.show()
```

![image-20220224104205246](https://s2.loli.net/2022/02/24/NWmBxM1V5bqS2jr.png)



---

## 9.共用坐标轴（在一个坐标轴下绘制多个图形）

注意：这不是在一个子区内绘制多个曲线，而是绘制多个图形。只是共用了某条坐标轴的刻度。本质上是多个图。

### （1）一个子区内共享坐标轴

```python
import matplotlib as mpl
import matplotlib.pyplot as plt
import numpy as np
mpl.rcParams["font.sans-serif"]=["SimHei"]
mpl.rcParams["axes.unicode_minus"]=False
x = np.linspace(0, 10, 1000)
y1 = x**2
fig,ax1 = plt.subplots(1,1)
ax1.plot(x, y1, ls='-', lw=2, c='r')
ax1.tick_params("y", colors='r')

y2 = x + 10
ax2 = ax1.twinx()
ax2.plot(x, y2, ls='-', lw=2, c='blue')
ax2.tick_params("y", colors='blue')

# ax1.plot(x, y2, ls='-', lw=2, c='blue')
plt.show()
```

![image-20220228111813968](https://s2.loli.net/2022/02/28/AT9Umb8nfl7CPZu.png)

> **上图本质上是2个图，所以曲线间的相对位置和交点没有意义**
>
> **如果要花交点，要把两个曲线绘制在同一个ax中**
>
> ```python
> import matplotlib as mpl
> import matplotlib.pyplot as plt
> import numpy as np
> mpl.rcParams["font.sans-serif"]=["SimHei"]
> mpl.rcParams["axes.unicode_minus"]=False
> x = np.linspace(0, 10, 1000)
> y1 = x**2
> fig,ax1 = plt.subplots(1,1)
> ax1.plot(x, y1, ls='-', lw=2, c='r')
> ax1.tick_params("y", colors='r')
> 
> y2 = x + 10
> ax1.plot(x, y2, ls='-', lw=2, c='blue')
> plt.show()
> ```
>
> ![image-20220228112308802](https://s2.loli.net/2022/02/28/tN6aXYnZFQzM9US.png)



---



## 10. 图像保存

```python
plt.savefig(path)
```















