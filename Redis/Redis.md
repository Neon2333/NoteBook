# Redis基础

## 1. 安装

https://blog.csdn.net/weixin_44893902/article/details/123087435

```redis
redis-server.exe redis.windows.conf	//启动服务
redis-cli.exe -h 127.0.0.1 -p 6379	//连接Redis客户端

```

## 2. 工具-RedisInsight



## 3. 字符串

Redis中，数据以**键值对**形式存储，key和value都是以**二进制存储**。

key区分大小写。

### （1）设置获取

```sql
127.0.0.1:6379> set name wk	#给key赋值
OK
127.0.0.1:6379> get name	#获取value
"wk"
127.0.0.1:6379> set Name WK	#key区分大小写
OK
127.0.0.1:6379> get Name
"WK"
127.0.0.1:6379> del Name	#删除一个key
(integer) 1
127.0.0.1:6379> get Name
(nil)
127.0.0.1:6379> EXISTS Name	#判断key是否存在
(integer) 0
127.0.0.1:6379> keys *		#查找所有的key
1) "name"
127.0.0.1:6379> keys *me	#查找所有以me结尾的key
1) "name"
127.0.0.1:6379> flushall	#删除所有key
```

### （2）显示中文

```bash
127.0.0.1:6379> set age 30ￋ￪	#value是中文
OK
127.0.0.1:6379> get age
"30\xcb\xea"					#以二进制形式存储key和value，显示也是以二进制显示
127.0.0.1:6379> quit		

C:\Users\Administrator>redis-cli --raw	#退出客户端，加上--raw参数表示以原本形式显示value
127.0.0.1:6379> get age
30岁								#中文正常显示了
127.0.0.1:6379>
```

### （3）设置key的过期时间

expire key seconds设置过期时间

```bash
127.0.0.1:6379> TTL age		#获取age的过期时间。-1表示未设置过期时间
-1
127.0.0.1:6379> expire age 20	#expire key seconds设置过期时间
1
127.0.0.1:6379> TTL age
17
127.0.0.1:6379> TTL age
15
127.0.0.1:6379> TTL age
14
127.0.0.1:6379> TTL age
14
127.0.0.1:6379> TTL age
13
127.0.0.1:6379> TTL age
7
127.0.0.1:6379> TTL age
2
127.0.0.1:6379> TTL age
-2								#-2表示已过期
127.0.0.1:6379> get age			
							#过期后再获取age就为空了
127.0.0.1:6379>
```

setex key [**seconds**] [value]		#设置一个带有过期时间的键值对

```bash
127.0.0.1:6379> setex age 20 30ￋ￪
OK
127.0.0.1:6379> get age
30岁
127.0.0.1:6379> ttl age
12
127.0.0.1:6379>
```

## 4. 列表List



