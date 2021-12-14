# 基础数据类型

## 1. 字符串

### （1）打印字符串

```python
print("hello world!")
```

### （2）拼接

```python
msg1 = "hello world"
msg2 = "wk"
print(msg1 + " " + msg2)
```

### （3）制表符、换行符

```python
\t
\n
```

### （4）删除字符串首尾空白

```python
>>> favorite_language = ' python '
>>> favorite_language.rstrip()
' python'
>>> favorite_language.lstrip()
'python '
>>> favorite_language.strip()
'python
```

### （5）引号

Python字符串可用`单引号`或`双引号`

字符串内有单引号的话就用双引号将字符串括起来，字符串内有双引号的话就用单引号将字符串括起来。这样，就不会出现错误识别字符串的情况。

```python
msg1="  hello world"
msg2="wk    "
print((msg1 + " " + msg2).strip().upper())
```

## 2. 数字

### （1）整数

```python
2 + 1
2 - 1
2 * 1
2 / 1
2 ** 2
```

### （2）浮点数

浮点数运算结果的位数不确定，多余的小数位需要处理

### （3）数字转字符串

```python
num = 30.5
print(str(num))
```

## 3. 注释

### （1）单行注释

```python
#print("hello world!")
```

### （2）多行注释

```python
'''
3个连续的单引号括起来
'''
```

```python
"""
3个连续的双引号括起来
"""
```

### （3）Pycharm快捷键

Ctrl+/

## 4. 列表

### （1）定义

容器，可存储不同类型的数据。以方括号括起来，逗号分隔元素。以索引访问元素，索引从0开始。

### （2）访问/修改元素

索引访问

```python
lls = ["hello", 1.4, 10]
print(lls[0].upper())
```

```python
lls = ["hello", 1.4, 10]
lls[0] = "haha"
```

#### 负数索引

list[-1]表示最后一个元素

list[-2]表示倒数第二个元素

......

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
print(cars[-1])		#'subaru'
```



### （3）添加元素

```python
#末尾添加
lls = ["hello", 1.4, 10]
lls.append("wk")
```

```python
#指定位置添加
lls = ["hello", 1.4, 10]
lls.insert(0, "hhh") 
print(lls)	#["hhh", "hello", 1.4, 10]

#insert添加元素的索引位置，元素将取代原先元素，其他元素向后移动一位
```

### （4）删除元素

```python
lls = ["hello", 1.4, 10]
del lls[0]
print(lls)	#[1.4, 10]
```

### （5）弹出且获取元素

```python
lls = ["hello", 1.4, 10]
sstr = lls.pop()	#弹出末尾元素
```

```python
lls = ["hello", 1.4, 10]
sstr = lls.pop(0)	#弹出索引0处元素
```

```python
lls = ["hello", 1.4, 10]
sstr = lls.remove(1.4)	#删除匹配指定元素的第一个值
```

### （6）字典序排序

#### 按照字典序给列表元素排序

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.sort()		#按照字母序排
print(cars)
```

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.sort(reverse=True)	#按照字母序反向排
print(cars)
```

#### 临时排序

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
print(sorted(cars))		#cars本身的顺序不变
```

### （7）翻转列表

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
cars.reverse()	#翻转列表，不是按照字典序排，只是将列表内顺序翻转
print(cars)
```

### （8）遍历列表

python的for语句体不用花括号表示，而用缩进表示，for后跟着缩进的语句都是for语句体，不缩进的属于for语句体外。

```python
cars = ['bmw', 'audi', 'toyota', 'subaru']
for car in cars:
    print(car)
```

### （9）数值列表

#### range()生成一系列数字

```python
#1~99，步长为1
for val in range(1, 100):
    print(val)
#1~99，步长为2
for val in range(1, 100, 2):
    print(val)
```

#### list()和range()创建数值列表

```python
nums = list(range(1, 100))	#用list创建
print(nums)
```

```python
ll = []
for val in range(1, 100, 2):
    ll.append(val**2)	#append向空列表添加
print(ll)
```

### （10）数值列表统计





### 





















































