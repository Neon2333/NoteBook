# [官网文档](https://matplotlib.org/stable/tutorials/)

# 1. matplotlib绘图的组成元素

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
> lw——线宽
>
> c——颜色
>
> label——该曲线的标签（显示在legend中）

### legend——**图例**

先plot()后legend()

```python 
plt.legend(wedges, labels, loc = 'best', bbox_to_anchor = (num1, num2), ncol = 2,title = '图例标题', edgecolor = 'black', facecolor = 'pink')
```

> **wedges——饼片实例列表。就可以将饼片外部的文本标签放在图例中，而饼片的数值标签仍然放在饼片内部**
>
> **labels——文本标签。若缺省则显示pie等绘图函数中的labels。**
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

### 注释文本

在图形的指定位置添加文本，用于对图形进行说明

```python
plt.text(2.8, 0.3, s= 'sin(x)', weight = 'bold', color = 'blue')
```

### [annotate](https://zhuanlan.zhihu.com/p/32501335)——箭头指向+注释文本

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

https://blog.csdn.net/captain811/article/details/79248912?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ELandingCtr%7EHighlightScore-4.queryctrv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ELandingCtr%7EHighlightScore-4.queryctrv2

https://zhuanlan.zhihu.com/p/38160291

饼图通过将一个圆饼按照分类的占比划分成多个区块，整个圆饼代表数据的总量，每个区块（圆弧）表示该分类占总体的比例大小，所有区块（圆弧）的加和等于 100%。

```python
labels = ["面粉","砂糖","奶油","草莓酱","坚果"] 	## 每个饼片的文字标签
weight = [0.15,0.35,0.25,0.25]		## 饼片的权重（和为1）
colors = ["#377eb8","#4daf4a","#984ea3","#ff7f00"]		## 饼片的颜色
explode = (0.1,0,0,0)	## 饼片突出的程度

wedges, texts, autotexts = plt.pie(weight, labels = labels, explode = explode, autopct = "%3.1f%%", colors = colors, shadow = True, startangle = 45, radius = 0.7, labeldistance = 1.1, pctdistance = 0.7, textprops = dict('fontsize':13, 'color':'black'), wedgeprops = {'linewidth':0.3, 'edgecolor':'white'}, frame = True, center = (1,1))

## 图例函数“plt.legend(wedges,elements)”，我们就可以将饼片外部的文本标签放在图例中，而饼片的数值标签仍然放在饼片内部。函数legend()的参数wedges和elements分别表示饼片实例列表和文本标签列表
plt.legend(wedges,elements,fontsize=12,title="配料表",loc="center left",bbox_to_anchor=(0.91, 0, 0.3, 1))

plt.axis('equal')	## 让显示的饼图保持圆形，需要添加axis保证长宽一样
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
plt.show()
```

![image-20220215141018335](https://s2.loli.net/2022/02/15/PL7rkEamHbVhDeB.png)



### 箱线图



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

> style——字体风格
>
> family——字体类别















