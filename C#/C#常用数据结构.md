# Dictionary

 ## 1.特点

* 必须包含名空间System.Collection.Generic

* Dictionary里面的每一个元素都是一个键值对(由二个元素组成：键和值)

  ```C#
  Dictionary<int, faultsIndexAndStatus> faultsOriginal = new Dictionary<int, faultsIndexAndStatus>();
  ```

* 键必须是唯一的,而值不需要唯一的

* 键和值都可以是任何类型(比如：string, int, 自定义类型，等等)

* 通过一个键读取一个值的时间是接近O(1)





# HashTable

## 1.特点

* Hashtable 类代表了一系列基于键的哈希代码组织起来的**键/值**对。它使用**键**来访问集合中的元素

* 命名空间**using** System.Collections;
* Hashtable的Key和Value都是object类型，所以在使用值类型的时候，必然会出现装箱和拆箱的操作，因此性能肯定是不如Dictionary的

```C#
int a = 1;
string b = "222";
HashTable ht = new HashTable();
ht.Add(a, b);	

foreach(DictionaryEntry de in ht){
    int c = (int)de.Key;	//取出时是object类型
    string d = (string)de.Value;
}
```

## 2.方法

https://www.cnblogs.com/xpvincent/archive/2013/01/15/2860841.html