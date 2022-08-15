# 集合


[集合](https://segmentfault.com/a/1190000040177363)

## 常见的集合有哪些？

* Java集合类主要由两个根接口`Collection`和`Map`派生出来的 
* Collection派生出了三个子接口：`List、Set、Queue`（Java5新增的队列）
* 因此Java集合大致也可分成`List、Set、Queue、Map`四种接口体系。

**注意：** 
Collection是一个接口，Collections是一个工具类，Map不是Collection的子接口。

![集合](/img/集合/img.png)

* `List`代表了有序可重复集合，可直接根据元素的索引来访问
* `Set`代表无序不可重复集合，只能根据元素本身来访问
* `Queue`是队列集合 
* `Map`代表的是存储key-value对的集合，可根据元素的`key`来访问`value`

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
* HashSet 
* TreeSet 
* TreeMap


## Collection体系的常用类及其背后的数据结构

### Collection常用类

#### list

有序可重复，拥有下标,最常用的是 ArrayList，实际就是一个数组

常用类：
* ArrayList
* LinkedList

#### set
无序且不可重复的元素集合,没有下标。
* HashSet（无序）
* LinkedHashSet（有序）
* TreeSet（排序）

Collection体系简介图

![Collection体系](/img/Collection体系原理/Collection体系.png)

### Collection背后的数据结构

#### ArrayList与LinedList
* Arraylist：底层使用的是`Object` 数组。
* LinkedList：底层使用的是 `双向链表` 数据结构。

#### HashSet、LinkedHashSet与TreeSet

* HashSet: 底层采用 `HashMap` 来保存元素，`HashMap` 使用的是拉链法，也叫作链地址法。
* LinkedHashSet：底层是链表+哈希表，链表保证数据存储有序，链表用来记录存储顺序；哈希表保证数据唯一，存储真正的数据
* TreeSet：[红黑树](https://zh.wikipedia.org/wiki/%E7%BA%A2%E9%BB%91%E6%A0%91)

## ArrayList源码阅读

### 继承结构
`ArrayList `extends` AbstractList` <br>
`AbstractList `extends `AbstractCollection `

### 构造方法

ArrayList有三个构造方法

**无参构造方法**

```java
/**
* Constructs an empty list with an initial capacity of ten.　　默认会给10的大小，所以说一开始arrayList的容量是10.
*/
　　　　//ArrayList中储存数据的其实就是一个数组，这个数组就是elementData，在123行定义的 private transient Object[] elementData;
　　     public ArrayList() {　　
            super();        //调用父类中的无参构造方法，父类中的是个空的构造方法
            this.elementData = EMPTY_ELEMENTDATA;  //EMPTY_ELEMENTDATA：是个空的Object[]， 将elementData初始化，elementData也是个Object[]类型。空的Object[]会给默认大小10，等会会解释什么时候赋值的。
}
```

**有参构造方法**

```java
/**
     * Constructs an empty list with the specified initial capacity.
     *
     * @param  initialCapacity  the initial capacity of the list
     * @throws IllegalArgumentException if the specified initial capacity
     *         is negative
     */
    public ArrayList(int initialCapacity) {
        super(); //父类中空的构造方法
        if (initialCapacity < 0)    //判断如果自定义大小的容量小于0，则报下面这个非法数据异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity]; //将自定义的容量大小当成初始化elementData的大小
    }
```

**有参构造方法**~~(不常用)~~

```java
//这个构造方法不常用，举个例子就能明白什么意思
    /*
        Strudent exends Person
         ArrayList<Person>、 Person这里就是泛型
        我还有一个Collection<Student>、由于这个Student继承了Person，那么根据这个构造方法，我就可以把这个Collection<Student>转换为ArrayList<Sudent>这就是这个构造方法的作用
    */
     public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();    //转换为数组
        size = elementData.length;   //数组中的数据个数
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class) //每个集合的toarray()的实现方法不一样，所以需要判断一下，如果不是Object[].class类型，那么久需要使用ArrayList中的方法去改造一下。
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    }　　
```

### add()方法

`add(E)` //默认直接在末尾添加元素

`add(int，E)`//在特定位置添加元素，也就是插入元素

### remove()方法

`remove(int)`//通过删除指定位置上的元素

`remove(Object)`//这个方法可以看出来，arrayList是可以存放null值得。

`clear()`//将elementData中每个元素都赋值为null，等待垃圾回收将这个给回收掉，所以叫clear

### set()方法

设定指定下标索引的元素值

### indexOf()方法

从头开始查找与指定元素相等的元素，注意，是可以查找null元素的，意味着ArrayList中可以存放null元素的。与此函数对应的lastIndexOf，表示从尾部开始查找。

### get()方法

get函数会检查索引值是否合法（只检查是否大于size，而没有检查是否小于0）

## ArrayList是如何扩容的？

**扩容操作**

```java
/**
 * The maximum size of array to allocate.
 * Some VMs reserve some header words in an array.
 * Attempts to allocate larger arrays may result in
 * OutOfMemoryError: Requested array size exceeds VM limit
 */
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

/**
 * Increases the capacity to ensure that it can hold at least the
 * number of elements specified by the minimum capacity argument.
 *
 * @param minCapacity the desired minimum capacity
 */
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    // 新容量扩大到原容量的1.5倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        // 如果新容量还是比所需的最小容量小，则让新容量等于所需的最小容量
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        // 如果新容量超过了Integer.MAX_VALUE - 8，继续计算
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    // 所需的最小容量minCapacity 接近size
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

扩容计算，`int newCapacity = oldCapacity + (oldCapacity >> 1);` oldCapacity 是ArrayList 内部数组长度，oldCapacity >> 1 是位运算的右移操作，右移一位相当于除以2，新的容量 newCapacity 为之前容量的1.5倍。

elementData = Arrays.copyOf(elementData, newCapacity); 对 elementData 数组进行扩容。

```java
private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}
```
**ArrayList 扩容每次都是原容量的1.5倍吗？**

从源码中可以看出，当使用无参构造方法创建一个 ArrayList 实例，调用 add 方法添加第一个元素的时候，calculateCapacity 方法返回的是默认初始容量 DEFAULT_CAPACITY 大小为10；当使用指定初始容量创建ArrayList 实例，调用 addAll 方法添加多个元素的时候，原容量的1.5倍也无法存放元素的时候，会创建一个更大（不会超过 Integer.MAX_VALUE）的数组来存放元素。


## HashMap源码阅读

### put方法

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
    }

    //第四个参数是，只有当key对应的位置为空的时候，才进行替换，一般设置为false
    //第五个参数如果是false，表示是在第一次放置+初始化数组容量的时候调用。
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        //如果table数组为空，则进行第一次resize,扩容到初始容量
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        //如果key在数组中映射的位置上的元素为空，没有产生哈希冲突，则直接放置
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);

        else {
            Node<K,V> e; K k;
            //如果key值相同，则直接覆盖
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            //如果key值不同，则产生了哈希冲突，需要解决冲突
            else if (p instanceof TreeNode)//如果当前是个树节点，则需要往树上放置元素
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                //是个链表
                for (int binCount = 0; ; ++binCount) {
                    //如果走到链表的末尾，则直接新建一个节点，插入到链表末尾
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        //判断需不需要进行变形，把链表变成红黑树，提高查找效率
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    //如果当前的key值和链表上的某个key值相同
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    //指针移动
                    p = e;
                }
            }
            //如果循环结束后，e不等于null，则e的value值需要被替换成新的value值
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                //HashMap的这个函数是空的，LinkedHashMap继承HashMap重写了这个方法，用来实现插入有序，或者LRU访问
                afterNodeAccess(e);
                return oldValue;
            }
        }
        //修改数++
        ++modCount;
        //如果当前数组的容量超过了扩容的阈值，则进行扩容
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

### get方法

```java
//根据key值获取
    public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }

    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        //table数组不为空,且length>0，且hash值和数组长度做&运算得到的那个bucket不为空
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            //如果是第一个节点，则直接返回第一个节点
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            //开始找下一个节点
            if ((e = first.next) != null) {
                //如果下一个节点是红黑树节点
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key); //则开始在树上找节点
                do {
                    //如果是链表节点，一直遍历链表，知道找到。
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        //否则直接返回空
        return null;
    }
```

## HashMap是如何扩容的？

HashMap 中的 resize 方法主要包含两部分逻辑：

1. 初始化数组 table，并设置阈值。
2. 数组容量翻倍，将元素迁移到新数组。

```java
/**
 * Initializes or doubles table size.  If null, allocates in
 * accord with initial capacity target held in field threshold.
 * Otherwise, because we are using power-of-two expansion, the
 * elements from each bin must either stay at same index, or move
 * with a power of two offset in the new table.
 *
 * @return the table
 */
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) { // 第一次进来，table为null，oldCap为0，不会进入这里
        if (oldCap >= MAXIMUM_CAPACITY) { // 扩容前的数组大小如果已经达到最大(2^30)了
            threshold = Integer.MAX_VALUE; // 取整型最大值(2^31-1)，这样以后就不会扩容了
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY && // oldCap翻倍得到newCap
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold // 第一次进来，如果手动设置了初始容量initialCapacity，这里为true，则将threshold作为初始容量
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults // 如果没有手动设置initialCapacity，则设为默认值16
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) { // 第一次进来，这里必为true，重新计算 threshold = capacity * Load factor
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) { // 对oldTab中所有元素进行rehash。由于每次扩容是2次幂的扩展(指数组长度/桶数量扩为原来2倍)，所以，元素的位置要么是在原位置，要么是在原位置再移动2次幂的位置
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) { // 数组j位置的元素不为空，需要该位置上的所有元素进行rehash
                oldTab[j] = null;
                if (e.next == null) // 桶中只有一个元素，则直接rehash
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode) // 桶中是树结构
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order // 桶中是链表结构（JDK1.7中旧链表迁移新链表的时候，用的是头插法，如果在新表的数组索引位置相同，则链表元素会倒置；但是JDK1.8不会倒置，用的是双指针）
                    Node<K,V> loHead = null, loTail = null; // low位链表，其桶位置不变，head和tail分别代表首尾指针
                    Node<K,V> hiHead = null, hiTail = null; // high位链表，其桶位于追加后的新数组中
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) { // 是0的话索引没变，是1的话索引变成“原索引+oldCap”
                            if (loTail == null)
                                loHead = e; // 总是指向头结点
                            else
                                loTail.next = e; // 该操作有可能会改变原链表结构
                            loTail = e; // 总是指向下一个节点，直到尾节点
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead; // 原索引
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead; // 原索引+oldCap
                    }
                }
            }
        }
    }
    return newTab;
}
```

HashMap 每次扩容都是建立一个新的 table 数组，长度和容量阈值都变为原来的两倍，然后把原数组元素重新映射到新数组上，具体步骤如下：

1. 首先会判断 table 数组长度，如果大于 0 说明已被初始化过，那么按当前 table 数组长度的 2 倍进行扩容，阈值也变为原来的 2 倍

2. 若 table 数组未被初始化过，且 threshold(阈值)大于 0 说明调用了 HashMap(initialCapacity, loadFactor) 构造方法，那么就把数组大小设为 threshold

3. 若 table 数组未被初始化，且 threshold 为 0 说明调用 HashMap() 构造方法，那么就把数组大小设为 16，threshold 设为 16*0.75

4. 接着需要判断如果不是第一次初始化，那么扩容之后，要重新计算键值对的位置，并把它们移动到合适的位置上去，如果节点是红黑树类型的话则需要进行红黑树的拆分

## HashMap从Java7到Java8发生了哪些变化？

1. JDK 7 是先对 size++ 进行检查， 如果超过阈值， 则扩容，最后把节点放入 table。
2. 而 JDK 8 相反，先把节点放入， 放入后的 size 若超出， 则扩容

## 为什么HashMap不是线程安全的？

* JDK1.7 中，由于多线程对HashMap进行扩容，调用了HashMap#transfer()，具体原因：某个线程执行过程中，被挂起，其他线程已经完成数据迁移，等CPU资源释放后被挂起的线程重新执行之前的逻辑，数据已经被改变，造成死循环、数据丢失。
* JDK1.8 中，由于多线程对HashMap进行put操作，调用了HashMap#putVal()，具体原因：假设两个线程A、B都在进行put操作，并且hash函数计算出的插入下标是相同的，当线程A执行完第六行代码后由于时间片耗尽导致被挂起，而线程B得到时间片后在该下标处插入了元素，完成了正常的插入，然后线程A获得时间片，由于之前已经进行了hash碰撞的判断，所有此时不会再进行判断，而是直接进行插入，这就导致了线程B插入的数据被线程A覆盖了，从而线程不安全。


## Collection体系简介
### list

```java
Collection<Integer> c = new LinkedHashSet<>();
        
// IntegerList
List<Integer> list = new ArrayList<>(c);
        
// 等价于
List<Integer> list2 = new ArrayList<>();
list2.addAll(c);
        
// 等价于
List<Integer> list3 = new ArrayList<>();
for (Integer i : c) {
    list3.add(i);
}
```
### Set


只是简单通过object.contains()判断添加新元素时是否重复，从而实现去重的 Set 是很低效的，这就引出了对象的 hashcode。

### Java世界第⼆重要的约定
hashCode:
1. 同⼀个对象必须始终返回相同的 hashCode
2. 两个对象的 equals 返回 true，必须返回相同的 hashCode
3. 两个对象不等，也可能返回相同的 hashCode

### HashSet
HashSet 是无序的，是最常用的 Set 实现。
可以利用 set 为 list 过滤去重：
```java
List<Integer> list = new ArrayList<>();

list.add(2);
list.add(3);
list.add(4);
list.add(3);
        
Set<Integer> set = new HashSet<>(list); 
```
LinkedHashSet 是有序的，顺序就是插入元素时的顺序。
### Collections ⼯具⽅法集合
* emptySet(): 等返回⼀一个⽅方便便的空集合
* synchronizedCollection: 将⼀一个集合变成线程安全的
* unmodifiableCollection: 将⼀一个集合变成不不可变的（也可以使⽤用Guava的Immutable）
## Map 体系
map 是 一个将 keys 映射到 values 的对象，键不能重复，每个键只能映射一个值，值可以重复。 <br>
* `keySet()`返回键的集合，因为键不可重复，所以可以返回一个 set； <br>
* `values()`返回值的集合，因为值可以重复，所以返回的是 collection。 <br>
  注意：`keySet()`和 `map`背后的 keys 是同一组数据，所以二者的修改会相互影响。 <br>
  <br>
### HashMap
* HashMap 是最常用、最高效的 Map 实现。
* HashMap 的扩容，思路同样是创建更大的空间，然后把之前的数据 copy 进来。
* HashMap 是多线程不安全的，可使用 ConcurrentHashMap。<br>

Java 7 开始会采用代替链表

HashMap 和 HashSet 本质上是同一个东西：<br>
HashMap 的 key 集合（set）就是 HashSet，而 HashSet 内部其实就是个 HashMap，毕竟 HashSet 拥有的功能 HashMap 都有。
### 有序集合TreeSet/TreeMap
TreeSet 可以排序（默认是自然顺序）。<br>
HashSet、LinkedHashSet 与 TreeSet 对比：
```java
public class Main {

    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1000, -13, 0, -41656, 1250, 555);

        Set set1 = new HashSet<>(list);
        Set set2= new LinkedHashSet(list);
        Set set3 = new TreeSet(list);

        System.out.println(set1);
        System.out.println(set2);
        System.out.println(set3);
    }
}

[0, 1250, -41656, 1000, 555, -13]
[1000, -13, 0, -41656, 1250, 555]
[-41656, -13, 0, 555, 1000, 1250]
```
TreeMap 同理
## Guava
* 不要重复发明轮⼦子！尽量量使⽤用经过实战检验的类库
* Lists/Sets/Maps
* ImmutableMap/ImmutableSet
* Multiset/Multimap
* BiMap

# HashMap

相比于JDK7，HashMap在JDK8中做链表结构做了优化（但仍然线程不安全），在一定条件下将链表转为红黑树，提升查询效率。

## put()操作

* 先根据key确定在哈希table中的下标，找到对应的bucket，遍历链表（或红黑树），做插入操作。
* 在JDK7中，新增结点是使用头插法，但在JDK8中，在链表使用尾插法，将待新增结点追加到链表末尾。

![Put](/img/HashMap/1.png)

红黑树的转换操作如下：

```java
/**
 * Replaces all linked nodes in bin at index for given hash unless
 * table is too small, in which case resizes instead.
 */
final void treeifyBin(Node<K,V>[] tab, int hash) {
    int n, index; Node<K,V> e;
    // 若表为空，或表长度小于MIN_TREEIFY_CAPACITY，也不做转换，直接做扩容处理。
    if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
        resize();
    else if ((e = tab[index = (n - 1) & hash]) != null) {
        TreeNode<K,V> hd = null, tl = null;
        do {
            TreeNode<K,V> p = replacementTreeNode(e, null);
            if (tl == null)
                hd = p;
            else {
                p.prev = tl;
                tl.next = p;
            }
            tl = p;
        } while ((e = e.next) != null);
        if ((tab[index] = hd) != null)
            hd.treeify(tab);
    }
}
```

## 扩容操作

什么场景下会触发扩容？

* 哈希table为null或长度为0；
* Map中存储的k-v对数量超过了阈值threshold；
* 链表中的长度超过了TREEIFY_THRESHOLD，但表长度却小于MIN_TREEIFY_CAPACITY。

一般的扩容分为2步，第1步是对哈希表长度的扩展（2倍），第2步是将旧table中的数据搬到新table上。

那么，在JDK8中，HashMap是如何扩容的呢？

```java
// 前面已经做了第1步的长度拓展，我们主要分析第2步的操作：如何迁移数据
table = newTab;
if (oldTab != null) {
    // 循环遍历哈希table的每个不为null的bucket
    // 注意，这里是"++j"，略过了oldTab[0]的处理
    for (int j = 0; j < oldCap; ++j) {
        Node<K,V> e;
        if ((e = oldTab[j]) != null) {
            oldTab[j] = null;
            // 若只有一个结点，则原地存储
            if (e.next == null)
                newTab[e.hash & (newCap - 1)] = e;
            else if (e instanceof TreeNode)
                ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
            else { // preserve order
                // "lo"前缀的代表要在原bucket上存储，"hi"前缀的代表要在新的bucket上存储
                // loHead代表是链表的头结点，loTail代表链表的尾结点
                Node<K,V> loHead = null, loTail = null;
                Node<K,V> hiHead = null, hiTail = null;
                Node<K,V> next;
                do {
                    next = e.next;
                    // 以oldCap=8为例，
                    //   0001 1000  e.hash=24
                    // & 0000 1000  oldCap=8
                    // = 0000 1000  --> 不为0，需要迁移
                    // 这种规律可发现，[oldCap, (2*oldCap-1)]之间的数据，
                    // 以及在此基础上加n*2*oldCap的数据，都需要做迁移，剩余的则不用迁移
                    if ((e.hash & oldCap) == 0) {
                        // 这种是有序插入，即依次将原链表的结点追加到当前链表的末尾
                        if (loTail == null)
                            loHead = e;
                        else
                            loTail.next = e;
                        loTail = e;
                    }
                    else {
                        if (hiTail == null)
                            hiHead = e;
                        else
                            hiTail.next = e;
                        hiTail = e;
                    }
                } while ((e = next) != null);
                if (loTail != null) {
                    loTail.next = null;
                    newTab[j] = loHead;
                }
                if (hiTail != null) {
                    hiTail.next = null;
                    // 需要搬迁的结点，新下标为从当前下标往前挪oldCap个距离。
                    newTab[j + oldCap] = hiHead;
                }
            }
        }
    }
}
```

## get()操作

* 先根据key计算hash值，进一步计算得到哈希table的目标index
* 若此bucket上为红黑树，则再红黑树上查找，若不是红黑树，遍历链表。

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}


final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

## HashMap、HashTable是什么关系？

共同点:

* 底层都是使用哈希表 + 链表的实现方式。

异同点:

* 从层级结构上看，HashMap、HashTable有一个共用的Map接口。另外，HashTable还单独继承了一个抽象类Dictionary；
* HashTable线程安全，HashMap线程不安全；
* 初始值和扩容方式不同。HashTable的初始值为11，扩容为原大小的`2*d+1`。
  容量大小都采用奇数且为素数，且采用取模法，这种方式散列更均匀。
  但有个缺点就是对素数取模的性能较低（涉及到除法运算）,
  而HashTable的长度都是2的次幂，设计就较为巧妙，这种方式的取模都是直接做位运算，性能较好。
* HashMap的key、value都可为null，且value可多次为null，key多次为null时会覆盖。
  当HashTable的key、value都不可为null，否则直接NPE(NullPointException)。

## 保证HashMap的线程安全

* 可以添加synchronized关键字





