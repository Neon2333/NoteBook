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



# ArrayList——动态数组

## 1. 特点

* ArrayList可以存储不同类型的元素。这是由于ArrayList会把它的元素都当做Object来处理。因而，加入不同类型的元素是允许的。

* 不必在声明ArrayList时指定它的长度，这是由于ArrayList对象的长度是按照其中存储的数据来动态增长与缩减的

* 若要使用则必须引入System.Collections

* 那是因为ArrayList可以存储不同类型数据的原因是由于把所有的类型都当做Object来做处理，也就是说ArrayList的元素其实都是Object类型的，辣么问题就来了。

  ArrayList不是类型安全的。因为把不同的类型都当做Object来做处理，很有可能会在使用ArrayList时发生类型不匹配的情况。
  如上文所诉，数组存储值类型时并未发生装箱，但是**ArrayList由于把所有类型都当做了Object，所以不可避免的当插入值类型时会发生装箱操作，在索引取值时会发生拆箱操作**。这能忍吗？

  ```
  	注：为何说频繁的没有必要的装箱和拆箱不能忍呢？且听小匹夫慢慢道来：所谓装箱 (boxing)：
  	就是值类型实例到对象的转换(百度百科)。那么拆箱：就是将引用类型转换为值类型咯（还是来自百度百科）。下面举个栗子~
  12
  //装箱，将值类型转成引用类型
  int  info = 1989;  
  object obj=(object)info;  
  
  //拆箱，引用类型转换成值类型
  object obj = 1;
  int info = (int)obj;
  1234567
  ```

  显然，从原理上可以看出，装箱时，生成的是全新的引用对象，这会有时间损耗，也就是造成效率降低。

## 2. 方法



# List——链表

## 1. 特点

* 向链表中插入或删除节点无需调整结构的容量。因为本身不是连续存储而是靠各对象的指针所决定，所以添加元素和删除元素都要比数组要有优势。

* 链表适合在需要有序的排序的情境下增加新的元素，这里还拿数组做对比，例如要在数组中间某个位置增加新的元素，则可能需要移动移动很多元素，而对于链表而言可能只是若干元素的指向发生变化而已。

* 有优点就有缺点，由于其在内存空间中不一定是连续排列，所以访问时候无法利用下标，而是必须从头结点开始，逐次遍历下一个节点直到寻找到目标。所以当需要快速访问对象时，数组无疑更有优势。

综上，链表适合元素数量不固定，需要经常增减节点的情况。

