https://blog.csdn.net/weixin_41732430/article/details/83753628?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162352061716780357228205%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=162352061716780357228205&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-2-83753628.first_rank_v2_pc_rank_v29&utm_term=C%23+%E6%93%8D%E4%BD%9Csqlite&spm=1018.2226.3001.4187

## 头文件

```C#
using System.Data.SQLite;
using System.Data;
```

```C#
string connStr="Data Source=;Password=;Version=;";
//创建连接对象sqliteConn
SQLiteConnection sqliteConn = new SQLiteConnection(connStr);
//打开文件，若文件不存在，将会创建文件
sqliteConn.Open();
//创建命令执行对象sqliteCmd
SQLiteCommand sqliteCmd = new SQLiteCommand(strCmd, sqliteConn);
//执行指令
sqliteCmd.ExecuteNonQuery();
```

## 打开数据库.db文件，连接数据库

```C#
//----------------------------------------------------
        //连接数据库
        //----------------------------------------------------
        public Boolean SQLite_connect()
        {
            bool flag = false;
            
            if (dataBaseName == "")
            {
                return false;
            }

            try
            {
                sqliteConn = new SQLiteConnection(connStr);
                sqliteConn.Open();
                flag = true;
            }
            catch (Exception ex)
            {
                ex.ToString();
                flag = false;
            }
            return flag;
        }

        //---------------------------------------------------------------
        //创建数据库
        //---------------------------------------------------------------
        public Boolean SQLite_createDataBase()
        {
            bool flag = false;
            if (dataBaseName == "")
            {
                return false;
            }

            try
            {
                if (System.IO.File.Exists(dataSource) == false)
                {
                    sqliteConn = new SQLiteConnection(connStr);
                    sqliteConn.Open();
                    flag = true;
                }
                else
                {
                    flag = false;
                }
            }
            catch (Exception ex)
            {
                ex.ToString();
            }
            return flag;
        }

```

## 删除数据库

直接删除.db文件即可

```C#
 public Boolean SQLite_deleteDataBase()
        {
            bool flag = false;
            if (dataBaseName == "")
            {
                return false;
            }

            try
            {
                if (System.IO.File.Exists(dataSource))
                {
                    System.IO.File.Delete(dataSource);
                    flag = true;
                }
            }
            catch (Exception ex)
            {
                ex.ToString();
                flag = false;
            }
            return flag;
        }
```

## 建表

```C#
        //------------------------------------------------
        //新建表
        //参数：建表指令,string strCmd = "CAMERA1 (BRAND TEXT, RESULT TINYINT, RIQI TIMESTAMP NOT NULL);";

        //------------------------------------------------
        public Boolean SQLite_createTable(string strCmd)
        {
            bool flag = false;
            strCmd = "CREATE TABLE IF NOT EXISTS " + strCmd;
            if (dataBaseName == "" || strCmd == "")
            {
                return false;
            }
            //没有库文件时不能建表
            if (System.IO.File.Exists(dataSource) == false)
            {
                return false;
            }

            try
            {
                if (sqliteConn.State != ConnectionState.Open)	//判断连接状态
                {
                    sqliteConn.Open();	//若未连接，则连接
                }
                SQLiteCommand sqliteCmd = new SQLiteCommand(strCmd, sqliteConn);
                sqliteCmd.ExecuteNonQuery();
                flag = true;
            }
            catch (Exception ex)
            {
                ex.ToString();
                flag = false;
            }
            return flag;
        }
```

## 删表

```C#
 public Boolean SQLite_deleteTable(string tableName)
        {
            bool flag = false;

            try
            {
                if (sqliteConn.State != ConnectionState.Open)
                {
                    sqliteConn.Open();
                }
                SQLiteCommand cmd = new SQLiteCommand();
                cmd.Connection = sqliteConn;
                cmd.CommandText = "DROP TABLE IF EXISTS " + tableName;
                cmd.ExecuteNonQuery();
                flag = true;
            }
            catch (Exception ex)
            {
                ex.ToString();
                flag = false;
            }
            return flag;
        }
```

## 插入记录

```C#
 //---------------------------------
        //插入记录
        //string cmd1 = "CAMERA1 (BRAND,RESULT,RIQI) VALUES (@brand,@result,STRFTIME('%Y-%m-%d %H:%M:%f', 'NOW'));";  //时间戳精确到ms

        //SQLiteParameter p1 = new SQLiteParameter("@ID", DbType.Int32);
		//p1.value=20;
		//使用事务时，transactionBegin="BEGIN TRANSACTION"，不用时，transactionBegin=""
        //---------------------------------
        public Boolean SQLite_insert(string strCmd, SQLiteParameter[] param, string transactionBegin, string transactionEnd)
        {
            bool flag = false;
            if (strCmd == "")
            {
                return false;
            }
            strCmd = transactionBegin + "INSERT INTO " + strCmd + transactionEnd;


            try
            {
                if (sqliteConn.State != ConnectionState.Open)
                {
                    sqliteConn.Open();
                }
                SQLiteCommand cmd = new SQLiteCommand();
                cmd.Connection = sqliteConn;
                cmd.CommandText = strCmd;
                foreach (var ele in param)
                {
                    cmd.Parameters.Add(ele);	//
                }
                cmd.ExecuteNonQuery();
                flag = true;
            }
            catch (Exception ex)
            {
                ex.ToString();
                flag = false;
            }
            return flag;
        }
```

## 删除记录

```C#
 //---------------------------------
        //删除记录
        //strCmd=`table_name` WHERE `col`=val
        //---------------------------------
        public Boolean SQLite_delete(string strCmd)
        {
            bool flag = false;
            if (strCmd == "")
            {
                return false;
            }
            strCmd = "DELETE FROM " + strCmd;


            try
            {
                if (sqliteConn.State != ConnectionState.Open)
                {
                    sqliteConn.Open();
                }
                SQLiteCommand cmd = new SQLiteCommand();
                cmd.Connection = sqliteConn;
                cmd.CommandText = strCmd;
                cmd.ExecuteNonQuery();
                flag = true;
            }
            catch (Exception ex)
            {
                ex.ToString();
                flag = false;
            }
            return flag;
        }
```

## 查询记录

```C#
 //---------------------------------
        //查询记录
        //string cmd = "CAMERA1 WHERE (`RIQI` BETWEEN '2021-06-03 02:10:52' AND '2021-06-03 02:10:57') AND `RESULT` = 1;";

        //---------------------------------
        public DataTable SQLite_search(string strCmd)
        {

            if (strCmd == "")
            {
                return null;
            }
            DataTable dt = new DataTable();
            strCmd = "SELECT * FROM " + strCmd;

            try
            {
                if (sqliteConn.State != ConnectionState.Open)
                {
                    sqliteConn.Open();
                }
                SQLiteDataAdapter adapter = new SQLiteDataAdapter(strCmd, sqliteConn);
                SQLiteCommandBuilder builder = new SQLiteCommandBuilder(adapter);
                adapter.Fill(dt);
            }
            catch (Exception ex)
            {
                ex.ToString();
                return null;
            }
            return dt;
        }

```

## 计数

```C#
//----------------------------------------------------------
        //查询计数
        //strCmd=table_name WHERE 
        //----------------------------------------------------------
        public Int32 SQLite_count(string strCmd)
        {
            Int32 count = 0;
            if (strCmd == "")
            {
                return -1;
            }

            try
            {
                if (sqliteConn.State != ConnectionState.Open)
                {
                    sqliteConn.Open();
                }
                strCmd = "SELECT COUNT(*) FROM " + strCmd;
                SQLiteCommand cmd = new SQLiteCommand();
                cmd.Connection = sqliteConn;
                cmd.CommandText = strCmd;
                SQLiteDataReader reader = cmd.ExecuteReader();
                reader.Read();
                count = reader.GetInt32(0);
            }
            catch (Exception ex)
            {
                ex.ToString();
            }
            return count;
        }
```

## 建索引

```C#
//----------------------------------------------------------
        //创建索引
        //string cmd = "DATATIME_index ON CAMERA1(RIQI);";

        //----------------------------------------------------------
        public Boolean SQLite_index(string strCmd)
        {
            bool flag = false;
            if (strCmd == "")
            {
                return false;
            }
            strCmd = "CREATE INDEX " + strCmd;

            try
            {
                if (sqliteConn.State != ConnectionState.Open)
                {
                    sqliteConn.Open();
                }
                SQLiteCommand cmd = new SQLiteCommand();
                cmd.Connection = sqliteConn;
                cmd.CommandText = strCmd;
                cmd.ExecuteNonQuery();

            }
            catch (Exception ex)
            {
                ex.ToString();
                flag = false;
            }
            return flag;
        }

    }
}
```







