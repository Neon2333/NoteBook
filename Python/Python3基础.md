# 注意点收集

## 1.形参

Python中传值是通过引用传值





---

# pycharm快捷键

注释一行：Ctrl-/

删除一行：Ctrl-Y

开始debugger：shift-F9

结束debugger：Ctrl-F2

不进入函数：F8

单步、进入函数：F7

跳出函数：shift-F8

# 基础语法

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

### （6）子字符串在字符串中是否存在

关键字`in`

```python
if str_ in string_
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
ll = {'color':blue, 'price':10}
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



---

## 10. 函数

### （1）定义函数

**函数体相对于函数声明要缩进。**

**三引号部分——文档字符串，是描述函数功能的注释，用于生成程序中函数的文档。要放在函数体中，要缩进。**

每个函数都应包含简要地阐述其功能的注释，该注释应紧跟在函数定义后面，并采用文档字符串格式。

```python
def greet_user(username):
	"""显示简单的问候语"""	
	print("Hello, " + username.title() + "!")
```

```python
## 调用函数，传递实参
greet_user('jesse')
```

### （2）传递实参

```python
## 函数定义
def describe_pet(animal_type, pet_name):
	"""显示宠物的信息"""
	print("\nI have a " + animal_type + ".")
	print("My " + animal_type + "'s name is " + pet_name.title() + ".")
```

```python
## 不指定形参名称，传递实参需要按照顺序对应
describe_pet('hamster', 'harry')
```

```python
## 指定了形参的名称后，实参的顺序将不再重要，因为清晰的指定了实参赋值给哪个形参
describe_pet(pet_name='harry', animal_type='hamster')
```

### （3）形参默认值

**设定默认值的形参必须放在括号最右侧，若在设定默认值的形参右侧还有不设定默认值的形参则会报错**

```python
def describe_pet(animal_type, pet_name='33'):
```

```python
describe_pet('cat')		## 'cat' '33'
```

**若在调用函数时，显式的给设定默认值的形参赋值，则默认值将会被覆盖**

```python
describe_pet(animal_type='cat', pet_name='22')		## 'cat' '22'
```

#### 形参默认值实现实参列表可选

对于指定了默认值的形参，若不显式对其传参则使用默认值在函数体中进行处理，若显示传参则用传入的参数进行处理。

这样就可使形参中的某几个变为可选，使得传参可以传2个参数也可以传3个参数。

```python
def get_formatted_name(first_name, last_name, middle_name=''):
"""返回整洁的姓名"""
	if middle_name:
		full_name = first_name + ' ' + middle_name + ' ' + last_name
	else:
		full_name = first_name + ' ' + last_name
	return full_name.title()
musician = get_formatted_name('jimi', 'hendrix')
print(musician)
musician = get_formatted_name('john', 'hooker', 'lee')
print(musician)
```

### （4）返回值

* return 

  ```python
  def get_formatted_name(first_name, last_name, middle_name=''):
  """返回整洁的姓名"""
  	if middle_name:
  		full_name = first_name + ' ' + middle_name + ' ' + last_name
  	else:
  		full_name = first_name + ' ' + last_name
  	return full_name.title()
  ```

* 返回字典

  ```python
  def build_person(first_name, last_name, age=''):
  	"""返回一个字典，其中包含有关一个人的信息"""
  	person = {'first': first_name, 'last': last_name}
  	if age != '':
  		person['age'] = age
  	return person
  ```


### （5）传参——列表

* 传参同其他数据类型一样直接传

* 直接传入列表时传的是列表的”引用“，在函数中对列表进行修改会导致列表自身的永久改变

* 要想传列表的copy，可使用列表的切片

  ```python
  def print_models(unprinted_designs, completed_models):
  	"""
  	模拟打印每个设计，直到没有未打印的设计为止
  	打印每个设计后，都将其移到列表completed_models中
  	"""
  	while unprinted_designs:
  		current_design = unprinted_designs.pop()
  		# 模拟根据设计制作3D打印模型的过程
  	print("Printing model: " + current_design)
  	completed_models.append(current_design)	
  def show_completed_models(completed_models):
  	"""显示打印好的所有模型"""
  	print("\nThe following models have been printed:")
  	for completed_model in completed_models:
  		print(completed_model)
  ```

  ```python
  unprinted_designs = ['iphone case', 'robot pendant', 'dodecahedron']
  completed_models = []
  print_models(unprinted_designs[:], completed_models)	##取unprinted_designs列表的副本
  show_completed_models(completed_models)
  ```


### （6）接收任意数量参数——元祖

`*var`定义元祖，可接受任意数量的参数。

* 传参方式为位置参数时：

  元祖要放在函数列表最后，前面的实参对应相应的形参后，剩余所有实参放在元祖中

  ```python
  def make_pizza(size, *toppings):
  	"""概述要制作的比萨"""
  	print("\nMaking a " + str(size) +
  	"-inch pizza with the following toppings:")
  	for topping in toppings:
  	print("- " + topping)
  make_pizza(16, 'pepperoni')
  make_pizza(12, 'mushrooms', 'green peppers', 'extra cheese')
  ```

* 传参方式为关键字参数时：

  向函数传实参时用`形参名=实参`的形式，以双星号创建空字典，接收`形参名:实参`。

  ```python
  def build_profile(first, last, **user_info):
  	"""创建一个字典，其中包含我们知道的有关用户的一切"""
  	profile = {}
  	profile['first_name'] = first
  	profile['last_name'] = last
  	for key, value in user_info.items():
          profile[key] = value
  		return profile
  user_profile = build_profile('albert', 'einstein',location='princeton',field='physics')
  print(user_profile)
  ```

### （7）模块

模块——代码文件`.py`

#### 导入整个模块，然后使用`模块名.函数名`的方式使用函数。

`import`导入模块后，将读取模块中的函数并复制到调用程序中，随后就可以使用模块中的所有函数。

所有import语句都应放在程序开头。

#### 模块别名——导入整个模块

```python
import module as md		##将较长的模块名简化，便于
```

#### 导入模块中指定函数

```python
from module import function1, function2, function3
```

#### 给模块中的函数起别名——为了简化函数名称或函数名和当前程序中的函数名冲突

```python
from module import function1 as func1, function2 as func2, function3 as func3
```

#### 

---

## 11. 类

### （1）创建类

```python
class Cat():
    """这是一个描述小猫的类"""			##文档字符串，描述类功能
    def __init__(self,name,age):		##构造函数，必须要传入self用以指代实例
        self.name=name				    ##调用构造函数时，将变量name、age的值赋值给实例self的属性name和age
        self.age=age
    def eat():						   ##类中的函数称为方法
        print(self.name.title() + "is eating")	##方法内以【self.属性】的方式调用属性
    def meow():
        print(self.name.title() + "is barking")		##实际项目中方法内放描述Cat meow的实现代码
```

#### 构造函数

构造函数——`__init__()`

### （2）访问属性、调用方法

`实例.属性`的形式访问

```python
yuanzi = Dog("yuanzi", 3)	##返回一个对象
```

`实例.方法`的形式访问

### （3）修改属性

#### 直接访问属性进行修改

```python
yuanzi.age = 4		##直接访问age进行修改
```

#### 通过在类中编写函数修改属性

```python
## 在Cat类中加方法modifyAge()
def modifyAge(age):
    self.age = age
```

```python
yuanzi.modifyAge(3)
```

#### 属性默认值

```python
class Cat():
    """这是一个描述小猫的类"""			
    def __init__(self,name,age):		
        self.name=name				   
        self.age=age
        self.height = 20
```

### （4）继承

子类继承父类将自动获取父类所有的属性和方法，同时还可以对父类的方法进行覆写，从而调用方法时调用的就不再是父类的方法。

#### 构造——调用父类的构造

```python
class orangeCat(Cat):
    def __init__(name,age,weight):
        super.__init__(name,age)
        self.weight = weight						##定义子类特有的属性
    def eat():
        print("fat " + self.name + "is eating!!")	  ##重写eat方法
```

#### 将实例作为类的属性

```python
class Car():
	--snip--
class Battery():
	"""一次模拟电动汽车电瓶的简单尝试"""
	def __init__(self, battery_size=70):	## 电池默认容量70
        """初始化电瓶的属性"""
		self.battery_size = battery_size
	def describe_battery(self):			## Battery的方法
		"""打印一条描述电瓶容量的消息"""
		print("This car has a " + str(self.battery_size) + "-kWh battery.")
class ElectricCar(Car):
	"""电动汽车的独特之处"""
	def __init__(self, make, model, year):
		"""
		初始化父类的属性，再初始化电动汽车特有的属性
		"""
		super().__init__(make, model, year)
		self.battery = Battery()		## 将Battery实例作为属性
my_tesla = ElectricCar('tesla', 'model s', 2016)
print(my_tesla.get_descriptive_name())
my_tesla.battery.describe_battery()	
```

### （5）导入模块中的类

#### 导入单个类/多个类

模块`pet.py`

```python
from pet import Cat
```

```python
from pet import Cat,Dog,Bird
```

#### 导入整个模块

导入整个模块后使用类同使用函数一样，以`模块名.类名`访问

```python
import pet
myCat = Cat("yuanzi",3)
```

#### 模块A中的类依赖另一模块B中的类

需要在模块A中导入另一模块B

当前代码调用模块A中类时，不仅仅需要import模块A还需要import模块B

```python
## 模块car.py
"""一组用于表示燃油汽车和电动汽车的类"""
class Car():
	--snip--
class Battery():
	"""一次模拟电动汽车电瓶的简单尝试"""
	def __init__(self, battery_size=60):
		"""初始化电瓶的属性"""
		self.battery_size = battery_size
	def describe_battery(self):
		"""打印一条描述电瓶容量的消息"""
		print("This car has a " + str(self.battery_size) + "-kWh battery.")
	def get_range(self):
		"""打印一条描述电瓶续航里程的消息"""
		if self.battery_size == 70:
			range = 240
		elif self.battery_size == 85:
			range = 270
	message = "This car can go approximately " + str(range)
	message += " miles on a full charge."
	print(message)
```

```python
## 模块electric_car.py
## 该模块定义了2个类Battery和ElectricCar
from car import Car
class Battery():
	--snip--
class ElectricCar(Car):
	--snip--
```

```python
## 逻辑代码
from car import Car
from electric_car import ElectricCar
	my_beetle = Car('volkswagen', 'beetle', 2016)
	print(my_beetle.get_descriptive_name())
	my_tesla = ElectricCar('tesla', 'roadster', 2016)
	print(my_tesla.get_descriptive_name())
```

### （6）Python标准库

一组模块，安装的Python都包含它。可使用标准库中的任何函数和类，为此只需在程序开头包含一条简单的import语句。

### （7）类编码风格

首字母大写+驼峰

### （8）代码开发流程

事先设计好架构：多个模块，各个模块中存放相应的类与函数

一个文件先写好逻辑功能：功能实现后，再对代码进行抽取、梳理架构



---

## 12. 文本文件IO

### （1）打开文件——open

```python
with open("\\cc.txt") as file_object:
```

> file_object——文件对象
>
> with——由Python决定文件不再使用时，调用close将文件关闭

### （2）读取文本文件

#### 读取整个文本文件内容——read

我们使用方法read()（前述程序的第2行）读取这个文件的全部内容，并将其作为一个长长的字符串存储在变量contents中。

```python
with open("\\cc.txt") as file_object:	
    contents = file_object.read()
    print(contents.rstrip())		## 相比cc.txt末尾多了一个空字符串，read读到文件末尾时返回一个空字符串，用rstrip去掉
```

#### 将文件各行存在列表中——readlines

使用关键字with时， open()返回的文件对象只在with代码块内可用。如果要在with代码块外访问文件的内容，可在with代码块内将文件的各行存储在一个列表中，并在with代码块外使用该列表  

```python
with open("\\cc.txt") as file_object:	
    lines = file_object.readlines()		 ## 列表lines中存的每行末尾都有\n
for line in lines:
    print(line.rstrip())  				## 因为文件的每行末尾有一个\n，print也会在输出内容后加一个\n所有要去掉一个\n
```

### （3）写入文本文件

#### 指定打开文件方式

> "w"——写入。文件不存在时创建，若文件存在且由内容将清空重写
>
> "a"——追加写入
>
> "r"——只读。open缺省值为只读
>
> "r+"——读写

#### 写入单行——write

```python
with open(filePath, "w") as file_object:
    file_object.write("coding")
```

#### 写入多行

```python
with open(filePath, "w") as file_object:
    file_object.write("coding\n")
    file_object.write("coding\n")
```



---

## 13. 异常

### （1）try-except-else

```python
try:
    code probably error..
except Error:
    error dealing..
else:
    code run normall
```

> try——捕获可能发生错误的代码
>
> except——发生异常时的处理代码
>
> else——代码未发生异常、正常运行时的代码
>
> 异常处理——避免给用户暴露出过多的错误信息traceback，可给用户展示更友好的错误信息，避免程序信息过多的展示，向用户显示他不想看到的信息可能会降低程序的可用性。

### （2）pass

在except代码块中明确地**告诉Python什么都不要做**。 Python有一个pass语句，可在代码块中使用它来让Python什么都不要做。

pass语句还充当了**占位符**，它提醒你在程序的某个地方什么都没有做，并且以后也许要在这里做些什么。  

```python
def count_words(filename):
	"""计算一个文件大致包含多少个单词"""
	try:
		--snip--
	except FileNotFoundError:
		pass
	else:
		--snip--
	filenames = ['alice.txt', 'siddhartha.txt', 'moby_dick.txt', 'little_women.txt']
	for filename in filenames:
count_words(filename)
```



---

## 14. JSON格式存储数据

将程序结果保存在json文件中，实现数据保存、读取，不同程序之间的数据交换，不同编程语言间的数据交换。

### （1）json.dump——写入数据到json文件

```python
import json
aa = [1,2,3,4]
with open("aa.json","w") as f_obj:
    json.dump(aa,f_obj)
```

### （2）json.load——从json文件中加载数据到内存

```python
Import json
with open("aa.json") as f_obj:
    aa = json.load()
print(aa)
```

```python 
## 示例
import json
try:
    with open("username.json") as f_obj:
        username = json.load(f_obj)
except FileNotFoundError:
    print("username.json not exist")
    name = input("please enter your name:")
    with open("username.json", "w") as f_obj_w:
        json.dump(name, f_obj_w)
        print("save you name, we'll remember you next login!")
else:
    print("welcome " + username + "!!")
```

```python
## 重构
import json
def readStoredName():
    """读取json中的username，若不存在json则返回None"""
    try:
        with open("username.json") as f_obj:
            username = json.load(f_obj)
    except FileNotFoundError:
        return None
    else:
        return username
    
def inputName():
    """用户输入username，并保存到json中"""
    name = input("please enter your name:\n")
    with open("username.json","w") as file_obj_w:
        json.dump(name,file_obj_w)
    return name

## 业务逻辑
def func():
    username = readStoredName()
    if username == None:
        username = inputName()
        print("welcome " + username + "!! we'll remember next login!!")
    else:
        print("welcome " + username + "!!")

func()

```



---

<<<<<<< Updated upstream
#  游戏制作库——pygame库

## 1. 常用函数

### （1）get_rect()

![image-20220110151653929](https://s2.loli.net/2022/01/10/jrQscyVk81G7SUB.png)

### （2）surface.blit()
=======
## 2. Pygame

### （1）pygame版本

[下载符合自己Python版本的pygame](https://blog.csdn.net/shizheng_Li/article/details/114731202)

[Python下载地址](https://github.com/pygame/pygame/releases/tag/2.1.2)

>>>>>>> Stashed changes

https://blog.csdn.net/zzwlyj/article/details/82117981

https://blog.csdn.net/m0_64660514/article/details/122191114?spm=1035.2023.3001.6557&utm_medium=distribute.pc_relevant_bbs_down.none-task-blog-2~default~OPENSEARCH~default-4.nonecase&depth_1-utm_source=distribute.pc_relevant_bbs_down.none-task-blog-2~default~OPENSEARCH~default-4.nonecase

```python
# 方法旨在将一个图像绘制到另一个图像的上方
surface.blit(source，dest=None，special_flags=0)
```

> 现在主要讲解blit的各个参数使用，主要就是两个，
>
> 一个source，很好理解，就是图片。图片用load方法加载
>
> ```python
> image = pygame.image.load('不知名女友.gif')
> ```
>
> 一个是dest我们可以理解为rect，我们可以直接使用rect参数进去



---

# 数据可视化库——MatPlotLib库

## 1. 安装

pycharm——File——Settings

![](https://s2.loli.net/2022/01/25/MGn3mFwi52hPN8L.png)



---

## 2. 绘制折线图——plot

标题（文本，字体大小）、坐标轴标签（文本，字体大小）、横轴纵轴坐标

```python
import matplotlib.pyplot as plt
x = range(1,101)				   ##区间[1,101)
y = []
for i in x:
    y.append(i**2)
plt.plot(x, y, linewidth = 3)		##plot指定横轴坐标、纵轴坐标、线宽。若不指定横轴坐标，默认x将从0开始计数
plt.title("y=x^2", fontsize=24)		##图表标题，字体大小
plt.xlabel("X", fontsize=14)		##横轴标签，字体大小
plt.ylabel("Y", fontsize=14)		##纵轴标签，字体大小
plt.tick_params(axis="both", labelsize=10)	##设置坐标轴刻度样式，坐标轴刻度字体大小
plt.show()
```



---

## 3. 绘制散点图——scatter

### （1）绘制单个点

```python
x = range(0, 101)
for i in x:
    plt.scatter(i, i**2, s=50)	##s表示点的大小
plt.title("Square Numbers", fontsize=24)
plt.xlabel("X", fontsize=10)
plt.ylabel("Y", fontsize=10)
plt.tick_params(axis="both", labelsize=10)
plt.show()
```

### （2）以列表形式给出一系列点

* scatter参数直接给出2个列表，绘制离散点时分别从2个列表中依次取出1个数值作为横、纵坐标

  ```python
  x = range(0, 101)
  y = []
  for i in x:
      y.append(i**2)		##i^2
  plt.scatter(x, y, s=50)
  plt.show()
  ```

  ```python
  x = range(0, 101)
  y = [i**2 for i in x]	## 用一个列表的元素初始化另一个列表的元素
  plt.scatter(x, y, s=50)	
  plt.axis([0, 100, 0, 11000])		## axis的4个参数分别指定X轴和Y轴的最小值、最大值
  plt.show()
  ```

* 去掉小圆点的黑色边界

  ```python
  plt.scatter(x, y, edgecolor='none', s=50)
  ```

* 设定小圆点的颜色

  参数c是一个包含3个分量的元祖，3个分量分别表示RGB，范围0~1，越接近0颜色越深

  ```python
  plt.scatter(x, y, c=(0.5, 0, 0.5), edgecolor='none', s=50)
  ```

* 颜色映射

  颜色映射colormap代表一组渐变颜色，通常用颜色变化突出数据变化的规律。

  ```python
  x = range(0, 101)
  y = [i**2 for i in x]
  plt.scatter(x, y, c=y, cmap=plt.cm.Blues, edgecolors='none', s=50)
  plt.axis([0, 100, 0, 11000])
  plt.show()
  ```

![](https://s2.loli.net/2022/01/25/hlnMFgDEmi7Sxd6.png)

* 保存图表

  ```python
  ##用于将Plt.show出来的图表保存，因此要先savefig在show；否则，代码阻塞在show处无法执行后续的savefig，而关闭show窗口后保存的则是空白
  plt.savefig("scatterDemo.png", bbox_inches='tight')		##bbox_inches='tight'用于裁剪图表周围空白
  ```

  ```python
  x = range(0, 101)
  y = [i**2 for i in x]
  plt.scatter(x, y, c=y, cmap=plt.cm.Blues, edgecolors='none', s=50)
  plt.axis([0, 100, 0, 11000])
  plt.savefig("scatterDemo.png", bbox_inches='tight')
  plt.show()
  ```



---

## 4. 直方图——hist

https://blog.csdn.net/hohaizx/article/details/79101322

```python
data = numpy.random.randn(10000)
plt.hist(data, bins=40, density=True, facecolor='blue', edgecolor='black', alpha=0.7)	
plt.xlabel("gauss distribution", fontsize=20)
plt.ylabel("value", fontsize=20)
plt.show()
```

> data——数据集
>
> bins——长条形数量
>
> density——设为True表示显示频率直方图，否则是频数直方图（**normed参数已被新版本matplotlib弃用了**）

![image-20220125171934322](https://s2.loli.net/2022/01/25/PEknyGhxaLFRpQf.png)





---

## 5. 饼图——pie

```python
size = [40, 40, 20]		##各个part所占比例
explode = [0.1, 0, 0]	##part从整体中突出的程度
color = ["red", "green", "blue"]	##各个part的颜色
label = ["part1", "part2", "part3"]	##各个part的标签

plt.pie(size, explode=explode, colors=color, labels=label, labeldistance=1.1, autopct="%1.1f%%", shadow=False, startangle=90, pctdistance=0.6)
plt.axis("equal")	
plt.legend()	##显示legend题注
plt.show()
```

`pie`

> labeldistance——外部的label距离圆心的距离（半径倍数）
>
> autopct——饼图内部文本的格式
>
> shadow——饼图阴影
>
> startangle——第一个part的起始角度
>
> pctdistance——饼图内文本距离圆心距离（半径倍数）



---

## 6. 条形图——bar

```python
##条形图
labels = ["2019", "2020", "2021", "2022"]
num1 = [50, 100, 150, 200]
num2 = [10, 100, 200, 300]
a = [0, 1, 2, 3]
##绘制
rects1 = plt.bar(a, height=num1, width=0.4, alpha=0.8, color="red", label="department1")	##label是题注显示。画bar时，a的坐标表示竖条的中心点横坐标
rects2 = plt.bar([i + 0.4 for i in a], height=num2, width=0.4, alpha=0.8, color="blue", label="department2")
##设置坐标轴
plt.title("company")
plt.xlabel("year")
plt.xticks([index + 0.2 for index in a], labels)	##横轴坐标
plt.ylabel("total count")
plt.ylim(0, 400)	##纵轴取值范围
plt.legend(loc="upper left")	
##条形图上文本
#for rect in rects1:
#     height = rect.get_height()
#     plt.text(rect.get_x(), rect.get_width()/2, height + 1, str(height), ha="center", va="bottom")
plt.show()
```

```python
plt.legend([“blue”, “green”], bbox_to_anchor=(0.75, 1.15), ncol=2)  
```

> bbox_to_anchor——题注的坐标
>
> loc——题注位置
>
> ncol——题注列数



---

## 7. 随机漫步

```python
## 随机漫步
from random import choice

class RandomWalk:
    def __init__(self, totalPoints):
        self.x_values = [0]
        self.y_values = [0]
        self.totalPoints = totalPoints

    def fillWalk(self):
        while len(self.x_values) < self.totalPoints:
            xDir = choice([-1, 1])
            xStepTemp = choice([0, 1, 2, 3, 4])
            yDir = choice([-1, 1])
            yStepTemp = choice([0, 1, 2, 3, 4])

            xStep = xDir * xStepTemp
            yStep = yDir * yStepTemp
            if xStep == 0 and yStep == 0:
                continue
            xNext = self.x_values[-1] + xStep
            yNext = self.y_values[-1] + yStep
            self.x_values.append(xNext)
            self.y_values.append(yNext)

while True:
    rw = RandomWalk(500)
    rw.fillWalk()
    plt.scatter(rw.x_values, rw.y_values, s=10)
    plt.show()
    flag = input("want another walk?(y/n)")
    if flag == "n":
        print("walk over..")
        break
```

![image-20220125153659538](https://s2.loli.net/2022/01/25/9lhMqZ5GUcdFmbK.png)

```python
##隐藏坐标轴
plt.axes().get_xaxis().set_visible(False)
plt.axes().get_yaxis().set_visible(False)
```

```python
##增加颜色映射
plt.scatter(rw.x_values, rw.y_values, c=list(range(0, rw.totalPoints)), cmap=plt.cm.Blues, edgecolors='none', s=10)
```

![image-20220125160220895](https://s2.loli.net/2022/01/25/v9kcxIaK24m1S3M.png)

```python
## 突出起点和终点
while True:
    rw = RandomWalk(500)
    rw.fillWalk()
    plt.scatter(0, 0, c="green", edgecolors="none", s=100)	## 突出起点
    plt.scatter(rw.x_values[-1], rw.y_values[-1], c="red", edgecolors="none", s=100)	## 突出终点
    plt.scatter(rw.x_values, rw.y_values, c=list(range(0, rw.totalPoints)), cmap=plt.cm.Blues, edgecolors='none', s=10)
    plt.show()
    flag = input("want another walk?(y/n)")
    if flag == "n":
        print("walk over..")
        break
```

![image-20220125160837632](https://s2.loli.net/2022/01/25/chXUFoNtBfTn48i.png)

```python
## 设置窗口大小、颜色
while True:
    rw = RandomWalk(500000)
    rw.fillWalk()
     plt.figure(dpi = 128, figsize=(10, 6), facecolor='red', edgecolor='yellow')		##dpi——窗口dpi，figsize——尺寸（单位：英寸）
    plt.scatter(0, 0, c="green", edgecolors="none", s=100)
    plt.scatter(rw.x_values[-1], rw.y_values[-1], c="red", edgecolors="none", s=100)
    plt.scatter(rw.x_values, rw.y_values, c=list(range(0, rw.totalPoints)), cmap=plt.cm.Blues, edgecolors='none', s=1)
    plt.show()
    flag = input("want another walk?(y/n)")
    if flag == "n":
        print("walk over..")
        break
```

> https://blog.csdn.net/black_shuang/article/details/81299200
>
> ```python
> matplotlib.pyplot.figure(num=None, figsize=None, dpi=None, facecolor=None, edgecolor=None, frameon=True, FigureClass=<class 'matplotlib.figure.Figure'>, clear=False, **kwargs)
> ```
>
> num——窗口身份标识
>
> figsize——窗口尺寸，英寸
>
> dpi——分辨率
>
> facecolor——窗口背景颜色（不是图表颜色）
>
> edgecolor——窗口边框颜色



---

## 8. CSV文件数据可视化

### （1）CSV文件

CSV（逗号分隔值文件），其文件以纯文本形式存储表格数据，内容是字符序列不含像二进制数据那样需要被解读的数据。CSV文件由任意数目的记录组成，**记录间以某种换行符分**隔；每条记录由字段组成，**字段间的分隔符是**其它字符或字符串，最常见的是**逗号或制表符**。

### （2）显示CSV文件

```python
import csv
```

```python
with open(filepath) as f:
    ...
```

```python
import csv
from datetime import datetime
filename = 'C:\\Users\\eivision\\Desktop\\222.csv'	
with open(filename) as f:	##打开文件作为文件对象f
    reader = csv.reader(f)	##创建和文件对象f关联的阅读器对象
    header_row = next(reader)	##next返回文件一行，并指向下一行
    for index, colIndex in enumerate(header_row):
        print(index, colIndex)

    date = []
    temperature = []
    for row in reader:	##因为上面用next，所以这里从第2行开始
        # curDate = datetime.strptime(row[0], "%Y/%m/%d")
        # date.append(curDate)
        date.append(row[0])
        temperature.append(int(row[1]))		##将纵坐标转为int类型，否则为字符串的话纵坐标将不按照顺序
    fig = plt.figure(dpi=128, figsize=(10, 6))	##窗口设置
    plt.plot(date, temperature, c="green")
    plt.title("csv graph", fontsize=24)
    plt.xlabel("date", fontsize=10)
    plt.ylabel("temperature ℃", fontsize=10)
    plt.ylim(0, 40)
    plt.tick_params(axis="both", which="major", labelsize=16)	##坐标轴刻度设置
    fig.autofmt_xdate()		##让x轴的标签斜着排，不会互相遮挡
    plt.show()
    print(temperature)
    print(date)
```

> enumerate——将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，**同时列出数据和数据下标**
>
> ```python
> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
> list(enumerate(seasons))
> >>> [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
> list(enumerate(seasons, start=1))       # 下标从 1 开始
> >>> [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
> ```
>
> 

### （3）从CSV中读取时间

将从CSV文件中读取的字符串转成指定格式的**日期**对象

```python
from datetime import datetime
cur_date = '2014-07-07'
current_date = datetime.strptime(cur_date, '%Y-%m-%d')
```

### （4）着色填充min-max之间曲线

```python
matplotlib.pyplot.fill_between(X, Yh, Yl, facecolor ,alpha)
```

> X——横坐标向量
>
> Yh——纵坐标最大值向量
>
> Yl——纵坐标最小值向量
>
> facecolor——填充曲线中间区域的颜色
>
> alpha——颜色透明度

### （5）CSV文件中数据缺失使用异常处理

若row[0]、row[1]、row[2]中有数据不存在，则捕获到valueError

```python
try:
	current_date = datetime.strptime(row[0], "%Y-%m-%d")
	high = int(row[1])
	low = int(row[3])
except ValueError:
	print(current_date, 'missing data')
else:
	dates.append(current_date)
	highs.append(high)
	lows.append(low)
```



---

# 数据可视化库——pygal库

##  1. JSON文件数据可视化

python操作json文件通过了两种方法：load()：用于读取json文件；dump()：用于写入json文件 

```python
# 读取json文件内容,返回字典格式
with open('./source_file/info.json','r',encoding='utf8')as fp:
    json_data = json.load(fp)
    print('这是文件中的json数据：',json_data)
    print('这是读取到文件数据的数据类型：', type(json_data))
```

```python
# 将字典数据写入到json文件中
dict1 = {'name': '张三', 'age': 18, 'sex': '男'}
with open('./source_file/info.json','a',encoding='utf8')as fp:
    json.dump(dict1,fp,ensure_ascii=False)　　
 #  如果ensure_ascii ' '为false，则返回值可以包含非ascii值
```



## 2. WebAPI

#### Github网站API

```python
# 调用GitHub的WebAPI获取数据
import requests

url = 'https://api.github.com/search/repositories?q=language:python&sort=stars'
r = requests.get(url)
print("status code = ", r.status_code)  # 状态码

response_dict = r.json()    # 返回结果（json），通过json()转化为字典
print("total repositories = ", response_dict['total_count'])    # 所有仓库数

repos = response_dict['items'] # 返回字典的列表
print('returned repositories = ', len(repos))   # 返回的仓库数

repo_first = repos[0]   # 第一个仓库（字典）
print(repo_first)
print('repo_first_len = ', len(repo_first))     # 第一个仓库中Key的个数
for key in sorted(repo_first.keys()):
    print(key)      # 打印第一个仓库的每个key

# 输出第一个仓库的一些基本信息
print('name of first repo = ', repo_first['name'])
print("owner of first repo = ", repo_first['owner']['login']) # repo_first['owner']仍然是字典
print('starts = ', repo_first['stargazers_count'])
print('created_date = ', repo_first['created_at'])
print('repository = ', repo_first['html_url'])
print('updated_date = ', repo_first['updated_at'])
print('description = ', repo_first['description'])

# 使用pygal可视化上述获取所有仓库信息
import pygal
from pygal.style import LightColorizedStyle as LCS, LightenStyle as LS

repo_names = []
repo_stars = []
for repo in repos:
    repo_names.append(repo['name'])
    repo_star = {'value': repo['stargazers_count'],
                 'label': repo['description'],
                 'xlink': repo['html_url']
                 }   
    # value作为纵坐标值，label是鼠标移动到bar时的工具提示，xlink用于给各个bar添加可点击的链接
    repo_stars.append(repo_star)

my_style = LS('#333366', base_style=LCS)    # 样式
my_config = pygal.Config()          # 参数
my_config.x_label_rotation = 45     # 横轴label旋转45度
my_config.show_legend = True        # 图表左上方的图例是否显示
my_config.title_font_size = 24      # 标题字体大小
my_config.label_font_size = 14      # 副标签字体大小
my_config.major_label_font_size = 18    # 主标签字体大小（）
my_config.truncate_label = 15       # 标签字符较长时缩短到15个字符
my_config.show_y_guides = False     # 不显示水平虚线
my_config.width = 1000              # 自定义图表在浏览器中显示的宽度

chart = pygal.Bar(my_config, style = my_style)
chart.title = 'projects on github sorted by stars'  # 标题
chart.x_labels = repo_names
chart.add('各项目的star数', repo_stars)
chart.render_to_file('C:\\Users\\25224\\Desktop\\github_repos.svg')     # 导出到svg文件，浏览器打开
```

> requests库解析网站数据，返回数据是json，通过json()转化为字典
>
> 纵轴数据源repo_stars是字典的列表，字典有3个key
>
> > value——bar的纵轴值
> >
> > label——鼠标移动到bar上时的描述
> >
> > xlink——鼠标单击时跳转的url

#### [BiliBili网站API](https://pypi.org/project/bilibili-api/#description)





































