一种文本规范。

可以方便的表示树形数据。

更简洁、对人友好，适合数据量不大结构不复杂的数据。

XML则程序处理更方便，对机器友好。适合数据量大、结构复杂的数据。

本质是一个字符串，程序中的使用往往**需要和对象相互转化。**

不同编程语言对json的原生支持不同：

* Python原生支持json：`import json`
* C++原生不支持：需导入Jsoncpp库

## 数据类型

字符串：双引号

数字：整数、浮点数

布尔值：true、false

null

字典（哈希表）：{}键值对，key必须是字符串，value可是6中类型中任意一种。

```json
{"key":value}
```

列表：[]

```json
["aaa","bbb","ccc"]
```

## 应用场景

常用在接口参数、返回结果、配置文件

![image-20240310202758097](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240310202758097.png?token=ANG32YBRJW4OXC7OZXVECATF5WT5S)