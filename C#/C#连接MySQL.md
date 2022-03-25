

## 1. MySQL相关

### （1）用到的dll和头文件

**DLL——MySql.Data.dll**

**头文件**

```C#
using MySql.Data;
using MySql.Data.MySqlClient;
```



https://www.cnblogs.com/gaokaitai/p/10633858.html

https://www.cnblogs.com/zwj1276952588/p/10499789.html

https://blog.csdn.net/liyazhen2011/article/details/82895946

https://zhuanlan.zhihu.com/p/68905542

https://blog.csdn.net/yelin042/article/details/90479330

https://www.cnblogs.com/andy9468/p/12841413.html

### （2）一些函数

* MySqlConnection.Open()

* MySqlConnection.State

* MySqlCommand.CommandTimeout

* MySqlCommand.ExecuteNonQuery()

  执行一个INSERT/DELETE/UPDATE命令后，cmd.ExecuteNonQuery()相当于刷新操作，执行后增/删/改指令起作用。该函数返回受到指令操作影响的行数。SELECT方法不适合该函数。用于确定操作是否成功，若受影响的行数=0则不成功，若>0则成功。

* ExecuteScalar()

* ExecuteReader()

* 类MySqlDataAdapter

* 类MySqlCommandBuilder

### （3）MySQLParameter

​		参数化查询（Parameterized Query ）是指在设计与数据库链接并访问数据时，在需要填入数值或数据的地方，使用参数 (Parameter) 来给值，这个方法目前已被视为最有效可预防SQL注入攻击 (SQL Injection) 的攻击手法的防御方式。下面将重点总结下Parameter构建的几种常用方法。说起参数化查询当然最主要的就是如何构造所谓的参数：比如，我们登陆时需要密码和用户名，一般我们会这样写sql语句，Select * from Login where username= @Username and password = @Password，为了防止sql注入，我们该如何构建@Username和@Password两个参数呢，下面提供六种（其实大部分原理都是一样，只不过代码表现形式不一样，以此仅作对比，方便使用）构建参数的方法，根据不同的情况选用合适的方法即可：

```mysql
static void Main(string[] args)
{       
    string connectionString = "Server='localhost';Database='test';UId='root';Password='123456'";
    MySqlConnection mycon = new MySqlConnection(connectionString);
    mycon.Open();
 
    string str = "insert into person(id,name) values(@id,@name)";
    MySqlCommand cmd = new MySqlCommand(str, mycon);
    cmd.CommandType = CommandType.Text;
            
    MySqlParameter p1 = new MySqlParameter("@id", MySqlDbType.Int32);
    p1.Value = 1;
    MySqlParameter p2 = new MySqlParameter("@name", MySqlDbType.VarChar);
    p2.Value = "Dave";
          
    cmd.Parameters.Add(p1);
    cmd.Parameters.Add(p2);
 
    if (cmd.ExecuteNonQuery() > 0)
    {
        Console.WriteLine("数据插入成功！");
    }
    Console.ReadLine();
    mycon.Close();
}
```

### （4）C#调用带参数的存储过程

https://www.cnblogs.com/zeroone/p/6021329.html

* 通过`MySqlParameter`封装参数
* `string cmd`只存`存储过程名`

```mysql
CREATE DEFINER=`root`@`localhost` PROCEDURE `p_deleteDevice`(IN ln VARCHAR(20), IN dn VARCHAR(20), OUT ifRowAffected INT(1))
BEGIN
DECLARE ifAffectedRow TINYINT(1) DEFAULT 1;
DECLARE SQL_FOR_UPDATE_device_config VARCHAR(100);

START TRANSACTION;

SET SQL_FOR_UPDATE_device_config=CONCAT('UPDATE device_config SET `DeviceStatus_', dn, '`=0 WHERE LineNO=', ln, ';');
SET @sql=SQL_FOR_UPDATE_device_config;
PREPARE stmt FROM @sql;
EXECUTE stmt;
-- 使用PREPARE，获取ROW_COUNT必须要在DEALLOCATE释放sql语句之前
CASE ROW_COUNT()
	WHEN 0 THEN 
		SET ifAffectedRow=0;
	ELSE 
		BEGIN END;
END CASE;	
DEALLOCATE PREPARE stmt;

DELETE FROM device_info WHERE LineNO=ln AND DeviceNO=dn;
CASE ROW_COUNT()
	WHEN 0 THEN 
		SET ifAffectedRow=0;
	ELSE 
		ALTER TABLE device_info AUTO_INCREMENT=1;
END CASE;

DELETE FROM device_info_threshold WHERE LineNO=ln AND DeviceNO=dn;
CASE ROW_COUNT()
WHEN 0 THEN 
	SET ifAffectedRow=0;
ELSE 
	ALTER TABLE device_info_threshold AUTO_INCREMENT=1;
END CASE;

DELETE FROM device_info_paranameandsuffix WHERE LineNO=ln AND DeviceNO=dn;
CASE ROW_COUNT()
WHEN 0 THEN 
	SET ifAffectedRow=0;
ELSE 
	ALTER TABLE device_info_paranameandsuffix AUTO_INCREMENT=1;
END CASE;

DELETE FROM faults_config WHERE LineNO=ln AND DeviceNO=dn;
CASE ROW_COUNT()
WHEN 0 THEN 
	SET ifAffectedRow=0;
ELSE 
	ALTER TABLE faults_config AUTO_INCREMENT=1;
END CASE;

IF(ifAffectedRow=1) THEN 
	COMMIT;
	SELECT ifAffectedRow INTO ifRowAffected;
ELSE 
	ROLLBACK;
END IF;

END
```

```C#
// MySQL接口
//参数列表：存储过程名、封装的参数MySqlParameter数组、输入参数个数、输出参数个数
public bool _executeProcMySQL(string cmdExecute, MySqlParameter[] parameters, int inParaCount, int outParaCount)
        {
            bool flag = false;
            try
            {
                MySqlCommand myCommand = new MySqlCommand(cmdExecute, conn);
                myCommand.CommandType = CommandType.StoredProcedure;	//一定要把命令类型声明为 存储过程
                myCommand.CommandTimeout = 12000;
                if (this.conn.State == ConnectionState.Closed)
                {
                    this.conn.Open();		//开启连接
                }
                for(int i = 0; i < inParaCount; i++)
                {
                    parameters[i].Direction = ParameterDirection.Input;		//每个封装的MySqlParameter都要设定是 输入参数、输出参数
                    myCommand.Parameters.Add(parameters[i]);			//将MySqlParameter添加到myCommand
                }

                for(int i = 0; i < outParaCount; i++)
                {
                    parameters[inParaCount + i].Direction = ParameterDirection.Output;
                    myCommand.Parameters.Add(parameters[inParaCount + i]);
                }

                int rows = myCommand.ExecuteNonQuery();
                if (rows > 0)
                {
                    flag = true;
                }
                myCommand.Parameters.Clear();
            }
            catch (SystemException ex)
            {
                ex.ToString();
            }
            return flag;
        }
```

```C#
MySQL.MySQLHelper mysqlHelper1 = new MySQL.MySQLHelper("localhost", "cloud_manage", "root", "ei41");
            mysqlHelper1._connectMySQL();

            DataRow drSelected = tileView1.GetDataRow(selectRow[0]);    //获取的是grid绑定的表所有列，而不仅仅是显示出来的列
            
            MySqlParameter lineNO = new MySqlParameter("ln", MySqlDbType.VarChar, 20);
            lineNO.Value = this.sideTileBarControl_deviceAdditionDeletion.tagSelectedItem;	//将参数值封装
            MySqlParameter deviceNO = new MySqlParameter("dn", MySqlDbType.VarChar, 20);
            deviceNO.Value = drSelected["DeviceNO"];
            MySqlParameter ifAffected = new MySqlParameter("ifRowAffected", MySqlDbType.Int32, 1);
            MySqlParameter[] paras = { lineNO, deviceNO, ifAffected };
            string cmdDeleteDevice = "p_deleteDevice";
            mysqlHelper1._executeProcMySQL(cmdDeleteDevice, paras, 2, 1);

            this.confirmationBox1.Visible = false;
            getDtDeviceCanDeleteEachLine(this.sideTileBarControl_deviceAdditionDeletion.tagSelectedItem);   //刷新grid显示
            
            if (Convert.ToInt32(ifAffected.Value) == 1)
            {
                MessageBox.Show("删除成功");
            }
            else if (Convert.ToInt32(ifAffected.Value) == 0)
            {
                MessageBox.Show("删除失败");
            }
            mysqlHelper1.conn.Close();
```

## 2. 流程

* 定义服务器、用户名、密码字符串connStr，创建连接对象conn；
* 打开连接conn.open()；
* 定义数据库操作指令字符串cmdStr（防sql注入，用@定义变量），创建命令对象cmd；
* 声明cmd的命令类型cmd.CommandType
* 读取数据ExecuteReader()、ExecuteScalar()、ExecuteNonQuery()。

### （1）创建连接对象

> * 使用【using】来调用mysql连接，这样使用完后可以自动关闭连接
> * 连接数据库：
>   【data source】=服务器IP地址;
>   【database】=数据库名称;
>   【user id】=数据库用户名;
>   【password】=数据库密码;
>   【pooling】=是否放入连接池;
>   【charset】=编码方式;
>   ————————————————
>   版权声明：本文为CSDN博主「(≯^ω^≮)喵毛」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
>   原文链接：https://blog.csdn.net/shenqiankk/article/details/99704319

```mysql
//命名空间 System.Data.MySqlClient.MySqlConnection
using MySql.Data;
using MySql.Data.MySqlClient;

string constring = "data source=localhost;database=test1;user id=root;password=1234;pooling=true;charset=utf8;";	//存储指定连接时的数据库、服务器、用户名等

MySqlConnection conn = new MySqlConnection(connStr);	//创建连接对象
conn.open();	//连接打开

 string constring = "data source=localhost;database=test1;user id=root;password=1234;pooling=true;charset=utf8;";
 
 //使用【using】来调用mysql连接，这样使用完后可以自动关闭连接
 using(MySqlConnection msc = new MySqlConnection(constring))
{
	conn.open();	//连接打开
}
```

### （2）创建命令对象/增删改

> ​	CommandType是SqlCommand对象的一个属性，用于指定执行动作的形式，它告诉.net接下来执行的是一个文本(text)、存储过程(StoredProcedure)还是表名称(TableDirect)。而CommandType是一个枚举类型。有三个值：text、StoredProcedure、TableDirect用于表示SqlCommand对象CommandType的执行形式。 cmd.CommandType = CommandType.Text; 是告诉cmd执行的sql是你赋给CommandText的值里写出的sql语句，如果是存储过程的话，cmd.CommandType = CommandType.StoredProcedure;然后CommandText='存储过程的名字'，这就是告诉cmd执行的是存储过程，存储过程的名字就是CommandText的值。 

**当指令不确定时用变量替代变换内容，再给变量赋值**

```mysql
string cmdStr="insert into person(id,name) values(@id,@name)";//在指令中定义变量id和name，以@表示
//写法1
MySqlCommand cmd = new MySqlCommand(cmdStr,conn);//绑定连接对象和指令
cmd.CommandType = CommandType.Text;
MySqlParameter p1 = new MySqlParameter("@id", MySqlDbType.Int32);//将变量id封装为p1
p1.Value = 1;//给@id赋值
MySqlParameter p2 = new MySqlParameter("@name", MySqlDbType.VarChar);
p2.Value = "Dave";          
cmd.Parameters.Add(p1);
cmd.Parameters.Add(p2);
//写法2
MySqlCommand cmd = new MySqlCommand();
cmd.Connection=sqlcon;
cmd.CommandType = CommandType.Text;	//告诉cmd按照用户给CommandText赋值的指令来操作
cmd.CommandText=cmdString;	//给CommandText赋值指令
cmd.Parameters.Add("@id",MySqlDbType.Int32);
cmd.Parameters.Add("@name",MySqlDbType.Varchar);
cmd.Parameters["@id"].Value=1;
cmd.Parameters["@name"].Value="Dave";
```

### （3）查询读取数据

* **ExecuteReader()**

  返回值MySqlDataReader类型。

  单向读取：只能依次读取下条数据。只读：数据无法修改。DataSet可修改。

  提供Read()方法读取一行，返回值为真时表明读到。对于每行元素，可以用getXXX()方法读取属性值，XXX为该属性类型，参数为属性名或者该属性为这张表的第几列。

  用MySqlDataReader.IsDBNull()方法判断是否为NULL。

```mysql
MySqlDataReader reader=cmd.ExecuteReader();	//返回一个MySQLDataReader对象
int index=0;
while(reader.Read()){
	this.dataGridView.Rows[index].Cells[0].Value=reader.GetString("id");
	this.dataGridView.Rows[index].Cells[1].Value=reader.GetString("name");
	this.dataGridView.Rows[index].Cells[2].Value=reader.GetInt32("age");
}
```

* **ExecuteScalar()**

  返回值为所有类型的父类的object类型，输出时强转一下。

  ```mysql
    //select语句查询不到结果时返回null
    if (objCMD.ExecuteScalar() == null)
          {
          	//为空时的操作
          }
          else
          {
          	//Trim()方法去除字符串开头和结尾的所有空格
           	result = objCMD.ExecuteScalar().ToString().Trim();
          }
  ```

* **ExecuteNonQuery()**

  返回值为在指令操作下发生改变的记录的行数。==0表明受影响行数为0，即指令操作不成功；>0表明成功。

  ```mysql
  string sql3 = "insert into characters (names,passwords) values ('XXX','1234456')";
  MySqlCommand cmd3 = new MySqlCommand(sql3,conn);
  conn.Open();
  int s = cmd3.ExecuteNonQuery();
  if (s == 0) 
  Console.WriteLine("false");
  else 
  Console.WriteLine("success");
  conn.Close();
  ```

  也可不用int变量接返回值，表明对刚刚的指令对数据库进行的操作进行刷新。

  ```mysql
  string uname=Console.ReadLine();
  string upwd=Console.ReadLine();//获取用户输入
  string sql="insert into characters (names,passwords) values (@name,@pwd)";//使用@符构造sql变量
  MysqlCommand cmd = new MysqlCommand(sql,conn);
  //使用MysqlCommand对象的parameters属性，该属性为像sql语句传递的参数集合，使用add方法向其中添加参数，参数以MysqlParameters对象形式传递
  cmd.parameters.Add(new MysqlParametes("@name",uname));
  cmd.parameters.Add(new MysqlParameters("@pwd",upwd));
  conn.Open();
  cmd.ExecuteNonQuery();	//刷新
  conn.Close();
  ```

* **MySqlDataAdapter+MySqlCommandBuilder**——读取一个datatable

  ```C#
   //----------------------------------------------------------------------
          // 功能说明：数据库表查询函数。显示表中所有字段，返回DataTable。sqlSearch存过滤条件。
          // 输入参数：无
          // 输出参数：无
          // 返回值：无
          //----------------------------------------------------------------------
          public DataTable _SearchTable(String sqlSearch)
          {
              DataTable myDataTable = new DataTable();//创建存放数据表。DataTable是MySQL中本地的表，DataSet本地的库，其中有若干DataTable组成
  
              try
              {
                  sqlSearch = "select * from " + sqlSearch;//查询指令内容
                  MySqlCommand myCommand = new MySqlCommand(sqlSearch, mySqlConnect);//创建MySqlCommand对象
                  myCommand.CommandTimeout = 12000;//超时
                  if (mySqlConnect.State == ConnectionState.Closed)
                  {
                      mySqlConnect.Open();//开启连接
                  }
  
                  MySqlDataAdapter adapter = new MySqlDataAdapter(sqlSearch, mySqlConnect);//创建MySqlDataAdapter对象
                  MySqlCommandBuilder builder = new MySqlCommandBuilder(adapter);//此处必须有，否则无法更新
                  adapter.Fill(myDataTable);//将查询的内容填充到数据表中
              }
              catch (SystemException ex)
              {
                  ex.ToString();
              }
  
              return myDataTable;
          }
  ```
  
  
  
* **用DataSet/DataTable读取数据（常用）**

  DataSet与DataReader以“流”随时和服务器保持联系不同，类似于一个本地的小数据库，速度略慢但可忽略不计，连接池负担小。

  https://blog.csdn.net/yelin042/article/details/90479330

  **DataTable赋值、取值**

  DataTable.Rows.Count

  DataTable.Columns.Count

  ```mysql
  //已有表dt
  
  //赋值
  //可通过索引访问，也可通过字段名访问
  dt.Rows[0][1]="李四"	//第一个索引表示第1行，第二个索引表示第2个字段
  dt.Rows[0]["name"]="李四"	//通过字段名"name"访问
  
  //取值
  string name = dt.Rows[0][1].ToString();
  ```

### （4）读取数据后的类型转换

[(string)、toString()、Convert.toString()的区别](https://www.cnblogs.com/kissdodog/p/3565988.html)

![image-20211031133859987](https://i.loli.net/2021/10/31/83DdpiSRM9Get7P.png)

```C#
//其中，DT.Rows[i][colNum]是object(double)类型
num = this.DT.Rows[i][colNum].ToString();
//num = Convert.ToString(this.DT.Rows[i][colNum]);  //可以
//num = (string)this.DT.Rows[i][colNum];    //无法将double转成string
```

### （5）读到DataTable后操作

#### 对DataTable某列求和

https://blog.csdn.net/weixin_40508362/article/details/106668117

https://blog.csdn.net/missysm586/article/details/6644319?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link

#### 对DataTable查询

https://www.cnblogs.com/programsky/p/4290024.html

# 3. 插入

### 不使用事务

https://www.cnblogs.com/rainbow70626/p/14157637.html

### 使用事务

https://www.cnblogs.com/wwg1990/p/10362667.html

# 4. dataGridView控件

https://www.cnblogs.com/monkeyZhong/p/4530795.html

https://www.cnblogs.com/my---world/p/12044302.html

http://c.biancheng.net/view/3040.html

http://www.360doc.com/content/14/0331/16/16325679_365222145.shtml

### 3.1 属性

* DataGridView.ReadOnly=true;	//不允许在表上编辑

* DataGridView.SelectionMode = DataGridViewSelectionMode.FullRowSelect;	//选中模式，整行选中  

* DataGridViewSelectionMode枚举类型

* DataGridView.AllowUserToAddRows = false 	//不允许用户自行添加一行

  设置为true时，表最后会空出一行，用户可以自行添加数据

* DataGridView.BackgroundColor=Color.White;    //设置表格背景颜色

* **DataGridView.Columns[9].Visible=false;     //设置第10列隐藏**

* DataGridView.Rows[9].Visible=false;     //设置第10行隐藏

* **dataGridView.Column.Count=num;	//事先设置列数，否则设置HeadText会越界**

* dataGridView.Column.Width;     //列宽度

### 3.2 删除行/列

（1）删除列

```C#
//删除列标题为Column1的列
DataGridView1.Columns.Remove("Column1");
//删除第一列 
DataGridView1.Columns.RemoveAt(0);

```

（2）删除行

在清除DataGridview的数据时遇到个问题。我想要清空DataGridview的数据，用了DataGridview.Rows.Clear()，这时就出错了，提示“不能清除此列表”。于是搜索了下，**用数据源绑定的DataGridView不能用Rows.Clear()清除**，手动添加的是能够用clear()的。所以将datasource设置为null就可以清空数据，但是这不是我要的效果，这样会将DataGridView的列也删掉。想要保持原有的列用如下代码就可以了，就是重新绑定一个没有数据的datatable。

```C#
//删除所有行，方法1
DataTable dt = (DataTable)dataGridView1.DataSource;
dt.Rows.Clear();         
dataGridView1.DataSource = dt;
//方法2，删除所有行
while (this.dgv1.RowCount != 0)
{
   this.dgv1.Rows.RemoveAt(0); //每次删除一行数据
}

//删除指定行
//删除第一行 
DataGridView1.Rows.RemoveAt(0); 

//删除选中行
foreach (DataGridViewRow r in DataGridView1.SelectedRows)
{
    if (!r.IsNewRow)
    {
        DataGridView1.Rows.Remove(r);
    }
}
```

（3）重新显示数据前清空dgv

```c#
 //清空。不用for判断因为RowCount在变
    while (this.dgv1.RowCount != 0)
     {
        this.dgv1.Rows.RemoveAt(0); //每次删除一行数据
     }
```

```C#
//如果使用
for (int i = 0; i < dgv.Rows.Count; i++)
{
if (dgv.Rows[i].Cells[8].Value.ToString() == “离线”)
{
dgv.Rows.Remove(dgv.Rows[i]);
}
else
{
	continue;
}
}
//删除当前行后 整个datagridview的所有行的索引会发生变化 i++后会跳过下一条数据 会造成删除本不应该删除的数据，应该在 dgv.Rows.Remove(dgv.Rows[i]); 后面加上i--使循环检索回到跳过检索的数据当中进行检索，满足条件就删除完整代码如下：
for (int i = 0; i < dgv.Rows.Count; i++)
{
if (dgv.Rows[i].Cells[8].Value.ToString() == “离线”)
{
dgv.Rows.Remove(dgv.Rows[i]);
i--;
}
else
{
continue;
}
}
```

### 3.3 修改列标题

 通过设置 DataGridView 控件的 DataSource 属性即可绑定 DataGridView 控件，但绑定后的 DataGridView 控件中的标题是数据表中的列名。如果需要更改 DataGridView 控件的列标题 ，用HeadText修改。

```mysql
DataGridView.Columns[0].HeadText="每一列名";
DataGridBiew.Columns[0].Width = 70;
```

### 3.4 绑定数据

（1）数据源绑定的方式

**绑定数据源后，列标题已经有了！！只不过是表中的列名**

```c#
//全部显示,绑定表
DataTable dt = new DataTable();
DataGridView.DataSource = dt；	//数据源指向表，就可以显示表中数据了
//绑定库
DataSet ds = new DataSet(); 
   da.Fill(ds); 
   this.dataGridView1.DataSource = ds.Tables[0]; 
}
```

**不能向没有列的 DataGridView 控件添加行。必须首先添加列。**

DataGridView 有个属性可以设置自动产生 列 AutoGenerateColumns=true ,而且默认值是为true 的， 你这种情况， 估计是没有绑定数据源就先向控件添加行了，这就相当于没有生成列就添加行了，这样操作是不允许的。所有产生异常，有两种解决办法，一种就是你先绑定数据源、然后再添加行。另外一种就是先手动给控件添加列，那就怎么都不会出错了。不过如果你的数据源是表格或者、对象集合的话，直接在表里面添加空行、添加对象，这样更容易些。

先对DataTable/DataSet进行操作，然后在绑定数据源。 

```c#
DataTable dt = new DataTable();//建立个数据表

dt.Columns.Add(new DataColumn("id", typeof(int)));//在表中添加int类型的列

dt.Columns.Add(new DataColumn("Name", typeof(string)));//在表中添加string类型的Name列

DataRow dr;//行

for (int i = 0; i < 3; i++)
{
      dr = dt.NewRow();	//新建一个行
      dr["id"] = i;	//给行的列赋值
      dr["Name"] = "Name" + i;
      dt.Rows.Add(dr);//在表里添加此行
}
dataGridView1.DataSource =dt;	//将表绑定为dgv1的数据源
```

（2）手动添加方式

**在手动添加行之前必须要先添加列**

```c#
int index = this.dataGridView.Rows.Add();	//增加一行，并返回行号。增加行前要先添加列
this.dataGridView.Rows[index].Cells[0].Value="1";	//列的序号访问
this.dataGridView.Rows[index].Cells["name"].Value="lihua";	//用字段访问
this.dataGridView.Rows[index].Cells[2].Value="28";
```

### 3.5 对调某两列

```mysql
//手动设置表头名，并将datatable另一列绑定该列

//交换第一列的id和第二列的name
DataGridView.Columns[0].HeadText="name";
DataGridView.Columns[1].HeadText="id";
DataGridView.Columns[0].DataPropertyName=DataTable.Column[1].ToString;	//将表中1列数据绑定到显示的第0列
DataGridView.Columns["id"].DataPropertyName=DataTable.Column[0].ToString;	//将表第0列数据绑定到显示的第1列。DataTable.Column[0].ToString是这一列的表头不是列中的数据。
```

DataPropertyName绑定数据源用的，DataGridView.Columns["id"].DataPropertyName="xx",将表中"xx"这一列与表中"id"这列绑定。

### 3.6 设置列宽度

```C#
this.dataGridView1.Columns[i].Width = 150;
```

**设置自适应列宽**

https://blog.csdn.net/kasama1953/article/details/51656495

```C#
    /// <summary>
    /// 使DataGridView的列自适应宽度
    /// </summary>
    /// <param name="dgViewFiles"></param>
    private void AutoSizeColumn(DataGridView dgViewFiles)
    {
        int width = 0;
        //使列自使用宽度
        //对于DataGridView的每一个列都调整
        for (int i = 0; i < dgViewFiles.Columns.Count; i++)
        {
            //将每一列都调整为自动适应模式
            dgViewFiles.AutoResizeColumn(i, DataGridViewAutoSizeColumnMode.AllCells);
            //记录整个DataGridView的宽度
            width += dgViewFiles.Columns[i].Width;
        }
        //判断调整后的宽度与原来设定的宽度的关系，如果是调整后的宽度大于原来设定的宽度，
        //则将DataGridView的列自动调整模式设置为显示的列即可，
        //如果是小于原来设定的宽度，将模式改为填充。
        if (width > dgViewFiles.Size.Width)
        {
            dgViewFiles.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.DisplayedCells;
        }
        else
        {
            dgViewFiles.AutoSizeColumnsMode = DataGridViewAutoSizeColumnsMode.Fill;
        }
        //冻结某列 从左开始 0，1，2
        dgViewFiles.Columns[1].Frozen = true;
    }
```



---

# 5. 向MySQL插入数据到datetime类型字段

## 5.1 日期格式

### （1）DateTime

```C#
.toString("yyyy-MM-dd HH:mm:ss.fff");	//mm-dd才显示08-01，否则显示8-1
//fff个数表示小数点后显示的位数，这里精确到小数点后3位
```

**注意：一定要注意大小写！！！M是月份，写成了m就是分钟数了！！**

### （2）DateTime转String

DateTime转string：https://www.cnblogs.com/JiYF/p/7831547.html

### （3）String转DateTime

string转DateTime：https://www.cnblogs.com/Pickuper/articles/2058880.html

## 5.2 C#插入Mysql的日期字段

https://blog.csdn.net/xcymorningsun/article/details/73234626



