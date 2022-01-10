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

#  pygame

## 1. 常用函数

### （1）get_rect()

![image-20220110151653929](https://s2.loli.net/2022/01/10/jrQscyVk81G7SUB.png)

### （2）surface.blit()

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







































