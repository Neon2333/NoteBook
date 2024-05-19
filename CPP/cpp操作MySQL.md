# 1. MySQL C API使用

---

> ```c
> #include <mysql.h>
> ```
>
> * 使用API要引入的动态库：
>
>   **Windows：`libmysql.dll`**
>
>   **Linux：`libmysqlclient.so`**
>
> * Windows下VS引入动态库：
>
>   属性->VC++->包含目录：MySQL-include，库目录：MySQL-lib
>   链接器->输入->附加依赖项：依赖的外部库的名称：libmysql.lib

## （1）连接MySQL服务器

初始化MySQL连接：

```c
//mysql参数赋值NULL
MYSQL* mysql_init(MYSQL* mysql);
```

连接服务器：

```c
/*
返回值: 
    成功: 返回MYSQL*连接句柄, 对于成功的连接，返回值与第1个参数的值相同。返回值指向的内存和第一个参数指针指向的内存一样
    失败，返回NULL。
    句柄: 是windows中的一个概念, 句柄可以理解为一个实例(或者对象)
*/ 
MYSQL* mysql_real_connect(
    MYSQL *mysql,           // mysql_init() 函数的返回值
    const char *host,       // mysql服务器的主机地址, 写IP地址即可
                            // localhost, null -> 代表本地连接
    const char *user,       // 连接mysql服务器的用户名, 默认: root 
    const char *pwd,     // 连接mysql服务器用户对应的密码, root用户的密码
    const char *db,         // 要使用的数据库的名字
    unsigned int port,      // 连接的mysql服务器监听的端口
                            // 如果==0, 使用mysql的默认端口3306, !=0, 使用指定的这个端口
    const char *unix_socket,// 本地套接字, 不使用指定为 NULL
    unsigned long client_flag); // 通常指定为0
```

## （2）执行sql

```c
// 执行一个sql语句, 添删查改的sql语句都可以
int mysql_query(MYSQL* mysql, const char* query);
参数:
    - mysql: mysql_real_connect() 的返回值
    - query: 一个可以执行的sql语句, 结尾的位置不需要加 ;
返回值: 
    - 如果查询成功，返回： 0。如果是查询, 结果集在mysql 对象中
    - 如果出现错误，返回：非0值。 
```

## （3）获取结果集

获得一个`MYSQL_RES`对象

```c
// 将结果集从 mysql(参数) 对象中取出
// MYSQL_RES 对应一块内存, 里边保存着这个查询之后得到的结果集
// 如何将行和列的数据从结果集中取出, 需要使用其他函数
// 返回值: 具有多个结果的MYSQL_RES结果集合。如果出现错误，返回NULL。 
MYSQL_RES *mysql_store_result(MYSQL* mysql);
```

## （4）获取字段个数

```c
// 得到列数
int num = mysql_num_fields(res);
```

## （5）获取字段名

```c
// 参数: 调用 mysql_store_result() 得到的返回值
// 返回值: MYSQL_FIELD* 指向一个结构体
// 通过查询官方文档, 返回是一个结构体的数组
// 通过这个函数得到结果集中所有列的名字
MYSQL_FIELD* mysql_fetch_fields(MYSQL_RES* result);	
```

返回值MYSQL_FIELD对应的是一个结构体，在mysql.h中定义如下:

```c
// mysql.h
// 结果集中的每一个列对应一个 MYSQL_FIELD
typedef struct st_mysql_field {
  char *name;                 /* 列名-> 字段的名字 */
  char *org_name;             /* Original column name, if an alias */
  char *table;                /* Table of column if column was a field */
  char *org_table;            /* Org table name, if table was an alias */
  char *db;                   /* Database for table */
  char *catalog;              /* Catalog for table */
  char *def;                  /* Default value (set by mysql_list_fields) */
  unsigned long length;       /* Width of column (create length) */
  unsigned long max_length;   /* Max width for selected set */
  unsigned int name_length;
  unsigned int org_name_length;                                                                                        
  unsigned int table_length;
  unsigned int org_table_length;
  unsigned int db_length;
  unsigned int catalog_length;
  unsigned int def_length;
  unsigned int flags;         /* Div flags */
  unsigned int decimals;      /* Number of decimals in field */
  unsigned int charsetnr;     /* Character set */
  enum enum_field_types type; /* Type of field. See mysql_com.h for types */
  void *extension;
} MYSQL_FIELD;
```

demo：

```c
// 得到存储头信息的数组的地址
MYSQL_FIELD* fields = mysql_fetch_fields(res);
// 得到列数
int num = mysql_num_fields(res);
// 遍历得到每一列的列名
for(int i=0; i<num; ++i)
{
    printf("当前列的名字: %s\n", fields[i].name);
}
```

## （6）遍历结果集

```c
// 如果想遍历整个结果集, 需要对该函数进行循环调用
// 如果要遍历整个结果集, 需要循环调用这个函数
typedef char** MYSQL_ROW;	//char* []字符串数组，代表一条记录，每个字符串是记录中字段的值
// 遍历结果集的下一行 
//char* []; 数组中的字符串对应的一列数据
// 对 MYSQL_ROW 遍历就可以得到每一列的值
MYSQL_ROW mysql_fetch_row(MYSQL_RES *result);
参数: 
    - result: 通过查询得到的结果集
返回值: 
    - 成功: 得到了当前记录中每个字段的值
    - 失败: NULL, 说明数据已经读完了
```

## （7）得到字段的长度

`int(5)`这种

```c
/* 
返回结果集内当前行的列的长度:
    1. 如果打算复制字段值，使用该函数能避免调用strlen()。
    2. 如果结果集包含二进制数据，必须使用该函数来确定数据的大小，原因在于，对于包含Null字符的任何字段，strlen()将返回错误的结果。
*/
unsigned long *mysql_fetch_lengths(MYSQL_RES *result);
参数: 
    - result: 通过查询得到的结果集
返回值:
    - 无符号长整数的数组表示各列的大小。如果出现错误，返回NULL。
```

## （8）事务操作

以1个不可分割的业务流程作为1个事务。

```c
//MySQL的自动提交默认是开启的。执行事务前需手动临时关闭自动提交
my_bool mysql_autocommit(MYSQL* mysql, my_bool mode) 
参数:
    如果模式为“1”，启用autocommit模式；如果模式为“0”，禁止autocommit模式。
返回值
    如果成功，返回: 0，如果出现错误，返回非0值。
        
// 事务提交
my_bool mysql_commit(MYSQL *mysql);
返回值: 成功: 0, 失败: 非0
    
// 数据回滚
my_bool mysql_rollback(MYSQL *mysql) 
返回值: 成功: 0, 失败: 非0
```

## （9）打印错误信息

```c
// 返回错误的描述
const char *mysql_error(MYSQL *mysql);
// 返回错误的编号
unsigned int mysql_errno(MYSQL *mysql);
```

## （10）释放资源

```c
// 释放结果集
void mysql_free_result(MYSQL_RES *result);
```

```c
// 关闭连接
void mysql_close(MYSQL* mysql);
```

## （11）查看/设置当前使用的字符集

查看MySQL内中文数据的存储格式，用utf-8编码中文才不会乱码。

```c
// 获取api默认使用的字符编码
// 为当前连接返回默认的字符集。
const char *mysql_character_set_name(MYSQL *mysql) 
// 返回值: 默认字符集。 

// 设置api使用的字符集
// 第二个参数 csname 就是要设置的字符集 -> 支持中文: utf8
int mysql_set_character_set(MYSQL *mysql, char *csname);
```

## （12）demo

```c
#include <stdio.h>
#include <mysql.h>

int main()
{
    // 1. 初始化连接环境
    MYSQL* mysql = mysql_init(NULL);
    if(mysql == NULL)\
    {
        printf("mysql_init() error\n");
        return -1;
    }

    // 2. 连接数据库服务器
    mysql = mysql_real_connect(mysql, "localhost", "root", "root", 
                               "scott", 0, NULL, 0);
    if(mysql == NULL)
    {
        printf("mysql_real_connect() error\n");
        return -1;
    }

    printf("mysql api使用的默认编码: %s\n", mysql_character_set_name(mysql));

    // 设置编码为utf8
    mysql_set_character_set(mysql, "utf8");

    printf("mysql api使用的修改之后的编码: %s\n", mysql_character_set_name(mysql));

    printf("恭喜, 连接数据库服务器成功了...\n");

    // 3. 执行一个sql语句
    // 查询scott数据库下的dept部门表
    const char* sql = "select * from dept";
    // 执行这个sql语句
    int ret = mysql_query(mysql, sql);
    if(ret != 0)
    {
        printf("mysql_query() a失败了, 原因: %s\n", mysql_error(mysql));
        return -1;
    }

    // 4. 取出结果集
    MYSQL_RES* res = mysql_store_result(mysql);
    if(res == NULL)
    {
        printf("mysql_store_result() 失败了, 原因: %s\n", mysql_error(mysql));
        return -1;
    }

    // 5. 得到结果集中的列数
    int num = mysql_num_fields(res);

    // 6. 得到所有列的名字, 并且输出
    MYSQL_FIELD* fields = mysql_fetch_fields(res);
    for(int i=0; i<num; ++i)
    {
        printf("%s\t\t", fields[i].name);
    }
    printf("\n");

    // 7. 遍历结果集中所有的行
    MYSQL_ROW row;
    while(row = mysql_fetch_row(res))
    {
        if(row != NULL)
        {
             // 将当前行中的每一列信息读出
        	for(int i=0; i<num; ++i)
        	{
            	printf("%s\t\t", row[i]);
        	}
        	printf("\n");
        } 
    }

    // 8. 释放资源 - 结果集
    mysql_free_result(res);

    // 9. 写数据库
    // 以下三条是一个完整的操作, 对应的是一个事务
    // 设置事务为手动提交
    mysql_autocommit(mysql, 0); 
    int ret1 = mysql_query(mysql, "insert into dept values(61, '海军', '圣地玛丽乔亚')");
    int ret2 = mysql_query(mysql, "insert into dept values(62, '七武海', '世界各地')");
    int ret3 = mysql_query(mysql, "insert into dept values(63, '四皇', '新世界')");
    printf("ret1 = %d, ret2 = %d, ret3 = %d\n", ret1, ret2, ret3);

    if(ret1==0 && ret2==0 && ret3==0)
    {
        // 提交事务
        mysql_commit(mysql);
    }
    else
    {
        mysql_rollback(mysql);
    }

    // 释放数据库资源
    mysql_close(mysql);
    
  return 0;
}
```

# 2. C++封装API

---

将上述C接口封装成C++类MysqlHelper，简化使用。

注意点：

* Windows+VS环境下导入库方法：
  属性->VC++->包含目录：MySQL-include，库目录：MySQL-lib
  链接器->输入->附加依赖项：填写导入库的名称：libmysql.lib

  导入库的图标是这样的：![image-20240518214703866](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240518214703866.png)

* `MySQLConntion::value(unsigned int index)`中

  ```cpp
  std::string MySQLConntion::value(unsigned int index)
  {
  	if (queryResult == nullptr || queryResultRow == nullptr)
  	{
  		return std::string();
  	}
  
  	if (index < 0 || index >= mysql_num_fields(queryResult))
  	{
  		return std::string();
  	}
  
  	//防止某个字段的值包含'\0'，这样string(char*)到'\0'就会停止
  	// char str[] = { '1', '2','3','4','5','\0','6','7' };
  	//std::string s(str);	//12345
  	//string(char*,int)遇到'\0'也不会停止，会把指定个数的char都包含
  	unsigned long* colRealLengths = mysql_fetch_lengths(queryResult);
  	return std::string(queryResultRow[index], colRealLengths[index]);	
  }
  ```

* 调用一次`next()`才可以用`value`取到一行记录

* 调用`startTransaction()`后的`update()`操作还是会正常的返回`true`或`false`，但只有调用了`commit()`后，才会将修改提交到数据库中

```cpp
/*
Windows+VS环境下导入库方法：
属性->VC++->包含目录：MySQL-include，库目录：MySQL-lib
链接器->输入->附加依赖项：依赖的外部库的名称：libmysql.lib
*/

#pragma once
#include <iostream>
#include <mysql.h>
#include <vector>

class MySQLConntion
{
public:
	/// <summary>
	/// 初始化连接
	/// </summary>
	MySQLConntion();
	//断开连接
	~MySQLConntion();
	/// <summary>
	/// 连接服务器
	/// </summary>
	/// <param name="host">主机</param>
	/// <param name="user">用户名</param>
	/// <param name="pwd">密码</param>
	/// <param name="dbName">数据库名</param>
	/// <param name="port">端口(default=3306)</param>
	/// <returns></returns>
	bool connect(std::string host, std::string user, std::string pwd, std::string dbName, unsigned int port = 3306);
	/// <summary>
	/// 更新数据库：insert、delete、update
	/// </summary>
	/// <param name="sqlUpdate">增删改sql语句</param>
	/// <returns></returns>
	bool update(std::string sqlUpdate);
	/// <summary>
	/// 查询数据库
	/// </summary>
	/// <param name="sqlQuery">查询sql语句</param>
	/// <returns></returns>
	bool query(std::string sqlQuery);
	/// <summary>
	/// 查看所有字段名
	/// </summary>
	/// <returns></returns>
	std::vector<std::string> fields();
	/// <summary>
	/// 遍历查询数据集，指向下一条记录
	/// </summary>
	/// <returns></returns>
	bool next();
	/// <summary>
	/// 通过字段的index获取值
	/// </summary>
	/// <param name="index"></param>
	/// <returns></returns>
	std::string value(unsigned int index);
	/// <summary>
	/// 通过字段名获取字段值
	/// </summary>
	/// <param name="fieldName"></param>
	/// <returns></returns>
	std::string value(std::string fieldName);
	/// <summary>
	/// 开启事务操作，设置成手动提交、创建保存点
	/// </summary>
	/// <returns></returns>
	bool startTransaction();
	/// <summary>
	/// 事务提交
	/// </summary>
	/// <returns></returns>
	bool commit();
	/// <summary>
	/// 事务回滚
	/// </summary>
	/// <returns></returns>
	bool rollback();
private:
	/// <summary>
	/// 清空上一次查询结果
	/// </summary>
	void freeQueryResult();

	MYSQL* mysqlConn = nullptr;	//MySQL连接
	MYSQL_RES* queryResult = nullptr;	//查询结果集
	std::vector<std::string> fieldNames;	//当前结果集的所有字段名
	MYSQL_ROW queryResultRow = nullptr;	//当前查询记录
};
```

```cpp
#include "MySQLConntion.h"

MySQLConntion::MySQLConntion()
{
	mysqlConn = mysql_init(mysqlConn);	//初始化连接
}

MySQLConntion::~MySQLConntion()
{
	if (mysqlConn != nullptr)
	{
		mysql_close(mysqlConn);	//关闭连接
		freeQueryResult();	//清空结果集queryResult和结果集中字段fieldNames
	}
}

bool MySQLConntion::connect(std::string host, std::string user, std::string pwd, std::string dbName, unsigned int port)
{
	mysqlConn = mysql_real_connect(mysqlConn, host.c_str(), user.c_str(), pwd.c_str(), dbName.c_str(), 
		port, nullptr, 0);	//失败返回NULL
	return mysqlConn != nullptr;	
}

bool MySQLConntion::update(std::string sqlUpdate)
{
	return mysql_query(mysqlConn, sqlUpdate.c_str()) == 0;	//修改成功返回0，否则返回非0
}

bool MySQLConntion::query(std::string sqlQuery)
{
	if (mysql_query(mysqlConn, sqlQuery.c_str()) != 0)	//查询成功返回非0
	{
		return false;
	}

	freeQueryResult();	//清空上次的查询结果和字段名
	queryResult = mysql_store_result(mysqlConn);	//从MySQL服务器把查询结果拉到客户端
	return true;
}

std::vector<std::string> MySQLConntion::fields()
{
	if (queryResult == nullptr)
	{
		return fieldNames;
	}

	std::vector<std::string>(0).swap(fieldNames);	//清空上次查询记录的字段
	int colCount = mysql_num_fields(queryResult);	//获取字段的个数
	MYSQL_FIELD* fields = mysql_fetch_fields(queryResult);	//获取字段名数组

	for (int i = 0; i < colCount; i++)
	{
		fieldNames.emplace_back(fields[i].name);	//遍历存储字段名
	}
	return fieldNames;
}

bool MySQLConntion::next()
{
	if (queryResult == nullptr)
	{
		return false;
	}

	queryResultRow = mysql_fetch_row(queryResult);	//从结果集取下一条记录
	return queryResultRow != nullptr;	//如果已经取完，返回NULL
}

std::string MySQLConntion::value(unsigned int index)
{
	if (queryResult == nullptr || queryResultRow == nullptr)
	{
		return std::string();	//返回空字符串
	}

	if (index < 0 || index >= mysql_num_fields(queryResult))
	{
		return std::string();
	}

	//防止某个字段的值包含'\0'，这样string(char*)到'\0'就会停止
	// char str[] = { '1', '2','3','4','5','\0','6','7' };
	//std::string s(str);	//12345
	//string(char*,int)遇到'\0'也不会停止，会把指定个数的char都包含
	unsigned long* colRealLengths = mysql_fetch_lengths(queryResult);	//获取该字段值的实际长度
	return std::string(queryResultRow[index], colRealLengths[index]);	
}

std::string MySQLConntion::value(std::string fieldName)
{
	if (queryResult == nullptr || queryResultRow == nullptr)
	{
		return std::string();
	}

	if (fieldName == "")
	{
		return std::string();
	}

	for (unsigned int i = 0; i < fieldNames.size(); i++)
	{
		if (fieldName == fieldNames[i])
		{
			return value(i);
		}
	}
	return std::string();
}

void MySQLConntion::freeQueryResult()
{
	if (queryResult != nullptr)
	{
		mysql_free_result(queryResult);
		std::vector<std::string>(0).swap(fieldNames);
	}
}

bool MySQLConntion::startTransaction()
{
	return mysql_autocommit(mysqlConn, 0) == 0;
}

bool MySQLConntion::commit()
{
	return mysql_commit(mysqlConn) == 0;
}

bool MySQLConntion::rollback()
{
	return mysql_rollback(mysqlConn) == 0;
}
```

```cpp
#include <iostream>
#include "MySQLConntion.h"

int main()
{
	MySQLConntion* mysql = new MySQLConntion();
	
	if (mysql->connect("localhost", "root", "wk3217609", "test"))
	{
		std::cout << "connected.." << std::endl;
	}

	mysql->query("select * from machine;");
	
	for (auto field : mysql->fields())
	{
		std::cout << "field=" << field << std::endl;
	}

	while (mysql->next())
	{
		int colCount = 0;
		int fieldsCount = (int)mysql->fields().size();
		for (int i = 0; i < fieldsCount; i++)
		{
			std::cout << mysql->value(colCount++) << "\t";
		}
		std::cout << std::endl;
	}
	
	while (mysql->next())
	{
		std::cout << mysql->value("MachineName") << std::endl;
	}

	mysql->startTransaction();
	bool flag1 = mysql->update("insert into machine values('16','201','test');");
	bool flag2 = mysql->update("delete from machine where NO='1';");

	if (flag1 && flag2)
	{
		mysql->commit();
	}
	else
	{
		mysql->rollback();
	}

}
```

# 3. 连接池实现

连接池目的就是避免连接频繁的创建和销毁。

后端服务器和数据库服务器往往是分开的。

作为后端服务器，往往是通过多线程从连接池中取连接去访问数据库服务器，从中获取数据。在进行业务处理，返回结果给前端的。

## （1）数据库连接配置文件

> 连接数据库，一般从配置文件`dbconf`中读取dbServerIP、user、password、port等参数。配置文件可选：json、xml或自定义格式。（建议选json）





















