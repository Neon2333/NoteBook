# [官方文档](https://www.numpy.org.cn/user/)

https://blog.csdn.net/xjl271314/article/details/80409034

https://www.runoob.com/numpy/numpy-dtype.html

https://www.numpy.org.cn/article/basics/an_introduction_to_scientific_python_numpy.html#%E6%95%B0%E7%BB%84%E5%9F%BA%E7%A1%80

---

## 1. 基本概念

#### **NumPy** 

* 是一个 Python 的第三方库，代表 “Numeric Python”，主要用于==**数学/科学计算，一般和scipy和Matplotlib一起用**。== 它是一个由多维数组对象和用于处理数组的例程集合组成的库。

* 使用 Numpy 我们可以轻松进行如下等计算：

> * 数组的算数和逻辑运算。
>
> - 傅立叶变换和用于图形操作的例程。
> - 与线性代数有关的操作。 NumPy 拥有线性代数和随机数生成的内置函数。

#### **ndarray**

* **概念**

  ==numpy中的ndarray(n-dimensional-array)为多维数组==，是numpy中最为重要也是python进行科学计算非常重要和基本的数据类型。numpy中基本的运算符进行了重载，算数运算符和逻辑运算符都是逐元素操作的，还有==广播机制(broadcasting)==，使得一个标量与多维数组相互运算的时候也是逐元素运算。==ndarray 的一个特点是同构：即其中所有元素的类型必须相同。==

  ndarray和Python库类不同`array.array`，后者只处理一维数组并提供较少的功能。

* **axis**

  可以把axis看成是存放下标的一个框框（虽然不准确，但是这么理解用起来没有问题），第一个框就是第0个axis, 第二个框就是第1个axis……

  对于矩阵$T_{ijk}(i=0,1,2;j=0,1,2,3;k=0,1,2,3,4)$，==sum(axis=0)==即$T_{jk}=\sum_{i=0}^{2}{T_{jk}}$

  ```python
  np.ones((3,4,5)) = 
  [[[1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]]
  
   [[1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]]
  
   [[1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]
    [1. 1. 1. 1. 1.]]]
  
  >>> a.sum(axis=0)
  array([[3., 3., 3., 3., 3.],
         [3., 3., 3., 3., 3.],
         [3., 3., 3., 3., 3.],
         [3., 3., 3., 3., 3.]])
  >>> a.sum(axis=1)
  array([[4., 4., 4., 4., 4.],
         [4., 4., 4., 4., 4.],
         [4., 4., 4., 4., 4.]])
  >>> a.sum(axis=2)
  array([[5., 5., 5., 5.],
         [5., 5., 5., 5.],
         [5., 5., 5., 5.]])
  ```
  
  ```python
  >>> b = np.arange(12).reshape(3,4)
  >>> b
  array([[ 0,  1,  2,  3],
         [ 4,  5,  6,  7],
         [ 8,  9, 10, 11]])
  >>>
  >>> b.sum(axis=0)                            # sum of each column
  array([12, 15, 18, 21])
  >>>
  >>> b.min(axis=1)                            # min of each row
  array([0, 4, 8])
  >>>
  >>> b.cumsum(axis=1)                         # cumulative sum along each row
  array([[ 0,  1,  3,  6],
         [ 4,  9, 15, 22],
         [ 8, 17, 27, 38]])
  ```



## 2. 数组ndarray

### （1）数据类型

每个numpy数组都是相同类型元素的网格。Numpy在创建数组时尝试猜测数据类型，但构造数组的函数通常还包含一个可选参数来显式指定数据类型。

```python
import numpy as np

x = np.array([1, 2])   # Let numpy choose the datatype
print(x.dtype)         # Prints "int64"

x = np.array([1.0, 2.0])   # Let numpy choose the datatype
print(x.dtype)             # Prints "float64"

x = np.array([1, 2], dtype=np.int64)   # Force a particular datatype
print(x.dtype)                         # Prints "int64"
```

### （2）创建数组

创建Numpy数组有三种不同的方法：

* 使用Numpy内部功能函数

* 从列表等其他Python的结构进行转换

* 使用特殊的库函数

#### 内部功能函数

```python
# shape是一个元组
a = numpy.empty(shape, dtype) # 创建指定形状和dtype的未初始化数组。其初始内容是随机的，取决于内存的状态
a = numpy.zeros(shape, dtype) # 返回特定大小，以 0 填充的新数组
a = numpy.ones(shape, dtype) # 返回特定大小，以 1 填充的新数组
a= numpy.full((2,2), 3, dtype)	# 返回指定大小的常数矩阵[[3 3] 
										# [3 3]]
a = numpy.eye(2)	# 生成2*2单位矩阵
```

```python
z = np.ones([3], dtype = int)  
print(z)	# [1 1 1]

a = np.ones((2,3,2))
```

#### 其他Python的结构进行转换——array()

==注意：传入的是列表、元组，而不是单个数值==

```python
numpy.asarray(a, dtype) # 类似于numpy.array(a)
numpy.fromiter(a, dtype) # 此函数从任何可迭代对象构建一个ndarray对象，返回一个新的一维数组。
```

> **asarry和array区别点：
> （1）当他们的参数是列表型数据(list)时，二者没有区别；
> （2）当他们的参数是数组类型(array)时，==np.array()会返回参数数组的一个副本==(copy，两者值一样但指向不同的内存),==np.asarray()会返回参数数组的一个引用==(两者指向同一块内存).**

> **list和array的区别：**
>
> List： python 中的 list 是 python 的内置数据类型，list 中的数据类型不必相同，在 list 中保存的是数据的存放的地址，即指针，并非数据。
> array： array() 是 numpy 包中的一个函数，array 里的元素都是同一类型。ndarray 是一个多维的数组对象，具有矢量算术运算能力和复杂的广播能力，并具有执行速度快和节省空间的特点。ndarray 的一个特点是同构：即其中所有元素的类型必须相同。
>
> **list不换行，用“,”号分割元素；array每个元素之间换行**
>
> ```python
> data = [[1,2,3],[4,5,6],[7,8,9]]
> # 直接输出为list，元素间用逗号分隔
> >>>[[1,2,3],[4,5,6],[7,8,9]]
> # 转化输出为array时，元素间换行
> >>>
> [[1,2,3]
> [4,5,6]
> [7,8,9]]
> 
> # 从[0,10)区间中随机生成一个2*3维度的数组（array）
> data2 = np.random.randint(0,10,(2,3))
> >>>
> [[5,1,3]
>  [6,5,1]]
> 
> ```

```python
import numpy as np 

x =  [1,2,3]  # x是python的列表类型 不是数组
a = np.array(x)  
print(a)  # [1  2  3]

y = (4,5,6) # y是python的元组类型
b = np.array(y, dtype = float, order = 'F')	# 指定dtype=float，将整型的y转成了float
print(b)  # [4. 5. 6.]

>>> c = np.array( [ [1,2], [3,4] ], dtype=complex )	# 指定dtype是复数
>>> c
array([[ 1.+0.j,  2.+0.j],
       [ 3.+0.j,  4.+0.j]])
```

```python
import numpy as np 

x = range(5) # range方法创建了一个列表
a = np.fromiter(x)
print(a) #[0. 1. 2. 3. 4.]
```

### （3）数组属性

**shape——数组尺寸，元组。若矩阵是$n*m$阶，则shape=(n,m)**

**size——数组元素总数，等于shape的元素乘积**

**reshape——设置数组尺寸**

**ndim——数组维度**

> 当用数组下标表示的时候，需要用几个数字来表示才能唯一确定这个元素，这个数组就是几维。
>
> ```python
> 例如，一维数组a = [1,2,3,4,5,8],取得一个元素用a[i]只有一层
> 
> 下面是一个二维数组，ndim为2，shape属性值为（3，3）
> [
>    [1,2,3],
>    [4,5,6],
>    [7,8,9]
> ]
> 
> 下面是一个三维数组，ndim为3，shape属性值为（2，2，3）
> [
>     [
>         [1,2,3],
>         [4,5,6]
>     ],
>     [
>        [7,8,9],
>        [10,11,12]
>     ]
> ]
> ```

**itemsize——数组元素所占字节数**

**real——返回数组，数组元素是原数组各个元素取实部**

**imag——返回数组，数组元素是原数组各个元素取虚部**

**T——返回数组，原数组的转置（也可用b = transpose(a) ）**

### （4）广播机制

**是在算术运算期间处理不同形状的数组的能力，对数组进行扩展**

* 当两个数组发生相互作用的时候，比如相加、相乘之类的，当数组的shape不一致的时候，就会把数组扩展成shape的数组进行逐元素操作，得到新的数组。

* broadcasting规则：

  1.两个shape当中有一个为1就进行扩展

  2.shape中有位置缺失，也可进行扩展

  例如：

  (4,5,1)shape的数组和(1,5,3)shape数组作用最终得到（4,5,3)shape的数组。

  (4,5,1)shape的数组和(4,)的数组作用最终得到(4,5,1)shape的数组

  ```python
  >>> a=np.ones((1,3,2))
  >>> a
  array([[[1., 1.],
          [1., 1.],
          [1., 1.]]])
  >>> a.shape
  (1, 3, 2)
  >>> b=np.ones((2,3,1))
  >>> b.shape
  (2, 3, 1)
  >>> a*b
  array([[[1., 1.],
          [1., 1.],
          [1., 1.]],
  
         [[1., 1.],
          [1., 1.],
          [1., 1.]]])
  >>> (a*b).shape
  (2, 3, 2)
  ```
  
  **a被扩展为（2,3,2)shape的数组，$a[1][i][j]=a[0][i][j]$。b也同理被扩展为(2,3,2)shape的数组，$b[i][j][1]=b[i][j][0]$。**
  
* **demo**

```python
a = np.asarray([[[1, 1], [1, 1], [1, 1]]])
b = np.asarray([[[1, 1], [1, 1], [1, 1]], [[1, 1], [1, 1], [1, 1]]])
print(a)
print(b)
print(a*b)

[[[1 1]
  [1 1]
  [1 1]]]
[[[1 1]
  [1 1]
  [1 1]]

 [[1 1]
  [1 1]
  [1 1]]]
[[[1 1]
  [1 1]
  [1 1]]

 [[1 1]
  [1 1]
  [1 1]]]
```

### （5）数组计算

* 加减乘除

> 加减乘除都是针对元素的计算，矩阵乘法用**a.dot(b)**

```python
import numpy as np

x = np.array([[1,2],[3,4]], dtype=np.float64)
y = np.array([[5,6],[7,8]], dtype=np.float64)

# Elementwise sum; both produce the array
# [[ 6.0  8.0]
#  [10.0 12.0]]
print(x + y)
print(np.add(x, y))

# Elementwise difference; both produce the array
# [[-4.0 -4.0]
#  [-4.0 -4.0]]
print(x - y)
print(np.subtract(x, y))

# Elementwise product; both produce the array
# [[ 5.0 12.0]
#  [21.0 32.0]]
print(x * y)
print(np.multiply(x, y))

# Elementwise division; both produce the array
# [[ 0.2         0.33333333]
#  [ 0.42857143  0.5       ]]
print(x / y)
print(np.divide(x, y))

# Elementwise square root; produces the array
# [[ 1.          1.41421356]
#  [ 1.73205081  2.        ]]
print(np.sqrt(x))
```

请注意，与MATLAB不同，`*`是元素乘法，而不是矩阵乘法。 我们使用`dot`函数来计算向量的内积，将向量乘以矩阵，并乘以矩阵。

* 特殊运算符

  ```python
  a = np.arange(10)
  
  print(a.sum()) # >>>45
  print(a.min()) # >>>0
  print(a.max()) # >>>9
  ```

  ```python
  ## 矩阵转置
  a.T
  ```


### （6）常用数组操作

| 方法      | 描述                     |
| --------- | ------------------------ |
| flat      | 数组上的一维迭代器       |
| flatten   | 返回折叠为一维的数组副本 |
| ravel     | 返回连续的展开数组       |
| transpose | 翻转数组的维度           |
| ndarray.T | 和self.transpose()相同   |
| rollaxis  | 向后滚动指定的轴         |
| swapaxes  | 互换数组的两个轴         |

#### **矩阵扁平化**

将多维矩阵展开成一维

> flatten和ravel区别
>
> Numpy中经常使用到的操作由扁平化操作,Numpy提供了两个函数进行此操作,他们的功能相同,但在内存上有很大的不同。
>
> 我们在平时使用的时候flatten()更为合适.在使用过程中==flatten()分配了新的内存,但ravel()返回的是一个数组的视图==.视图是数组的引用(说引用不太恰当,因为原数组和ravel()返回后的数组的地址并不一样),在使用过程中应该注意避免在修改视图时影响原本的数组。==在实际应用中应尽量使用flatten()函数==,这样避免意外的错误。

#### 数组切片

与对Python列表进行切片类似，可以对numpy数组进行切片。

可看做是==一段连续的索引==

```python
a[1:4]	## 切片的索引范围是1~3
a[:3]	## 索引范围是0~2
```

```python
## 要切出A矩阵中的第二列，我们会这样做
A = np.array([[1,-1,2],[3,2,0]])
col = A[:,1:2]
## 第一个切片选择A中的所有行，而第二个切片仅选择每行中的中间条目
```



#### 数组切片步长

`::`设定切片步长，`:`是省略了start或end

```python
a = np.arange(0,10)
print(a[::2]) 
>>>0,2,4,6,8
```

#### 点乘

```python
w = np.dot(a, b)  ## w = a * b
```

### （7）多维数组切片

```python
a = np.array([[1,2,3,4], [5,6,7,8], [9,10,11,12]])
b = a[:2, 1:3]
print(b)
>>>[[2 3]
   [6 7]]
## row：0、1
## col：1、2
```

### （8）数组索引

#### 整数数组索引

方括号内传入多个索引值，可以同时选择多个元素

> **一维数组**
>
> ```python
> x = np.array([1,2,3,4,5,6,7,8])
> r = [0,1,2]		## 相当于x[0] x[1] x[2]
> print(x[r])
>  [1 2 3]
> 
> r = [0,1,-1]
> print(x[r])
>  [1 2 8]
> ```

> **二维数组**
>
> ```python
> x = np.array([[11,12,13,14,15],[16,17,18,19,20],[21,22,23,24,25],[26,27,28,29,30]])
> r = [0,1,2]
> print(x)
> [[11 12 13 14 15]
> [16 17 18 19 20]
> [21 22 23 24 25]
> [26 27 28 29 30]]
> print(x[r])			## x[0] x[1] x[2]
> [[11 12 13 14 15]
> [16 17 18 19 20]
> [21 22 23 24 25]]
> 
> x = np.array([[11,12,13,14,15],[16,17,18,19,20],[21,22,23,24,25],[26,27,28,29,30]])
> r = [0,1,2]
> c = [2,3,4]
> y = x[r,c]		## x[0,2] x[1,3] x[2,4]
> print(y)
> [13 19 25]
> ```

#### 整数数组索引和切片结合

```python
x = np.array([[11,12,13,14,15],[16,17,18,19,20],[21,22,23,24,25],[26,27,28,29,30]])
y = x[0:3,[1,2,2]]		## x[0,1] x[0,2] x[0,2]
					   ## x[1,1] x[1,2] x[1,2]
    				   ## x[2,1] x[2,2] x[2,2]
print(y)
[[12 13 13]
[17 18 18]
[22 23 23]]
```

### （9）高维数组索引

https://zhuanlan.zhihu.com/p/147290117

#### 确定高维数组元素索引

在面对高维数组时，通过索引来获取某个元素还是比较麻烦的。

![image-20220217143610961](https://s2.loli.net/2022/02/17/ia1LxGPvpnr79HV.png)

想取得上图中17这个元素，应该怎么办呢？

![image-20220217143656049](https://s2.loli.net/2022/02/17/O2JbQnZoP1AEmNp.png)

17在黄色块的[1,1]位置

黄色块在整个矩阵的[1,0]位置

==所以17在整个矩阵的索引为[1, 0, 1, 1]==

---

## 3. 矩阵

np.array()——==列表的列表，内层一个列表创建一行==

### 创建矩阵

```python
A = np.array([[1,2,3],[4,5,6]])
```

### 创建向量

```python
## 先创建一行，再转置
w = np.transpose(np.array([1,2,3]))
w = np.array([1,2,3]).T
```

```python
w = np.array([[1],[2],[3]])		## 创建三行，每行只有一个元素
```

### 求解矩阵方程AX=b

```python
A = np.array([[2,1,-2],[3,0,1],[1,1,-1]])
b = np.transpose(np.array([[-3,5,-2]]))
x = np.linalg.solve(A,b)
```

---

## 4. OpenCV相关

https://www.numpy.org.cn/article/other/py_basic_ops.html#%E7%9B%AE%E6%A0%87





---

## 5. 常用函数

## 1. random.rand()和random.randn()

* rand()根据指定维度生成[0,1)之间的均匀分布的随机数（包含0，不包含1）：
  (1) rand()——生成一个随机数
  (2) rand(100)——生成一个长度为100的向量；
  (3) rand(3,2)——生成3个长度为2的向量（3*2矩阵）；
  (4) rand(4,3,2)——生成4个长度3*2矩阵。

* randn()——根据指定维度生成服从标准正态分布N(0,1)的随机数

  ```python
  np.random.randn(100)	##生成100个
  ```

  

* random.randint()——生成范围内服从正态分布的随机数

```python
random.randint(low = 0, high = 10, size = (5,3), dtype)
```

> 生成整数范围[low, high)，若high省略则为[0,low)
>
> size——生成结果的维度：同rand
>
> dtype——默认int，可设为int64

## 2. range()和arange()

**在给定==间隔==内根据==步长==返回均匀间隔[start,stop)的值组成的array**

* range()

  只能生成**int型数据**，不能生成float型数据

  ```python
  range(start, end, step)
  ```

* numpy.arange()

  需要引入numpy

  除了int型数据，**可以生成float数据**。但生成float数据由于有限的浮点精度，通常不可能预测所获得的元素的数量。出于这个原因，通常最好使用`linspace`函数来接收我们想要的元素数量的函数，而不是步长（step），因为linespace可以直接指定生成的点的个数。

```python
numpy.arange(start, stop, step, dtype = None)
```

> start —— 开始位置，数字，可选项，**默认起始值为0**
> stop —— 停止位置，数字，开区间
> step —— 步长，数字，可选项， 默认步长为1，如果指定了step，则还必须给出start。
> dtype —— 输出数组的类型。 如果未给出dtype，则从其他输入参数推断数据类型。

```python
A = arange(5)  # 只有结束项，即[0,5)
print(A)  # 结果 [0 1 2 3 4] 结果不包含结束项
print(type(A))  # 结果 <class 'numpy.ndarray'>

A = arange(1, 5)  # 起点为1，步长默认为1
print(A)  # 结果 [1 2 3 4]

A = arange(1, 5, 2)  # 步长默认为2
print(A)  # 结果 [1 3]

A = arange(1, 5.2, 0.6)  # 浮点数参数，结果就不一定完全符合了
print(A)  # 结果 [1.  1.6 2.2 2.8 3.4 4.  4.6 5.2]
```

## 3. linspace()

会获得一个既==包含起始值又包含终止值==的==点个数确定==的数组，而且元素之间的步长（间隔）是相同的。

```python
np.linspace(start, stop, num, endpoint = True)
```

> start——起始值
>
> stop——终止值（包含）
>
> num——指定取值的个数
>
> endpoint——True（默认）：包含终止值（start~stop分成num-1份），False：不包含终止值（start~stop分成num份）

## 4. power()

**数组的元素分别求n次方，power(x1,x2)，x2可以是数字，也可以是数组，但是x1和x2的列数要相同。**

```python
>>> x1 = range(6)
>>> x1
[0, 1, 2, 3, 4, 5]
>>> np.power(x1, 3)
array([  0,   1,   8,  27,  64, 125])
```

```python
>>> x2 = [1.0, 2.0, 3.0, 3.0, 2.0, 1.0]
>>> np.power(x1, x2)
array([  0.,   1.,   8.,  27.,  16.,   5.])
```

```python
>>> x2 = np.array([[1, 2, 3, 3, 2, 1], [1, 2, 3, 3, 2, 1]])
>>> x2
array([[1, 2, 3, 3, 2, 1],
       [1, 2, 3, 3, 2, 1]])
>>> np.power(x1, x2)
array([[ 0,  1,  8, 27, 16,  5],
       [ 0,  1,  8, 27, 16,  5]])
```



