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

`str()`

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

### （3）Pycharm注释快捷键

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

#### 返回列表排序副本

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

### （9）数值列表创建

#### <1>	range()生成一系列数字：左闭右开

```python
#1~99，步长为1
for val in range(1, 100):
    print(val)
#1~99，步长为2
for val in range(1, 100, 2):
    print(val)
```

#### <2>	list()和range()创建数值列表

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

#### <3>	列表解析创建

```python
ll = [val**2 for val in range(1, 11)]
print(ll)
```

### （10）数值列表统计

#### 最大值

```python
max(ll)
```

#### 最小值

```python
min(ll)
```

#### 求和

```python
sum(ll)
```

### （11）列表切片——截取部分

截取列表一部分

#### 左闭右开区间

```python
players = ['charles', 'martina', 'michael', 'florence', 'eli']
print(players[1:3])		# martina、michael
```

#### 省略右侧范围

从左侧范围到**末尾**

```python
players = ['charles', 'martina', 'michael', 'florence', 'eli']
print(players[1:])	#martina、michael、florance、eli
print(players[-2:])	#florance、eli
print(players[:3])	#charles、martina、michael
```

#### 遍历切片

```python
players = ['charles', 'martina', 'michael', 'florence', 'eli']
for player in players[1:3]:
    print(player)	#martina、michael
```

### （12）复制列表

列表不能直接赋值，只能**赋值列表切片**，需要指定列表的范围

```python
my_foods = ['pizza', 'falafel', 'carrot cake']
friend_foods = my_foods[:]	#正确
friend_foods = my_foods	    #错误
```

### （13）判断列表是否为空

```python
ll = []
if ll:		#ll为空时判断为false，不为空时判断为true
    print("ll is not empty")
```

### （14）元组

元组看起来犹如列表，如果需要存储的一组值**在程序的整个生命周期内都不变**，可使用元组。 

元组的元素值不能修改。但是元组变量可以修改。

```python
dimensions = (200, 50)	#定义
print(dimensions[0])	#索引访问
print(dimensions[1])
```

```python
dimensions = (100,300)
for d in dimensions:
    print(d)

dimensions = (200,400)	#元组变量可以修改
for d in dimensions:
    print(d)
```



---

## 5. 缩进

PEP 8建议每级缩进都使用***四个空格***，这既可提高可读性，又留下了足够的多级缩进空间。在字处理文档中，大家常常使用制表符而不是空格来缩进。对于字处理文档来说，这样做的效果很好，但混合使用制表符和空格会让Python解释器感到迷惑。每款文本编辑器都提供了一种设置，可将输入的制表符转换为指定数量的空格。你在编写代码时应该使用制表符键，但一定要对编辑器进行设置，使其在文档中插入空格而不是制表符。



---

## 6. if

### （1）运算符

**==**

**!=**

**and——&&**

**or——||**

**in——检查特定值是否在列表中**

**not in——检查特定值是否不在列表中**

### （2）if语句

同C/C++/C#一样，条件语句中某条语句被执行后，其他语句就不会被判断执行

```python
if con:
    doSomething
else:
    doSomething
```

```python
if con1:
    doSomething
elif con2:
    doSomething
else:
    doSomething
```

## 7. 字典

### （1）定义

**每个键只能对应一个项，不允许一个键对应多个值**

键值对key-value，value可以是列表、字典，可以是Python中的任何对象

字典可包含任意数量的键值对，key和value间以`:`分隔，键值对间以`,`分隔

### （2）访问key和value

```python
ll = ['color':blue, 'price':10]
print(ll['color'])
print(ll['price'])
```

### （3）添加key-value

字典是一种动态结构，可随时在其中添加键—值对。直接给需要添加的key赋值value即可

字典键值对的添加顺序不重要

```python
ll = {'color': 'green', 'points': 5}
#添加键值对
ll['x_position'] = 10
ll['y_position'] = 20
```

```python
ll = {}		#创建空字典
#添加键值对
ll['x_position'] = 10
ll['y_position'] = 20
```

### （4）修改键值对

```python
ll = {'color': 'green', 'points': 5}
ll['color'] = 'blue'
```

### （5）删除键值对

```python
ll = {'color': 'green', 'points': 5}
del ll['color']
```

### （6）遍历字典

#### 遍历键值对

```python
ll = {'color': 'green', 'points': 5}
for key, val in ll.items():
    print(key + ', ' + val)
```

#### 获取/遍历所有键

`keys()`函数返回所有键组成的列表

```python
ll = {'color': 'green', 'points': 5}
for val in ll.keys():
    print(val.title())
```

#### 获取/遍历所有值

`values()`函数提取所有的值到**列表**，但是因为values可能存在重复（不同于key，key不会重复），若有重复也会提取出来

```python
favorite_languages = {
'jen': 'python',
'sarah': 'c',
'edward': 'ruby',
'phil': 'python',
}
for name in favorite_languages.values():
    print(name.title()) #python c ruby python
```

#### 用set避免values()提取的值重复

用values()函数提取得到的列表初始化一个set，会自动将列表中的重复值去掉

```python
favorite_languages = {
'jen': 'python',
'sarah': 'c',
'edward': 'ruby',
'phil': 'python',
}
for language in set(favorite_languages.values()):	#用favorite_languages.values()生成的列表初始化set，去重
print(language.title())
```

#### 先排序再遍历

```python
favorite_languages = {
'jen': 'python',
'sarah': 'c',
'edward': 'ruby',
'phil': 'python',
}
for name in sorted(favorite_languages.keys()):
print(name.title() + ", thank you for taking the poll.")
```

### （7）字典、列表的嵌套

* 一系列字典嵌套在列表中——***每个字典存放一个对象的一系列属性***
* 一系列列表作为value嵌套在字典中
* 一系列字典作为value嵌套在字典中

#### <1> 字典列表

列表中存储多个字典，每个字典存放某个对象的多种属性

```python
# 创建一个用于存储外星人的空列表
aliens = []
# 创建30个绿色的外星人
for alien_number in range (0,30):
    new_alien = {'color': 'green', 'points': 5, 'speed': 'slow'}
aliens.append(new_alien)
for alien in aliens[0:3]:
if alien['color'] == 'green':
alien['color'] = 'yellow'
alien['speed'] = 'medium'
alien['points'] = 10
# 显示前五个外星人
for alien in aliens[0:5]:
print(alien)
print("...")
```

#### <2> 列表作为字典的value

***字典的一个key需要关联多个value时***

一个字典存放一个对象的多种属性，若某个属性的值有多个值就可以用列表

```python
pizza = {
'crust': 'thick',
'toppings': ['mushrooms', 'extra cheese'],	#pizza的toppings属性有多个值
}
```

在遍历时，就需要嵌套for，外层for遍历字典，内层for遍历列表

```python
favorite_languages = {
'jen': ['python', 'ruby'],
'sarah': ['c'],
'edward': ['ruby', 'go'],
'phil': ['python', 'haskell'],
}
for name, languages in favorite_languages.items():
print("\n" + name.title() + "'s favorite languages are:")
for language in languages:
print("\t" + language.title())
```

#### <3> 字典嵌套字典

value仍然是字典

```python
users = {
'aeinstein': {
'first': 'albert',
'last': 'einstein',
'location': 'princeton',
},
'mcurie': {
'first': 'marie',
'last': 'curie',
'location': 'paris',
},
}
for username, user_info in users.items():
print("\nUsername: " + username)
full_name = user_info['first'] + " " + user_info['last']
location = user_info['location']
print("\tFull name: " + full_name.title())
print("\tLocation: " + location.title())
```

#### <4> 示例

```python
## 创建三个字典存储人的信息，三个字典存到列表中
person1 = {"name":"wang", "age":20, "job":"coder"}
person2 = {"name":"zhang", "age":30, "job":"manager"}
person3 = {"name":"li", "age":40, "job":"professor"}
people = [person1, person2, person3]
for peo in people:
    print("name=" + peo["name"] + ", age=" + str(peo["age"]) + ", job=" + peo["job"])
```

```python
## 创建字典存储三个人，人名作为key，value为列表存储若干个最喜欢的城市
favorite_places = {
                    "zhang":["beijing", "shanghai", "guangzhou"],
                    "li":["wuhan", "hangzhou"],
                    "wang":["new york", "tokyo", "LA"]
                   }
for key, val in favorite_places.items():
    print(key + "=  ")
    for item in val:
        print(item + "\t")
    print("\n")
```

```python
## 创建字典cities，城市名作为key,字典作为value存储"country"、"population"、"fact"
cities = {
    "new york":{"country":"USA", "population":1000, "fact":"capital of USA"},
    "tokyo":{"country":"Japan", "population":800, "fact":"capital of Japan"}
}
for keys, vals in cities.items():
    print("key=")
    for key, val in vals.items():
        print(key + ":" + str(val) + "\t")
    print("\n")
```



---

## 8. IO

### （1）input函数

```python
msg = input("提示信息")
```

若提示信息过长，可用变量存储，然后再将变量传递给Input

```python
msgHelp = "提示信息"
msg = input(msgHelp)
```

* Python将input读取的用户输入解读为字符串，若向转化为数值使用，用`int()`函数将其转化

  ```python
  num = input("please enter a num:")
  num = int(num)
  if(num > 10)
  	print("this num is bigger than 10")
  ```



---

## 9. while循环

for循环用于遍历

* break退出while/for循环
* continue终止当前一次循环，继续下次循环

### 使用while处理列表

* 将一个列表的所有元素移动到另一个列表

  ```python
  unconfirmed_users = ["wang", "zhang", "li"]
  confirmed_users = []
  while unfirmed_users:
      cur_user = unconfirmed_users.pop()
      print(cur_user.title())
      confirmed_users.append(cur_user)
  print(confirmed_users)
  ```

* 删除列表中所有的指定值

  ```python
  pets = ["cat", "dog", 'dog', 'cat', 'dog', 'goldfish', 'cat', 'rabbit', 'cat']
  while "cat" in pets:
      remove("cat")	## remove只删除第一个指定的元素值
  print(pets)
  ```

  

























