# 集合


[集合](https://segmentfault.com/a/1190000040177363)

## 常见的集合有哪些？

* Java集合类主要由两个根接口`Collection`和`Map`派生出来的 
* Collection派生出了三个子接口：`List、Set、Queue`（Java5新增的队列）
* 因此Java集合大致也可分成`List、Set、Queue、Map`四种接口体系。

**注意：** 
Collection是一个接口，Collections是一个工具类，Map不是Collection的子接口。

![集合](/img/集合/img.png)

Collection体系简介图

![Collection体系](/img/Collection体系原理/Collection体系.png)

* `List`代表了有序可重复集合，可直接根据元素的索引来访问
* `Set`代表无序不可重复集合，只能根据元素本身来访问
* `Queue`是队列集合 
* `Map`代表的是存储key-value对的集合，可根据元素的`key`来访问`value`

## 对Arraylist的源码理解？

* 首先，Arraylist属于线性表的结构，他其实是JDK封装的可变长数组，它的默认长度是10，当仅当第一个元素填加进来以后，他会自动把他设置成10，
然后后面再添加元素进来，其实每次都会先检查容量的大小，如果超过当前长度就会发生扩容。
Arraylist怎么扩容的？他就是底层调用c++将现在的长度变成原来的1.5倍，新数组长度等于旧数组长度加上旧数组长度右移1位。
Arraylist删除元素，比如说删除中间的一个元素，它会采用覆盖的形式将后面的元素依次往前挪，最后的一个元素就设置null,等着GC去回收掉。

* Arraylist相当于在内存空间上是一段连续的空间
* Arraylist：底层使用的是`Object` 数组。

## ArrayList和LinkedList的区别？

* 首先ArrayList和LinkedList啊，他们都是实现了这个List接口，那我们的ArrayList他们是基于这个索引的一个数据接口，
他的底层是数组，那他也可以以这个O(1)的一个时间复杂度，对我们的元素进行一些随机访问，那么LinkedList呢，是以我们这个元素啊，
以我们这个元素列表的形式去存储他的这个数据，他的每一个元素和他的起前一个和后一个元素呢，链接在一起，
那在这种情况下呢，就是我们去查找某个元素的一个时间复杂度，他是O(n)的，
相对我们的ArrayList，那这个LinkedList，他的一个插入删除会比较快很多。
ArrayList的查询比较快。LinkedList新增删除比较快呢，原因是当我们这个元素被添加到这个集合的一个任意的一个位置的时候啊，
他不需要像数组那样去重新计算我们这个整个大小，或者说更新这个索引，所以LinkedList呢，他相对我们这个ArrayList，他会更占内存，
因为LinkedList他等于为了每一个节点的一个存储了两个引用，一个指向前一个元素，一个指向后一个元素啊，大概就是这样。

## HashMap 和 Hashtable区别？

* 如果是在我们这个存储方面的话，我们的Hashmap是以这个key和value而是为null的，那在我们的Hashtable里面，就是不允许的。
如果是站在线程安全角度上的话，我们的Hashtable他是线程安全的，而Hashmap是非线程安全的，
我有在网上看一些资料，他们推荐使用的是说，因为在我们的Hashtable里面的一个类注释里面，是可以看到的，
我们的Hashtable是一个保留类，它是不推荐不建议我们使用的，所以比较推荐的是在我们单线程的一个环境下，
可以使用我们的HashMap去替代，如果我们有这个多线程业务的话，他是推荐我们去使用我们的这个ConcurrentHashMap去替代。

## HashMap 与 ConcurrentHashMap 区别

![HashMap 与 ConcurrentHashMap 区别](/img/集合/img_1.png)

## HashMap

* 负载因子0.75
* 默认16，超过16*0.75=12，就会自动扩容，扩容就是翻倍。将元素移动到新数组，在1.8过程中会升级为红黑树。

* HashMap从Java7到Java8发生了哪些变化？
1. JDK 7 是先对 size++ 进行检查， 如果超过阈值， 则扩容，最后把节点放入 table。
2. 而 JDK 8 相反，先把节点放入， 放入后的 size 若超出， 则扩容

## Hashset

* HashMap分为key和value,Hashset内部实现有个HashMap，他就相当于HashMap里的key。

## Treeset

* [红黑树](https://zh.wikipedia.org/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)
* 保证一个平衡二叉树
* 二叉查找树：左节点比右节点小，每次查找会缩半。


## 线程安全的集合有哪些？线程不安全的呢？

线程安全的：

* Hashtable：比HashMap多了个线程安全。 
* ConcurrentHashMap:是一种高效但是线程安全的集合。 
* Vector：比Arraylist多了个同步化机制。 
* Stack：栈，也是线程安全的，继承于Vector。

线性不安全的：

* HashMap 
* Arraylist 
* LinkedList 
* HashSet （无序）
* LinkedHashSet（有序）
* TreeSet （排序）
* TreeMap

