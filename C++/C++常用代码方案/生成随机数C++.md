# 生成随机数宏定义
```
#define RANDOM rand()/double(RAND_MAX)	        //doube:0~1
#define RANDOM_INT1(a,b) (rand() % (b-a+1))+ a	//[a,b]
#define RANDOM_INT2(a,b) (rand() % (b-a))+ a	//[a,b)
#define RANDOM_INT2(a,b) (rand() % (b-a))+ a + 1	//(a,b]
```

# 相关链接
[C++生成简单随机数](https://www.cnblogs.com/xiaokang01/p/9786751.html)

[随机数](https://blog.csdn.net/qq_34784753/article/details/79600809)

[随机数](https://www.cnblogs.com/byhj/p/4149467.html)