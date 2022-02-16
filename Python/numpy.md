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
random.randint(low = 0, high = 10, size = 5, dtype)
```

> 生成整数范围[low, high)，若high省略则为[0,low)
>
> size——生成结果的维度：同rand
>
> dtype——默认int，可设为int64

## 2. range()和arange()

**在给定间隔内返回均匀间隔[start,stop)的值**

* range()

  只能生成int型数据，不能生成float型数据

  ```python
  range(start, end, step)
  ```

* numpy.arange()

  需要引入numpy

  除了int型数据，**可以生成float数据**

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

## 3. power()

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



