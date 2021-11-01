# 问题总结

## 1. 获取系统时间

https://blog.csdn.net/u012326462/article/details/82081756

## 2. INSERT INTO指令中赋值字符串用单引号

## 3. Windows安装

> * https://www.runoob.com/mysql/mysql-install.html
>
>   #### MySQL版本为8+不需要配置my.ini中的datadir
>
> * root密码为空时，设置root密码
>
>   https://blog.csdn.net/fengzhantian/article/details/90213785
>
> * 配置环境变量
>
>   https://blog.csdn.net/wd2011063437/article/details/78793917
>
> * 修改root密码

## 4. 不是只有主键可以自增，给非主键设置自增时需要设成唯一主键

https://www.cnblogs.com/mm20/p/8060425.html

PRIMARY KEY (主键的列不能为空值)



## 5.批量插入大量数据

#### 通过INSERT INTO插入

```mysql
INSERT INTO device_config(
`LineNO`, 
`DeviceStatus_001`, 
`DeviceStatus_002`, 
`DeviceStatus_003`, 
`DeviceStatus_004`, 
`DeviceStatus_005`, 
`DeviceStatus_006`,
`DeviceStatus_007`,
`DeviceStatus_008`,
`DeviceStatus_009`,
`DeviceStatus_010`,
`DeviceStatus_011`,
`DeviceStatus_012`,
`DeviceStatus_013`,
`DeviceStatus_101`,
`DeviceStatus_102`,
`DeviceStatus_103`,
`DeviceStatus_104`,
`DeviceStatus_105`
)
VALUES
('001','1','1','1','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0'),
('002','0','0','0','1','1','1','0','0','0','0','0','0','0','0','0','0','0','0'),
('003','0','0','0','0','0','0','1','1','1','0','0','0','0','0','0','0','0','0'),
('004','0','0','0','0','0','0','0','0','0','1','1','1','0','0','0','0','0','0'),
('005','0','0','0','0','0','0','0','0','0','0','0','0','1','1','1','0','0','0'),
('006','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','1','1','1'),
('007','1','1','1','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0'),
('008','0','0','0','1','1','1','0','0','0','0','0','0','0','0','0','0','0','0'),
('009','0','0','0','0','0','0','1','1','1','0','0','0','0','0','0','0','0','0'),
('010','0','0','0','0','0','0','0','0','0','1','1','1','0','0','0','0','0','0'),
('011','0','0','0','0','0','0','0','0','0','0','0','0','1','1','1','0','0','0'),
('012','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','1','1','1'),
('013','1','1','1','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0'),
('101','0','0','0','1','1','1','0','0','0','0','0','0','0','0','0','0','0','0'),
('102','0','0','0','0','0','0','1','1','1','0','0','0','0','0','0','0','0','0'),
('103','0','0','0','0','0','0','0','0','0','1','1','1','0','0','0','0','0','0'),
('104','0','0','0','0','0','0','0','0','0','0','0','0','1','1','1','0','0','0'),
('105','0','0','0','0','0','0','0','0','0','0','0','0','0','0','0','1','1','1');
```

#### 通过Excel导入

https://www.cnblogs.com/vuciao/p/10586766.html

![image-20211028104242895](https://i.loli.net/2021/10/28/UwR8tMgOQ7ZEbDf.png)

![image-20211028104323228](https://i.loli.net/2021/10/28/UNGSHleYkIh5z8x.png)

![image-20211028104346101](https://i.loli.net/2021/10/28/f4MvY7pnSdWjuKN.png)

![image-20211028104425922](https://i.loli.net/2021/10/28/3DPeS6nsKoHZ4cl.png)



![image-20211028104532953](https://i.loli.net/2021/10/28/fgLslb7o3XAdIRu.png)

#### 通过代码+事务插入

https://www.jianshu.com/p/63bd412cb030

#### 通过脚本插入

https://www.jb51.net/article/163001.htm



## 6. 关于反引号

反引号一般用于把字段名、表名括起来，但一般来说字段名、表名若不会与关键字混淆，就不需要加。

---

# MySQL基本知识

## 1.基本概念

* MySQL是一个C/S型DBMS，user通过客户端访问服务器mysqld.exe，服务器软件副本可在本机也可在服务器上。

  常用客户端有GUI的也有CLI的，如cmd中的mysql、Navicat等。在cmd中输入mysql调用CLI形式的Client软件，直接输入mysql可能报错，因为该CLI客户端软件没有用户名和密码来连接服务器。

  ```mysql
  mysql -hlocalhost -p3036 -uroot -p 	//输入用户名和密码，调用CLI，连接服务器
  ```

* 注释

  单行：--空格，或#

  多行/**/

## 2.SHOW命令

```mysql
show databases;	//显示所有数据库
show tables;	//显示所有表
desc t_name;	//显示表结构
show create database db_name;	//显示数据库db_name创建时的信息
show create table t_name;	//显示表t_name创建时的信息
```

## 3.检索数据

* 检索出来的原始数据需要应用程序规定显示的格式

* 单列

  ```mysql
  SELECT filed_name FROM t_name;
  ```

* 多列

  ```mysql
  SELECT filed_name1,field_name2 FROM t_name;
  ```

* 所有列

  ```mysql
  //通配符
  //一般在不知道字段名称时用于显示
  SELECT * FROM t_name;
  ```

* 非主键的字段显示存在值重复的情况

  字段若不是主键，则表有多少行就会显示多少行，存在重复问题。

  **使用DISTINCT，只显示该字段的不同值。**

  ```mysql
  SELECT DISTINCT filed_name FROM t_name;
  ```

* 限制显示的行数

  ```mysql
  SELECT filed_name FROM t_name LIMIT n;	//从0行开始显示n行
  SELECT filed_name FROM t_name LIMIT n,m;	//从n行开始显示m行,行编号从0开始
  ```

* 完全限定名

  ```mysql
  SELECT t_name.filed_name FROM db_name.t_name;
  //使用.标识字段所在表和表所在数据库
  ```

* 排序数据

  (1) ORDER BY

  可以按照查询的字段进行排序，也可按照未查询的字段进行排序。

  按多个字段排序，当第一个字段相同时，才会在第一个字段相同的行内部按照第二个字段进行排序。若所有行第一个字段都相同，不会按照第二个字段排序。

  ```mysql
  SELECT field_name1,field_name2,field_name3 FROM t_name ORDER BY field_name1,field_name2;
  ```

  (2) 默认升序，DESC设置降序

  ```mysql
  SELECT field_name1,field_name2,field_name3 FROM t_name ORDER BY field_name1 DESC;
  ```

  (3) DESC只针对关键字前紧邻的字段有效。其他未用DESC修饰的字段仍然以升序排序。

  ```mysql
  SELECT field_name1,field_name2,field_name3 FROM t_name ORDER BY field_name1 DESC,field_name2;
  //field_name1按照降序排序，field_name1相同的按照field_name2升序排序
  ```

  (4) 若想对多个字段降序排序，每个字段都需要DESC修饰

* 语句顺序

  SELECT => FROM => ORDER BY => LIMIT


## 4. 过滤数据

* 通过WHERE子句指定过滤条件

  为什么要在数据库中指定过滤，而不通过应用层程序过滤：处理数据库的工作会极大影响应用程序的性能，且服务器需要发送大量无用数据占用带宽。

* WHERE子句在ORDEY BY之前，LIMIT在最后。

* 过滤条件

  ```mysql
  =
  !=
  <
  <=
  >
  >=
  BETWEEN val1 AND val2	//范围：[val1,val2]
  IN (val1,val2,val3,val4)	//列举
  ```

* 创建表时，可指定某一个字段是否可以不包含值。当一个列不含值时，为包含空值NULL

  ```mysql
  //空值检查，与字段包含0、空字符串、空格不同。
  WHERE field_name IS NULL;
  ```

*  用AND/OR连接多条WHERE语句。用()明确分组过滤条件，避免歧义。

  ```mysql
  SELECT field_name1,field_name2,field_name3 FROM t_name 
  WHERE field_name1=val1 AND field_name2<=val2;
  ```

* IN(val1,val2,val3,...)指定WHERE语句中字段的所需取值列表。需要取表中某些指定的字段的行。

  IN子句中可以包含SELECT子句。

* NOT

  可以容易的过滤出不符合给定条件的行。
  
  ```mysql
  NOT IN
  NOT BETWEEN
  ```


* LIKE模式，使用通配符进行过滤

  LIKE谓词：在子句中使用LIKE表明子句中使用的搜索模式，是通配符匹配而不是相等匹配。

  ```mysql
  SELECT field_name1,field_name2,field_name3 FROM t_name 
  WHERE field_name1 LIKE 'jet%';
  ```
  
  %可在搜索模式中任意位置，区分大小写，可有多个。可匹配0/1/多个字符。
  
  注意：待匹配字符串末尾有空格，需要用函数去掉首尾空格。
  
  _只能匹配一个字符
  
  通配符速度慢，不要滥用，尽量不把通配符放在字符串开头。
  
  ```mysql
  -- 24、查询id、货品名称、分类编号，零售价大于等于80并且货品名称匹配'%罗技M1_ _'：
  SELECT id,productName,dir_id,salePrice FROM product WHERE salePrice>=80 AND productName LIKE '%罗技M1__'
  ```

## 5.正则表达式

* REGEXP模式表示后面跟着的是正则表达式

* 表示匹配任意一个字符：.

* 匹配或关系的若干个串： |

* 匹配集合中的单一字符，集合：[c1c2c3...]，表示匹配c1或c2或c3。

  ```mysql
  [^c1c2c3]	//匹配除c1c2c3之外的所有字符
  [0-9]
  [a-z]	//表示范围
  ```

* 转义：

  \\

```mysql
\\.	  表示匹配.
\\\   表示匹配\
```

* 字符含义

  https://www.cnblogs.com/kuqs/p/5727409.html

```mysql
\bbuckets\b  表示两侧都是词界，只匹配buckets单词
^buckets$    同上，只匹配字符串buckets
```

正则练习网页：https://alf.nu/RegexGolf

* REGEXP '1000'和LIKE '1000'的区别

  对于'a1000a'，后者不会匹配，前者会匹配。前者相当于LIKE'%1000%'。后者相当于REGEXP '%1000$'。

* MySQL的正则不支持\w,\s,\d

* 练习

  https://blog.csdn.net/qq_28633249/article/details/77686976

## 6.默认值约束

* DEFAULT用于约束字段，当字段未输入值时，自动添加一个预设的值。

  一般用于NOT NULL，防止未输入值导致出错。

  http://c.biancheng.net/view/2447.html

  ```mysql
  mysql> CREATE TABLE tb_dept3
      -> (
      -> id INT(11) PRIMARY KEY,
      -> name VARCHAR(22),
      -> location VARCHAR(50) DEFAULT 'Beijing'
      -> );
      mysql>INSERT INTO tb_name
      ->(id,name)				
      ->VALUES
      ->(1,'wang');
      //某个字段是默认值时，插入字段中默认字段不写
  ```

* 修改表时添加默认约束

  ```mysql
  ALTER TABLE <数据表名>
  CHANGE COLUMN <字段名> <数据类型> DEFAULT <默认值>;
  ```

  

## 7.非空约束

http://c.biancheng.net/view/2448.html

* 字段不指定时，默认为可为NULL

* 删除NOT NULL约束

  ```mysql
  ALTER TABLE <数据表名>
  CHANGE COLUMN <字段名> <字段名> <数据类型> NULL;
  ```

* NULL相等不用=，用IS

  ```MYSQL
  SELECT id,productName,dir_id FROM product WHERE dir_id IS NULL
  ```

  
  
  ```mysql
  SELECT id,productName,dir_id FROM product WHERE dir_id IS NOT NULL
  ```
  
  

## 8.常用指令

#### 库操作

```mysql
CREATE DATABASE db_name；	//增
DROP DATABASE db_name;		//删
ALTER DATABASE db_name;		//改
SHOW DATABASES;			    //查
USE db_name;	//使用库
SELECT DATABASE();	//当前使用的库
```

#### 表操作

```mysql
//增
CREATE TABLE IF NOT EXISTS runoob_tbl(
   `runoob_id` INT UNSIGNED NOT NULL AUTO_INCREMENT,	--默认非NULL，自增
   `runoob_title` VARCHAR(100) NOT NULL,				--默认非NULL
   `runoob_author` VARCHAR(40) DEFAULT NULL,			--默认值NULL
   `submission_date` DATE	DEFAULT NULL,
   PRIMARY KEY ( `runoob_id` )
)ENGINE=InnoDB AUTO_INCREMENT=10 DEFAULT CHARSET=utf8;
```



```mysql
//删
DROP TABLE runoob_tbl;	//表被删除
TRUNCATE TABLE runoob_tb1;	//表还在，但是表中记录都被清空
```



```mysql
//改
ALTER TABLE tb_name ADD COLUMN `newField` typename(n) NOT NULL DEFAULT '...' COMMENT '...' AFTER field_;
ALTER TABLE tb_name DROP COLUMN field_;
ALTER TABLE tb_name MODIFY COLUMN field_ typename(n) NOT NULL DEFAULT '' COMMENT AFTER field_pre;
ALTER TABLE tb_name CHANGE COLUMN field_old field_new typename(n) NOT NULL DEFAULT '' COMMENT '' AFTER field_;
```

用Navicat修改表结构：

![image-20211029151207636](https://i.loli.net/2021/10/29/2RNHGFxpJOsoS7l.png)

![image-20211029151249940](https://i.loli.net/2021/10/29/m8u1ctxGMQgAf2I.png)

#### 主键字段的值不能重复

```mysql
//查
SHOW TABLES;	//显示当前库中所有的表
```

#### 字段操作

```mysql
//插入记录
INSERT INTO tb_name (field1,field2) VALUES (val11,val12),(val21,val22),(val31,val32);		//若每个字段都按顺序赋值则可省略字段名，若有字段被省略，则需要将赋值的字段写出来
//删除记录
DELETE FROM tb_name [WHERE clause];
//修改某条记录的某个字段的值
UPDATE tb_name SET `field_name` = newVal;
//显示某个字段
SELECT `field_name1` FROM tb_name; 
```

* MODIFY和CHANGE的区别：

  https://www.cnblogs.com/mentalidade/p/6293200.html

  MODIFY无法修改字段的名称，CHANGE可以。

* COMMENT

  给字段添加注释

* AFTER

  标记当前新增/修改的字段位于某一字段后。

* 小结：

  库/表：CREATE/DROP/ALTER/SHOW，且后要加上DATABASE/TABLE表明是库还是表

  字段：INSERT INTO/DELETE FROM/UPDATE SET/SELECT FROM，且不要加上TABLE关键字。

#### 获取表的所有字段名

```mysql
select COLUMN_NAME from information_schema.COLUMNS where table_name = 'your_table_name' and table_schema = 'your_db_name';  
```



## 9.函数

### （1）拼接计算字段

concat(field1, '(', field2 ,')')

' '中的字符将直接拼接在字段上

```MYSQL
SELECT t1.DeviceNO,t2.DeviceName,
(CASE WHEN t1.DeviceStatus=1 THEN '正常'
WHEN t1.DeviceStatus=0 THEN '异常'
WHEN t1.DeviceStatus=-1 THEN '无效'
END) AS DeviceStatus,
t1.TestingNum,t1.DefectNum,
CONCAT(t1.CPUTemperature,'℃') AS CPUTemperature,CONCAT(t1.CPUUsage,'%') AS CPUUsage,CONCAT(t1.MemoryUsage,'%') AS MemoryUsage
FROM device_info AS t1 INNER JOIN device AS t2
ON t1.DeviceNO=t2.DeviceNO
WHERE t1.LineNO='001'
ORDER BY t1.`NO`;
```

### （2）去掉空格

Trim(field)	//去掉field左右两侧的空格

LTrim(field)	//去掉左侧的空格

RTrim(field)	//去掉右侧的空格

### （3）别名

AS 后跟字符串单引号

```mysql
SELECT CONCAT(`id`,'(',`name`,')') AS 'result'
FROM `student`
WHERE `department`='中文系'
ORDER BY `id`;
```

### （4）计算字段

不仅已有字段可以作为字段由SELECT显示，已有字段的计算表达式也可，还可用AS取一个别名。

```mysql
SELECT prod_id,prod_quantity,prod_price,
prod_quantity*prod_price AS 'total price'
FROM orderitems 
WHERE order_num=20005;
```

```mysql
SELECT LineNO,
(
DeviceStatus_001 +
DeviceStatus_002 +
DeviceStatus_003 +
DeviceStatus_004 +
DeviceStatus_005 +
DeviceStatus_006 +
DeviceStatus_007 +
DeviceStatus_008 +
DeviceStatus_009 +
DeviceStatus_010 +
DeviceStatus_011 +
DeviceStatus_012 +
DeviceStatus_013 +
DeviceStatus_101 +
DeviceStatus_102 +
DeviceStatus_103 +
DeviceStatus_104 +
DeviceStatus_105
)AS DeviceTotalNum
FROM device_config;
```



### （5）日期时间

养成对应含有日期时间的字段，若需要时间则加上TIME()，若需要日期则加上DATE()的习惯。

* Now()

  当前年月日时分秒，YYYY-MM-DD格式

* Date()

  日期时间中的日期

* Time()

  日期时间中的时间

* Year()

  日期时间中的年份，年份一定要使用4位YYYY避免歧义

* Month()

  日期时间中的月份

* curdate()

  当前日期

* curtime()

  当前时分秒

* Year(Now())

  当前年份

* Year(Now())-age

  已知年龄age，获得出生年份

* Year(Now())-birth

  已知出生年份birth，获得年龄

```mysql
//查询2020年9月所有订单
SELECT `id`,`name` 
FROM orders
WHERE Date(order_date) BETWEEN '2020-9-01' AND '2020-9-30';	//需要知道月中有多少天
```

```mysql
SELECT `id`,`name` 
FROM orders
WHERE Year(order_date)=2020 AND Month(order_date)=9;		
```

### （6）聚合函数

处理一组数据，返回一个值的函数

* AVG()

  显示某一个字段/列的均值，跳过值为NULL的对象。

  可用WHERE子句过滤出符合条件的所有对象的某一列的均值。
  
  求均值的列的字段作为函数参数。

```mysql
SELECT AVG(prod_price) AS 'result'
FROM products
WHERE `prod_id` IN(1001,1002,1003);
```

* MIN()/MAX()

  求**某列**的最小值/最大值

* COUNT()

  COUNT(*) 求整张表的列数，不忽略NULL

  COUNT() 对某一列计数时，**忽略NULL的记录**。

* SUM()

  求某列记录之和，忽略NULL

  #### 对一行多列求和

  ```mysql
  SELECT LineNO,
  (
  DeviceStatus_001 +
  DeviceStatus_002 +
  DeviceStatus_003 +
  DeviceStatus_004 +
  DeviceStatus_005 +
  DeviceStatus_006 +
  DeviceStatus_007 +
  DeviceStatus_008 +
  DeviceStatus_009 +
  DeviceStatus_010 +
  DeviceStatus_011 +
  DeviceStatus_012 +
  DeviceStatus_013 +
  DeviceStatus_101 +
  DeviceStatus_102 +
  DeviceStatus_103 +
  DeviceStatus_104 +
  DeviceStatus_105
  )AS DeviceTotalNum
  FROM device_config;
  ```

  #### 统计一行中

* 搭配DISTINCT使用，忽略列中的重复记录

  ```mysql
  SELECT AVG(DISTINCT prod_price) AS 'result'
  FROM products;	//求prod_price的记录的均值
  ```

* 显示多个函数值

  ```mysql
  SELECT COUNT(*) AS `total_num`,
  AVG(prod_price) AS `avg_price`,
  SUM(prod_price) AS `total_price`
  FROM products;
  ```

## 10.分组

### （1）GROUP BY

**一般，GROUP BY搭配聚合函数使用。SELECT字段中非使用聚合函数的列，必须要出现在GROUP BY后**

https://blog.csdn.net/qq_28384753/article/details/52045995

只能选择SELECT选中的字段作为分组依据。若不指定排序的依据，则会按照GROUP BY的字段排序。（ORDER BY则可选择为选中字段作为排序依据。

```mysql
SELECT `prod_id`,COUNT(*) AS `prod_num`	//显示prod_id和prod_num
FROM products						  //数据源
GROUP BY `prod_id`;					   //从products中按照prod_id分组	
```

（特别的，若计算字段不是聚合函数，而是非GROUP BY后字段的普通字段，则**显示一组中的符合过滤条件的第一条记录**）

### （2）HAVING

**必须搭配GROUP BY和聚合函数使用**，以组为单位进行过滤。（WHERE过滤行）

WHERE在分组前对行进行过滤，HAVING则是在分组后**通过聚合条件**过滤组记录。

WHERE控制那些行进入聚集计算，HAVING总是包含聚集函数通过聚集函数进行组的筛选。

```mysql
SELECT `prod_id`,COUNT(*) AS `prod_num`	//显示prod_id和prod_num
FROM products						  //数据源
GROUP BY `prod_id`
HAVING COUNT(*)>=2;					  //按照每个组的总数对组进行过滤，将总数>=2的组过滤出来。不用别名										//`prod_num`		
```

### （3）ORDER BY

用GROUP BY/HAVING过滤后，显示，注意用ORDER BY排序。

```mysql
SELECT `prod_id`,COUNT(*) AS `prod_num`	//显示prod_id和prod_num
FROM products						  //数据源
GROUP BY `prod_id`
HAVING COUNT(*)>=2;
ORDER BY `prod_num`;                    //按照prod_num排序
```

### （4）子句的顺序

SELECT->FROM->WHERE->GROUP BY->HAVING->ORDER BY->LIMIT

![image-20211026085535132](https://i.loli.net/2021/10/26/Cwy6hxOYGn3IiZN.png)

## 11.子查询（嵌套查询）

https://www.cnblogs.com/xiaoxi/p/6734025.html

https://blog.csdn.net/weixin_35782148/article/details/113089077

https://www.cnblogs.com/zhuiluoyu/p/5822481.html

一个查询的结果作为另一个查询的条件

查询可以嵌套，子句中的查询叫子查询，相对的是外查询/主查询。

嵌套查询，先执行子查询，再执行主查询，从内到外。

子查询用括号。

**一般在子查询中，程序先运行在嵌套在最内层的语句，再运行外层。因此在写子查询语句时，可以先测试下内层的子查询语句是否输出了想要的内容，再一层层往外测试，增加子查询正确率。否则多层的嵌套使语句可读性很低。**

* 分类

  标量子查询：返回单一值的标量，最简单的形式。

  列子查询：返回的结果集是 N 行一列。

  行子查询：返回的结果集是一行 N 列。

  表子查询：返回的结果集是 N 行 N 列。 

* 操作符

   可以使用的操作符：= > < >= <= <> ANY IN SOME ALL EXISTS  

### （1）子查询作为过滤条件

#### 子查询的结果是一个值

​	SELECT 查询字段 FROM 表 **WHERE 字段=（**子查询**）;** 

#### 子查询结果是多行单列的时候

​	SELECT 查询字段 FROM 表 **WHERE 字段 IN （子查询）** 

#### 子查询的结果是多行多列

​	**子查询结果是多行多列的临时表**，肯定接在FROM后

​    SELECT 查询字段 FROM （子查询） **表别名 WHERE 条件**

​    **子查询作为表必须取别名，否则这张表没有名称则无法访问表中的字段** 。注意：用临时表的别名引用字段时，反引号括住字段名，临时表名不要括住。

```mysql
 SELECT `goods_id`,`goods_name`,`cat_id`,`shop_price`,tmp.`total` FROM
(SELECT `goods_id`,`goods_name`,`cat_id`,`shop_price`,COUNT(`goods_id`) AS 'total' FROM goods ORDER BY `cat_id` ASC,`goods_id` DESC) AS tmp
GROUP BY `cat_id`;
```

[![g3FkUH.png](https://z3.ax1x.com/2021/05/07/g3FkUH.png)](https://imgtu.com/i/g3FkUH)

[![g3Fe2t.png](https://z3.ax1x.com/2021/05/07/g3Fe2t.png)](https://imgtu.com/i/g3Fe2t)

* Q：主查询中未使用聚合函数，却使用GROUP BY。按照cat_id分组，查询goods_id,goods_name,cat_id,shop_price等字段，为什么每个cat_id只显示一条记录（cat_id分组下的第一条记录）？

  不规范的用法？？？

#### with as将临时表作为变量再操作

**MySQL8.0以上版本的特性，版本低于8.0使用该语法将会显示错误**

```mysql
SELECT a.name FROM 
(
SELECT name, gender FROM employee
)AS t1
WHERE t1.gender='man';

-- 使用with as可写成：
WITH t1 AS(
SELECT name,gender FROM emplyee
)
SELECT t1.gender='man';
```

```mysql
WITH t1 AS
(
SELECT 
LineNO,
DeviceStatus_001 + 
DeviceStatus_002 +
DeviceStatus_003 +
DeviceStatus_004 +
DeviceStatus_005 +
DeviceStatus_006 +
DeviceStatus_007 +
DeviceStatus_008 +
DeviceStatus_009 +
DeviceStatus_010 +
DeviceStatus_011 +
DeviceStatus_012 +
DeviceStatus_013 +
DeviceStatus_101 +
DeviceStatus_102 +
DeviceStatus_103 +
DeviceStatus_104 +
DeviceStatus_105 AS `DeviceTotalNum`
FROM device_config
)
SELECT t1.LineNO,t2.LineName,t1.DeviceTotalNum
FROM t1 INNER JOIN device AS t2
ON t1.LineNO=t2.LineNO;
```







### （2）子查询作为计算字段

T1-3



### （3）操作符

 ANY（SOME）, ALL, IN, EXISTS 

### (4)练习

https://blog.csdn.net/weixin_45664854/article/details/105498760

#### T1

```mysql
drop database if EXISTS mydb;
create database mydb;
use mydb;
-- 子查询
CREATE TABLE emp(
	empno		INT,
	ename		VARCHAR(50),
	job		VARCHAR(50),
	mgr		INT,
	hiredate	DATE,
	sal		DECIMAL(7,2),		-- 2表示小数的位数为2,7表示整数位数+小数位数不能超过7
	comm		DECIMAL(7,2),
	deptno		INT
) ;
INSERT INTO emp VALUES(7369,'SMITH','CLERK',7902,'1980-12-17',800,NULL,20);
INSERT INTO emp VALUES(7499,'ALLEN','SALESMAN',7698,'1981-02-20',1600,300,30);
INSERT INTO emp VALUES(7521,'WARD','SALESMAN',7698,'1981-02-22',1250,500,30);
INSERT INTO emp VALUES(7566,'JONES','MANAGER',7839,'1981-04-02',2975,NULL,20);
INSERT INTO emp VALUES(7654,'MARTIN','SALESMAN',7698,'1981-09-28',1250,1400,30);
INSERT INTO emp VALUES(7698,'BLAKE','MANAGER',7839,'1981-05-01',2850,NULL,30);
INSERT INTO emp VALUES(7782,'CLARK','MANAGER',7839,'1981-06-09',2450,NULL,10);
INSERT INTO emp VALUES(7788,'SCOTT','ANALYST',7566,'1987-04-19',3000,NULL,20);
INSERT INTO emp VALUES(7839,'KING','PRESIDENT',NULL,'1981-11-17',5000,NULL,10);
INSERT INTO emp VALUES(7844,'TURNER','SALESMAN',7698,'1981-09-08',1500,0,30);
INSERT INTO emp VALUES(7876,'ADAMS','CLERK',7788,'1987-05-23',1100,NULL,20);
INSERT INTO emp VALUES(7900,'JAMES','CLERK',7698,'1981-12-03',950,NULL,30);
INSERT INTO emp VALUES(7902,'FORD','ANALYST',7566,'1981-12-03',3000,NULL,20);
INSERT INTO emp VALUES(7934,'MILLER','CLERK',7782,'1982-01-23',1300,NULL,10);
select * from emp;

CREATE TABLE dept(
	deptno		INT,
	dname		VARCHAR(14),
	loc		    VARCHAR(13)
);
INSERT INTO dept VALUES(10, 'ACCOUNTING', 'NEW YORK');
INSERT INTO dept VALUES(20, 'RESEARCH', 'DALLAS');
INSERT INTO dept VALUES(30, 'SALES', 'CHICAGO');
INSERT INTO dept VALUES(40, 'OPERATIONS', 'BOSTON');
select * from dept;

select * from emp;
```

```mysql
-- 1.查询出高于10号部门的平均工资 的员工信息
SELECT * FROM
emp
WHERE `sal`>
(SELECT AVG(`sal`) FROM emp WHERE `deptno`=10);
```

```mysql
-- 2.查询出比10号部门任何员工薪资高的员工信息
SELECT * FROM 
emp
WHERE `sal`>(SELECT MAX(`sal`) FROM emp WHERE `deptno`=10);
```

```mysql
-- 3.获取员工的名字和部门的名字
SELECT ename,(SELECT dname FROM dept WHERE dept.deptno=emp.deptno) AS `dept_name`
FROM emp
ORDER BY ename;
```

```mysql
-- 4.查询与SCOTT同一个部门的员工
SELECT `ename` FROM 
emp
WHERE `deptno`=
(SELECT `deptno` FROM emp WHERE `ename`='SCOTT');
```

```mysql
-- 5.查询工作和工资与MARTIN完全相同的员工信息
SELECT * 
FROM emp
WHERE job=(SELECT job FROM emp WHERE ename='MARTIN')
AND 
sal=(SELECT sal FROM emp WHERE ename='MARTIN')
AND ename!='MARTIN';
```

#### T2

https://zhuanlan.zhihu.com/p/345457364

https://zhuanlan.zhihu.com/p/345459843

多表，记得把各个表的字段列出，标明表之间相同的字段。

```mysql
-- 创建库
CREATE DATABASE SQL50;
-- 创建4张表
CREATE TABLE SC(Sid VARCHAR(10),Cid VARCHAR(10),score DECIMAL(18,1));
CREATE TABLE Student(Sid VARCHAR(10),Sname VARCHAR(10),Sage DATE,Ssex VARCHAR(10));
CREATE TABLE Course (Cid VARCHAR(10),Cname VARCHAR(10),Tid VARCHAR(10));
CREATE TABLE Teacher(Tid VARCHAR(10),Tname VARCHAR(18));
-- 注意：这里有个DECIMAL(18,1)数据类型，意思就是整数位最大18位，小数位1位。

-- 插入数据
-- 向[学生表]插入数据
INSERT INTO Student VALUES('0001' , '李小龙' , '1995/1/2' , '男');
INSERT INTO Student VALUES('0002' , '成龙' ,   '2006/12/13' , '男');		
INSERT INTO Student VALUES('0003' , '周星驰' , '1996/4/28' , '男');
INSERT INTO Student VALUES('0004' , '周润发' , '2001/7/16' , '男');
INSERT INTO Student VALUES('0005' , '李连杰' , '2018/5/26' , '男');
INSERT INTO Student VALUES('0006' , '梁朝伟' , '2020/4/16' , '男');
INSERT INTO Student VALUES('0007' , '张国荣' , '2018/7/21' , '男');
INSERT INTO Student VALUES('0008' , '梁家辉' , '1995/9/13' , '男');
INSERT INTO Student VALUES('0009' , '刘德华' , '1990/10/7' , '男');
INSERT INTO Student VALUES('0010' , '刘青云' , '2006/6/2' , '男');
INSERT INTO Student VALUES('0011' , '邱淑贞' , '1990/1/8' , '女');	
INSERT INTO Student VALUES('0012' , '张敏' ,   '2017/12/7' , '女');
INSERT INTO Student VALUES('0013' , '朱茵' ,   '2015/11/6' , '女');
INSERT INTO Student VALUES('0014' , '关之琳' , '2002/11/18' , '女');
INSERT INTO Student VALUES('0015' , '林青霞' , '2014/2/14' , '女');
INSERT INTO Student VALUES('0016' , '王祖贤' , '2011/5/25' , '女');
INSERT INTO Student VALUES('0017' , '钟楚红' , '2006/12/10' , '女');
INSERT INTO Student VALUES('0018' , '黎姿' ,   '2000/12/25' , '女');
INSERT INTO Student VALUES('0019' , '李嘉欣' , '1992/2/19' , '女');
INSERT INTO Student VALUES('0020' , '周慧敏' , '2010/8/15' , '女');

-- 向[课程表]插入数据
INSERT INTO Course VALUES('s1' , '语文' , '0001');
INSERT INTO Course VALUES('s2' , '数学' , '0002');
INSERT INTO Course VALUES('s3' , '英语' , '0003');

-- 向[教师表]插入数据
INSERT INTO Teacher VALUES('0001' , '数据分析王子');
INSERT INTO Teacher VALUES('0002' , '刘亦菲');
INSERT INTO Teacher VALUES('0003' , '彭于晏');

-- 向[成绩表]插入数据
INSERT INTO SC VALUES('0001' , 's1' ,81);
INSERT INTO SC VALUES('0001' , 's2' ,67);
INSERT INTO SC VALUES('0001' , 's3' ,88);
INSERT INTO SC VALUES('0002' , 's1' ,77);
INSERT INTO SC VALUES('0002' , 's3' ,86);
INSERT INTO SC VALUES('0003' , 's1' ,81);
INSERT INTO SC VALUES('0003' , 's2' ,70);
INSERT INTO SC VALUES('0003' , 's3' ,91);
INSERT INTO SC VALUES('0004' , 's1' ,62);
INSERT INTO SC VALUES('0004' , 's2' ,93);
INSERT INTO SC VALUES('0004' , 's3' ,95);
INSERT INTO SC VALUES('0005' , 's1' ,72);
INSERT INTO SC VALUES('0005' , 's2' ,52);
INSERT INTO SC VALUES('0005' , 's3' ,61);
INSERT INTO SC VALUES('0006' , 's1' ,80);
INSERT INTO SC VALUES('0006' , 's2' ,86);
INSERT INTO SC VALUES('0007' , 's1' ,100);
INSERT INTO SC VALUES('0007' , 's2' ,62);
INSERT INTO SC VALUES('0007' , 's3' ,72);
INSERT INTO SC VALUES('0008' , 's1' ,88);
INSERT INTO SC VALUES('0008' , 's2' ,72);
INSERT INTO SC VALUES('0008' , 's3' ,51);
INSERT INTO SC VALUES('0009' , 's2' ,56);
INSERT INTO SC VALUES('0009' , 's3' ,52);
INSERT INTO SC VALUES('0010' , 's1' ,84);
INSERT INTO SC VALUES('0010' , 's2' ,61);
INSERT INTO SC VALUES('0010' , 's3' ,75);
INSERT INTO SC VALUES('0011' , 's2' ,82);
INSERT INTO SC VALUES('0011' , 's3' ,92);
INSERT INTO SC VALUES('0012' , 's1' ,97);
INSERT INTO SC VALUES('0012' , 's2' ,88);
INSERT INTO SC VALUES('0012' , 's3' ,64);
INSERT INTO SC VALUES('0013' , 's1' ,75);
INSERT INTO SC VALUES('0013' , 's2' ,97);
INSERT INTO SC VALUES('0013' , 's3' ,67);
INSERT INTO SC VALUES('0014' , 's1' ,83);
INSERT INTO SC VALUES('0014' , 's2' ,86);
INSERT INTO SC VALUES('0014' , 's3' ,96);
INSERT INTO SC VALUES('0015' , 's2' ,86);
INSERT INTO SC VALUES('0015' , 's3' ,57);
INSERT INTO SC VALUES('0016' , 's1' ,75);
INSERT INTO SC VALUES('0016' , 's3' ,99);
INSERT INTO SC VALUES('0017' , 's1' ,56);
INSERT INTO SC VALUES('0017' , 's2' ,98);
INSERT INTO SC VALUES('0017' , 's3' ,80);
INSERT INTO SC VALUES('0018' , 's1' ,53);
INSERT INTO SC VALUES('0018' , 's2' ,51);
INSERT INTO SC VALUES('0018' , 's3' ,69);
INSERT INTO SC VALUES('0019' , 's1' ,97);
INSERT INTO SC VALUES('0019' , 's2' ,99);
INSERT INTO SC VALUES('0019' , 's3' ,64);
INSERT INTO SC VALUES('0020' , 's1' ,81);
INSERT INTO SC VALUES('0020' , 's2' ,52);
INSERT INTO SC VALUES('0020' , 's3' ,62);
```

```mysql
-- 1.查询名字中含有「龙」字的学生信息
SELECT * 
FROM student
WHERE `Sname` LIKE '%龙%';
```

```mysql
-- 2.查询「数」姓的所有老师的信息
SELECT *
FROM teacher
WHERE `Tname` LIKE '数%';
```

```mysql
-- 3.查询男生、女生人数
SELECT `Ssex` AS '性别',COUNT(*)
FROM student
GROUP BY `Ssex`
ORDER BY `Ssex`;
```

```mysql
-- 4.查询2000年(不含2000)后出生的学生名单
SELECT `Sname` 
FROM student
WHERE YEAR(`Sage`)>2000;
```

```mysql
-- 5.查询同名同姓学生名单，并统计同名人数 
SELECT `Sname`,tmp.`total` 
FROM (SELECT *,COUNT(`Sname`) AS 'total' FROM student GROUP BY `Sname`) tmp
WHERE tmp.`total`>1;
-- 解法2：
SELECT `Sname`,COUNT(*)
FROM student
GROUP BY `Sname`
HAVING COUNT(*)>1;
```

```mysql
-- 6.查询每门课程选修人数
SELECT (SELECT `Cname` FROM course WHERE course.Cid=sc.Cid) AS '课程名称',COUNT(`Sid`)
FROM sc
GROUP BY `Cid`;
-- 解法2：
SELECT course.Cname,COUNT(*) 
FROM course INNER JOIN sc ON sc.Cid=course.Cid
GROUP BY course.Cname
ORDER BY course.Cname;
```

```mysql
-- 7.查询每门课程的平均成绩
SELECT AVG(score),(SELECT `Cname` FROM course WHERE course.`Cid`=sc.`Cid`)
FROM sc
GROUP BY `Cid`;

-- 解法2：
SELECT course.Cname,AVG(sc.score)
FROM course INNER JOIN sc ON course.Cid=sc.Cid
GROUP BY course.Cname
ORDER BY course.Cid;
```

```mysql
-- 8.查询每门成绩最好的前3名
(SELECT * 
FROM sc
WHERE `Cid`=(SELECT `Cid` FROM course WHERE `Cname`='语文')
ORDER BY `score` DESC
LIMIT 3)
UNION 
(SELECT * 
FROM sc
WHERE `Cid`=(SELECT `Cid` FROM course WHERE `Cname`='数学')
ORDER BY `score` DESC
LIMIT 3)
UNION
(SELECT * 
FROM sc
WHERE `Cid`=(SELECT `Cid` FROM course WHERE `Cname`='英语')
ORDER BY `score` DESC
LIMIT 3);

-- UNION组合的几个SELECT语句最好用括号括起来，若不括起来，LIMIT、ORDER BY语句在最后一个SELECT语句后，会影响整个查询结果，而不仅仅是最后一个SELECT。

-- 下面的代码中，最后的ORDER BY将覆盖第一个SELECT中按照Cid升序排序，整个表降序排序；LIMIT 3将整个表只显示3行
(SELECT * 
FROM sc
WHERE `Cid`=(SELECT `Cid` FROM course WHERE `Cname`='语文')
ORDER BY `Cid`
LIMIT 3)
UNION 
(SELECT * 
FROM sc
WHERE `Cid`=(SELECT `Cid` FROM course WHERE `Cname`='数学')
LIMIT 3)
UNION
SELECT * 
FROM sc
WHERE `Cid`=(SELECT `Cid` FROM course WHERE `Cname`='英语')
ORDER BY `Cid` DESC
LIMIT 3;
```

```mysql
-- 9.查询选修过任一课程的学生的学生信息

```

```mysql
-- 10.查询至少选修两门课程的学生学号
SELECT `Sid`
FROM sc
GROUP BY `Sid`
HAVING COUNT(`Cid`)>=2;
```

```mysql
-- 11.查询出只选修两门课程的学生学号和姓名
SELECT `Sid`,`Sname`
FROM student
WHERE `Sid`IN
(SELECT `Sid`
FROM sc
GROUP BY `Sid`
HAVING COUNT(`Cid`)=2);

-- 解法2：
SELECT tmp.Sid AS `学生学号`,student.Sname AS `学生姓名`,course.Cname AS `课程名称`
FROM student INNER JOIN 
(SELECT sc.Sid,sc.Cid,COUNT(sc.Cid) FROM sc GROUP BY sc.Sid HAVING COUNT(sc.Cid)=2)AS tmp 
ON student.Sid=tmp.Sid INNER JOIN course ON course.Cid= tmp.Cid;
```

```mysql
-- 12.查询出选修了全部课程的学生信息
SELECT * 
FROM student 
WHERE `Sid` IN (SELECT `Sid` FROM sc GROUP BY `Sid` HAVING COUNT(`Cid`)=(SELECT COUNT(`Cid`) FROM course));

-- 解法2：
SELECT * FROM student INNER JOIN 
(SELECT sc.Sid FROM sc GROUP BY sc.Sid HAVING COUNT(sc.Cid)=(SELECT COUNT(course.Cid) FROM course)) AS tmp
ON student.Sid=tmp.Sid
```

```mysql
-- 13.查询没有选修全部课程的同学的信息
-- 解法1：
SELECT * 
FROM student 
WHERE `Sid` IN (SELECT `Sid` FROM sc GROUP BY `Sid` HAVING COUNT(`Cid`)<(SELECT COUNT(`Cid`) FROM course));
-- 解法2：
SELECT * 
FROM student 
WHERE `Sid` NOT IN (SELECT `Sid` FROM sc GROUP BY `Sid` HAVING COUNT(`Cid`)=(SELECT COUNT(`Cid`) FROM course));
```

```mysql
-- 14.查询各学生的年龄，只按年份来算
SELECT `Sname` AS '姓名',YEAR(NOW())-YEAR(`Sage`) AS '年龄'
FROM student;
```

```mysql
-- 15.查询所有学生的姓名、课程名称和分数

```

## 12.组合查询

* UNION

  连接多条SELECT语句，将查询出来的多条结果放在一张表上

  若有重复的记录，则去重

* UNION ALL

  不去重

* 连接的语句最好用括号括起来，括号内部的ORDER BY / LIMIT作用于括号内的SELECT语句。否则ORDER BY/LIMIT语句放在最后一条SELECT后，作用的是整个表，而不是最后一条SELECT查询到的表。见T2-8。

## 13.联结查询

https://zhuanlan.zhihu.com/p/145679471

https://zhuanlan.zhihu.com/p/58108883

https://blog.csdn.net/wheredata/article/details/87191983

https://www.cnblogs.com/guokaifeng/p/11192266.html

https://blog.csdn.net/qq_36078992/article/details/106005655

> 1. 先确定数据要用到哪些表。
> 2. 将多个表先通过笛卡尔积变成一个表。
> 3. 然后去除不符合逻辑的数据（根据两个表的关系去掉）。
> 4. 最后当做是一个虚拟表一样来加上条件即可。

#### 叉联结+WHERE过滤——笛卡尔积

叉联结形成笛卡尔积，有很多无意义行，通过WHERE过滤出指定条件的行

是全相乘效率低，全相乘会在内存中生成一个非常大的数据(临时表)，因为有很多不必要的数据。

全相乘不能好好的利用索引，因为全相乘生成一张临时表，临时表里是没有索引的，大大降低了查询效率。

联结要指定条件，否则会返回笛卡尔积，新表列数=表1列数*表2列数。

#### 外联结——OUTER JOIN

查询出左表和右表所有数据，但是去除两表的重复数据

#### 内联结

**两表**的等值联结。若需联结多个表需多个INNER JOIN。

```mysql
select [column_list]
            FROM 
            t1 INNER JOIN t2 ON [连接条件1]
            INNER JOIN t3 ON [连接条件2]
            ...
            WHERE where_conditions;
```

![image-20211029140928846](https://i.loli.net/2021/10/29/Dq8fvNFYmaT2WKS.png)

![image-20211101095439013](https://i.loli.net/2021/11/01/vKc7PAxEmQDn2qU.png)

```mysql
SELECT t1.DeviceNO,t2.DeviceName,
(CASE WHEN t1.DeviceStatus=1 THEN '正常'
WHEN t1.DeviceStatus=0 THEN '异常'
WHEN t1.DeviceStatus=-1 THEN '无效'
END) AS DeviceStatus,
t1.TestingNum,t1.DefectNum,t1.CPUTemperature,t1.CPUUsage,t1.MemoryUsage
FROM device_info AS t1 INNER JOIN device AS t2
ON t1.DeviceNO=t2.DeviceNO
WHERE t1.LineNO='001'
ORDER BY t1.`NO`;
```

#### 多表内联结

**mysql 在运行是关联指定的每个表以处理联结，这种处理是非常消耗资源的，所以不要联结过多的表，表越多性能下降越厉害**

```mysql
```



#### 自联结

一张表看成两张表，要取2个别名。否则字段指定存在歧义。

#### 左联结/右联结

左连接查询达到了同样的效果，但是不会有其它冗余数据，查询速度快，消耗内存小，而且使用了索引。左连接查询效率相比于全相乘的查询效率快了10+倍以上。

![image-20211101095359954](https://i.loli.net/2021/11/01/T6poxlzaesh9k15.png)

右连接查询跟左连接查询类似，只是**右连接是以右表为主表**，会将右表所有数据查询出来，而左表则根据条件去匹配，如果左表没有满足条件的行，则左边默认显示NULL。左右连接是可以互换的。

![image-20211101095416260](https://i.loli.net/2021/11/01/9GDWcbePTiuaHYo.png)

```mysql
SELECT LineName, 
(CASE WHEN COUNT(FaultTime)>0 THEN 1 
WHEN COUNT(FaultTime)=0 THEN 0
END) AS LineStatus
FROM productionline AS t1 LEFT JOIN faults_time AS t2
ON t1.LineNO=t2.LineNO
GROUP BY LineName
ORDER BY t1.`NO`;
```

#### 在联结中使用聚集函数



#### CASE、WHEN、THEN

> 数据SQL CASE 表达式是一种通用的条件表达式，类似于其它语言中的 if/else 语句。 
>
> ```mysql
> CASE WHEN condition THEN result 
> 
> 　　　WHEN condition THEN result 
> 
> 　　　.............
> 　　　[WHEN ...] 
> 　　　[ELSE result] 
> END 
> ```
>
> CASE 子句可以用于任何表达式可以有效存在的地方。 ***condition 是一个返回boolean 的表达式***。 如果结果为真，那么 CASE 表达式的结果就是符合条件的 result。 如果结果为假，那么以相同方式搜寻任何随后的 WHEN 子句。 如果没有 WHEN condition 为真，那么 case 表达式的结果就是在 ELSE 子句里的值。 如果省略了 ELSE 子句而且没有匹配的条件， 结果为 NULL。
>
> ```mysql
> CASE sex 
>          WHEN '1' THEN '男' 
>          WHEN '2' THEN '女' 
> ELSE '其他' END 
> ```
>
> 




## 14.索引

### （1）普通索引

#### 表已建立后创建索引

```mysql
CREATE INDEX indexName ON t_name (column_name);
```

#### 修改表结构——添加索引

```mysql
ALTER TABLE t_name ADD INDEX indexName(`fieldName`);
```

#### 建表的时候创建索引

```mysql
CREATE TABLE t_name(  
`ID` INT NOT NULL,   
`username` VARCHAR(16) NOT NULL,  
INDEX [indexName] (username(length))  
);  
```

#### 删除索引

```mysql
DROP INDEX [indexName] ON t_name; 
```

#### 显示已建立的索引

```mysql
SHOW INDEX FROM t_name\G
```



### （2）唯一索引

**与前面的普通索引类似，不同的就是：索引列的值必须唯一，但允许有空值**

```mysql
CREATE UNIQUE INDEX indexName ON mytable(username(length)) 

ALTER table mytable ADD UNIQUE [indexName] (username(length))

CREATE TABLE mytable(   
ID INT NOT NULL,    
username VARCHAR(16) NOT NULL,   
UNIQUE [indexName] (username(length))   
);  
```





## 15.约束



## 16.视图

将查询语句查询出来的表作为一个**虚拟表**进行封装。

实现SQL语句的重用，简化复杂SQL语句（联结查询）的使用，给与用户一部分表（经过查询得出的表是原表的一部分）

```mysql
-- 创建
CREATE VIEW view_name
-- 删除
DROP VIEW view_name
-- 显示创建
SHOW CREATE VIEW view_name

-- 例子
CREATE VIEW productscustomers AS 
SELECT cust_name,cust_contact,prod_id
FROM customers INNER JOIN orders ON customers.cust_id=orders.cust_id
INNER JOIN orderitems ON orders.order_num=orderitems.order_num;
-- 上面语句查询列出订购任意产品的客户
-- 现在想查询订购了产品'TN2'的客户
SELECT cust_name 
FROM productscustomers 
WHERE prod_id='TN2';
```

## 17. 事务

### （1）使用条件

* 在 MySQL 中只有使用了 **Innodb数据库引擎的数据库或表才支持事务**。
* 事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行。
* **事务只能用来管理 insert,update,delete 语句**，不能控制SELECT和DROP。

### （2）几个概念

* 事务——TRANSACTION
* 回滚——ROLLBACK TO，回退到某个保留点
* 提交——COMMIT，将未保存的动作写入数据库表
* 保留点——SAVAPOINT，一系列语句中的**占位符**，当回退时，可以标记退回到什么位置。每个保留点都取**唯一的名字**。

### （3）事务控制语句

https://blog.csdn.net/weixin_39641173/article/details/113945257?utm_medium=distribute.pc_relevant_bbs_down.none-task-blog-baidujs-1.nonecase&depth_1-utm_source=distribute.pc_relevant_bbs_down.none-task-blog-baidujs-1.nonecase

* START TRANSACTION或BEGIN——开启事务

* COMMIT

  将COMMIT与START TRANSACTION之间的动作提交，动作就提交到表，无法撤销了。**提交后事务被显式结束。**

* ROLLBACK

  将ROLLBACK与START TRANSACTION之间的部分撤销，回到START TRANSACTION事务开启时，**回滚后事务被显式结束。**

* 使用保留点

  SAVAPOINT P1

  ROLLBACK TO P1

  使用savepoint回滚难免有些性能消耗，一般可以用IF改写

  savepoint的良好使用的场景之一是“嵌套事务”，你可能希望程序执行一个小的事务，但是不希望回滚外面更大的事务

  

* 保留点ROLLBACK或者COMMIT后就自动释放。也可用RELEASE P1手动释放。

```mysql
/**
(1)在执行sql语句之前，我们要开启事务 start transaction;

(2)正常执行我们的sql语句

(3)当sql语句执行完毕，存在两种情况：

1，全都成功，我们要将sql语句对数据库造成的影响提交到数据库中，committ

2，某些sql语句失败，我们执行rollback(回滚)，将对数据库操作赶紧撤销
*/

SET AUTOCOMMIT=0
BEGIN;
UPDATE table1 SET field1='aaa' WHERE type=1;
UPDATE table2 SET field2='bbb' WHERE type=1;
COMMIT;//或ROLLBACK;
END;
```



### （4）更改MySQL的自动提交行为

MySQL默认情况下，只要表发生更改，就会立即提交。

```mysql
SET GLOBAL AUTOCOMMIT=0;	-- 关闭自动提交，直到AUTOCOMMIT置1时才提交
SET GLOBAL AUTOCOMMIT=1;	-- 开启自动提交
```

### （5）隔离级别与脏读

* 脏读——一个事务读到另一个事务还没有提交的数据（没有提交的数据一旦回滚到原数据，那读到的数据就是脏数据）



## 18. 存储过程

调用。变量用@表示。





## 19.  游标





## 20. 账号管理

* 严肃对待root账号的使用，仅在绝对需要的时候才使用。

* 账号、密码存在mysql数据库中，user表的user列存储用户名。

  不要直接操作mysql数据库中数据。

  ```mysql
  use mysql;
  SELECT user FROM user;
  ```

* 创建用户账号

  ```mysql
  CREATE USER user_name IDENTIFIED BY 'password';
  ```

  创建好的账号没设置权限，可登录但不能看任何数据，也不能进行任何操作。

* 查看权限

  ```mysql
  SHOW GRANTS FOR user_name;
  ```

* 授予权限

  ```mysql
  -- 给账号授予在库上查询所有数据的权限
  GRANT SELECT ON database_name.* TO user_name;
  -- 授予在表上查询的权限
  GRANT SELECT ON database_name.t_name TO user_name;
  -- 整个服务器
  GRANT ALL
  -- 库
  ON database_name.*
  -- 表
  ON database_name.t_name
  -- 列
  ```

* 撤销权限

  ```mysql
  REVOKE SELECT ON database_name.* FROM user_name;
  ```

* 重命名用户账号

  ```mysql
  RENAME USER user_name_old TO user_name_new;
  ```

* 删除用户账号

  ```mysql
  DROP USER user_name;
  ```

* 修改密码

  ```mysql
  -- 为当前user修改密码
  SET PASSWORD = PASSWORD('password');
  -- 修改指定用户密码
  SET PASSWORD FOR user_name = PASSWORD('password');
  ```

## 21. 导出导入

https://www.cnblogs.com/chenbin93/p/14697451.html

https://www.cnblogs.com/FengGeBlog/p/9974207.html

**navicat右键，转储、运行SQL文件，导出导入.sql文件。导出/导入向导选择文件格式.csv。**

### （1）.sql

* 导出数据库

  ```mysql
  -- 导出一个库
  mysqldump  -u user_name -p pwd  dbname > Backup.sql;
  -- 导出多个库，库名用空格隔开
  mysqldump  -u user_name -p pwd  --databases dbname1  dbname2  dbname3  .... > Backup.sql;
  -- 导出所有库
  mysqldump  -u user_name -p pwd  --all-databases > Backup.sql;
  ```

  Navicat——https://jingyan.baidu.com/article/ca00d56cba9926a99eebcfd6.html

* 导入数据库

  https://jingyan.baidu.com/article/a24b33cd2de7e219ff002b6b.html

* 导出表

  ```mysql
  -- 指定库名，表名用用空格隔开
  mysqldump  -u user_name  -p pwd dbname table1 table2  table3... >  Backup.sql;
  ```

* 创建一个空库，use该库，source指令导入。

  ```mysql
  CREATE DATABASE db;
  USE db;
  SOURCE route.sql;
  ```

### （2）.csv

.csv文件可被Excel打开。

* 导出

  

* 导入

  

## 22. 日志

https://blog.csdn.net/defonds/article/details/46858949

需要配置启用日志。

## 23.存储图片

图片/视频不直接存在数据库中（要以二进制数据存），而是存在文件系统中，将图片的路径存在数据库中。

