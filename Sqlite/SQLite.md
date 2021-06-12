# 问题总结

## 1. 插入大量数据（万级）时的速度问题

使用事务，将所有对数据库的操作一次性执行完再写入文件。

## 2. 索引的使用时，应注意使用方法，不要让索引失效

## 3. C#获取系统时间（精确到毫秒）

https://bbs.csdn.net/topics/190043971

```C#
 DateTime.Now.ToString()+DateTime.Now.Millisecond.ToString() 
```

## 4. 给DATETIME变量插入记录

https://blog.csdn.net/weixin_34279246/article/details/90058751

## 5. 插入BOOLEAN变量

https://blog.csdn.net/zhou363667565/article/details/84042914

https://www.cnblogs.com/awomanofsin/p/8548384.html

## 6. SQLite获取系统时间

https://blog.csdn.net/weixin_30342827/article/details/97310822

## 7. 根据DATETIME字段范围查询数据

https://blog.csdn.net/a462464126/article/details/42674173

## 8. 大量插入数据时速度慢问题的解决

https://blog.csdn.net/qingflyer/article/details/6372498

sqlite 插入数据很慢的原因是因为它是已文件的形式存在磁盘中，每次访问时都要打开一次文件，如果对数据进行大量的操作，那时很慢。

 解决方法是用**事务**的形式提交：因为我们开始事务后，进行的大量操作的语句都保存在内存中，当提交时才全部写入数据库，此时，数据库文件也就只用打开一次。

# 1. SQLite与MySQL的区别

* MYSQL是完善的服务器数据库,功能全面，综合化，追求最大并发效率。基于客户端-服务器架构，需要联网访问服务器。将数据库导出为文件时耗时长；而SQLITE是单机数据库，功能简约，小型化，追求最大磁盘效率。 SQLite直接将信息存储在单个.db文件中，使其易于复制。不需要任何配置，并且可以使用最少的支持来完成该过程。 
* MySql主要用来做服务端的开发；而Sqlite是用来做客户端开发。
* SQLite没有内置的身份验证机制，任何人都可以访问数据库文件；而MySQL带有许多内置的安全功能，需要使用用户名，密码或SSH进行身份验证。
* SQLite没有任何特定的用户管理功能，因此不适合多用户访问；MySQL具有构造良好的用户管理系统，可以处理多个用户并授予不同级别的权限。
* SQLite是一个类似于Access的单机版数据库管理系统，它将所有数据库的定义（包括定义、表、索引和数据本身）都保存在一个单一的文件中。

SQLite多用在嵌入式设备上，如Android、Unix、Linux、单片机。MySQL多用于网站后端。

# 2. SQLite语法

重点指出与MySQL不同之处

## 1. CMD指令

```sqlite
.quit
.help
.databases
.tables
.indexes
```



##  2. 建库

cmd下不进入sqlite

```sqlite
sqlite3 path\\dbName.db3  --path是db文件的路径
```



## 3. 约束

* NOT  NULL

* 主键

  ```sqlite
  CREATE TABLE table_name (col_name1 INT PRIMARY KEY, col_name2 TEXT, col_name3 varchar(50), col_name4 REAL);
  ```

* UNIQUE

   ```sqlite
  CREATE TABLE table_name (col_name1 INT PRIMARY KEY, col_name2 TEXT NOT NULL UNIQUE, col_name3 DATATIME);
  ```

* DEFAULT

  ```sqlite
  CREATE TABLE table_name (col_name1 INT PRIMARY KEY, col_name2 TEXT NOT NULL UNIQUE, col_name3 REAL DEFAULT 500.24);
  ```

  

## 4. 索引

* 概念

  索引（Index）是一种特殊的查找表，数据库搜索引擎用来加快数据检索。简单地说，索引是一个指向表中数据的指针。一个数据库中的索引与一本书的索引目录是非常相似的。

  例如这样一个查询：select * from table1 where id=10000。如果没有索引，必须遍历整个表，直到ID等于10000的这一行被找到为止；有了索引之后(必须是在ID这一列上建立的索引)，即可在索引中查找。由于索引是经过某种算法优化过的，因而查找次数要少的多。

  **索引，不要滥用：**

  - 索引不应该使用在较小的表上。
  - **索引不应该使用在有频繁的大批量的更新或插入操作的表上。**
  - 索引不应该使用在含有大量的 NULL 值的列上。
  - **索引不应该使用在频繁操作的列上。**

  https://blog.csdn.net/zbc415766331/article/details/55053224

* 分类

  单列索引，将表的某一字段作为索引。

  组合索引，将两个字段或两个以上字段同时作为索引。

  唯一索引，将某一个字段作为索引的同时加上了UNIQUE约束，插入的记录该字段不能重复。

* 查看索引

  ```sqlite
  --cmd下
  .indexes
  ```

* 删除索引

  ```sqlite
  DROP INDEX index_name;
  ```

* 创建索引

  http://blog.sina.com.cn/s/blog_809ff62a01016f7l.html

  （1）表已存在

  ```sqlite
  --单列索引
  CREATE INDEX index_name ON table_name(col_name);
  --组合索引
  CREATE INDEX index_name ON table_name(col_name1, col_name2);
  --唯一索引
  CREATE UNIQUE INDEX index_name ON table_name(col_name);
  ```

  （2）建表时指定

  **SQLite不能建表时创建索引，而MySQL可以**
  
  

* 创建索引时可指定记录按照索引升序或降序排列

  ```sqlite
  --字段名后加上ASC或DESC
  CREATE UNIQUE INDEX index_name ON table_name(col_name DESC);
  ```

### 使用索引的注意点

https://www.cnblogs.com/elonlee/p/3508237.html



## 5. 事务

* 概念

  事务（Transaction）是一个对数据库执行工作单元。事务（Transaction）是以逻辑顺序完成的工作单位或序列，可以是由用户手动操作完成，也可以是由某种数据库程序自动完成。

  可以把许多的 SQLite 查询联合成一组，把所有这些放在一起作为事务的一部分进行执行。保工作单位内的所有操作都成功完成，否则，事务会在出现故障时终止，之前的操作也会回滚到以前的状态。

* 指令

  ```sqlite
  --启动事务。务通常会持续执行下去，直到遇到下一个 COMMIT 或 ROLLBACK 命令。
  --不过在数据库关闭或发生错误时，事务处理也会回滚。
  --注意用C#等语言操作时的SQLiteConnection对象是否一直处于连接状态
  --sqliteConn.State==ConnectionState.Open是否一直成立
  BEGIN TRANSACTION;
  --提交。用于把事务调用的更改保存到数据库中的事务命令
  COMMIT;
  --结束事务
  END TRANSACTION；
  --撤销还没有提交到数据库文件的操作
  ROLLBACK;
  ```

# 3. C#连接SQLite

https://blog.csdn.net/zyx_linux/article/details/21550989



