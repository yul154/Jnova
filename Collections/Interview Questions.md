![image](https://user-images.githubusercontent.com/27160394/138216027-a9bd8b72-bce9-4e36-a45a-0811ddf473f8.png)

## List

> ArrayList 如何扩容
ArrayList 内部是通过 Object 数组实现的，而数组的长度一经定义，就无法更改了

```
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```
* 首先调用`ensureCapacityInternal`方法，入参`minCapacity`为`ArrayList`包含的实际元素个数`size + 1`

```
private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private static int calculateCapacity(Object[] elementData, int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        // 如果是空ArrayList，则容量为 DEFAULT_CAPACITY 和 minCapacity 的最大值
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}
```
* 如果通过无参构造方法进行创建的
 * 那么满足下`elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA`，如果是添加第一个元素，则minCapacity 为 1，则数组扩容到 DEFAULT_CAPACITY 大小为10
 * `addAll`方法可以一次向 `ArrayList`中添加多个元素，新增加的元素个数可能大于`DEFAULT_CAPACITY`

```
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity); // 容量不够的话，会调用 grow 方法 进行扩容操作。
}
```
* ensureExplicitCapacity 方法确保 ArrayList 有足够的容量存放新的元素。


```
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

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}

```
* `oldCapacity >> 1`是位运算的右移操作，右移一位相当于除以2，新的容量`newCapacity`为之前容量的1.5倍


> ArrayList 扩容每次都是原容量的1.5倍吗？
* 当使用无参构造方法创建一个 ArrayList 实例，调用 add 方法添加第一个元素的时候，calculateCapacity 方法返回的是默认初始容量 DEFAULT_CAPACITY 大小为10；
* 当使用指定初始容量创建ArrayList 实例，调用 addAll 方法添加多个元素的时候，原容量的1.5倍也无法存放元素的时候，会创建一个更大（不会超过 Integer.MAX_VALUE）的数组来存放元素

> ArrayList 的 add 操作如何优化？
* 扩容需要移动数据，非常影响性能。那么优化的重点就是尽量避免 ArrayList 内部进行内部扩容。对于add 操作，如果添加的元素个数已知，最好使用指定初始容量的构造方法创建 ArrayList 实例或者在添加元素之前执行`ensureCapacity`方法确保有足够的容量来存放add操作的元素


> ArrayList 的构造方法
1. 无参构造方法 ： Java8 中使用了延迟初始化，使用无参构造方法，并不会马上创建长度为 10 的数组，而是在调用 add 方法添加第一个元素的时候才对 elementData 数组进行初始化
2. 指定初始容量的构造方法: 
  *  传入初始容量 initialCapacity，如果初始容量大于 0，那么直接创建一个指定大小的 Object 数组；
  *  如果初始容量等于0，elementData 指向共享的空数组实例 EMPTY_ELEMENTDATA
  *  如果初始容量小于0，抛出 IllegalArgumentException 异常


> ArrayList 源码中为何定义两个 Object 数组呢？EMPTY_ELEMENTDATA 和 DEFAULTCAPACITY_EMPTY_ELEMENTDATA 各有什么用处
这两个常量都是空Object数组的引用，都表示ArrayList实例的空状态，即elementData数组中没有元素。
* `EMPTY_ELEMENTDATA`使用指定初始容量的构造方法`ArrayList(int initialCapacity)`和指定初始集合的构造方法`ArrayList(Collection<? extends E> c)`时使用。
* `DEFAULTCAPACITY_EMPTY_ELEMENTDATA` 是使用无参构造方法时使用的。

> ArrayList 中的size 和 capacity 怎么理解？

* size 用于记录 ArrayList 实例中 elementData 数组中元素的个数，
* capacity 是elementData 数组的长度（包括已使用的数组空间和未使用的数组空间）

> 在索引中ArrayList的增加或者删除某个对象的运行过程？效率很低吗？解释一下为什么？
* 增加元素时，我们要把要增加位置及以后的所有元素都往后移一位，先腾出一个空间，然后再进行添加。
* 删除某个元素时，我们也要把删除位置以后的元素全部元素往前挪一位，通过覆盖的方式来删除。
* 如果遇到了需要频繁插入或者是删除的时候，你可以选择其他的Java集合，比如LinkedList

```
System. arraycopy方法实现数组的复制1-1：
System中提供了一个native静态方法arraycopy()，可以使用这个方法实现数组之间的复制。
对于普通的一维数组来说，会复制每个数组的值到另一个数组中，即每个元素都是按值传递，修改副本不会影响原来的值。
```

>  ArrayList 的Remove  方法
1. 获取指定位置 index 处的元素值
2. 将 index + 1 及之后的元素向前移动一位
3. 将最后一个元素置空，并将 size 值减 1
4. 返回被删除值，完成删除操作


> Linkedlist 扩容机制
* LinkedList是离散空间所以不需要主动扩容，而ArrayList是连续空间，内存空间不足时，会主动扩容
* .由于他的底层是用双向链表实现的，没有初始化大小，所以没有油扩容机制，就是一直在前面或者是后面新增就好


## HASHMAP

### 讲一下hashmap原理吧,HashMap的原理，特点，怎么使用？

数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的
1. 谈谈你理解的 HashMap，讲讲其中的 get put 过程。
put
* 获得key对象的hashcode
* 根据hashcode计算出hash值
* 生成Entry对象 key对象、value对象、hash值、指向下一个Entry对象的引用
* 将Entry对象放到table数组中

get
*  获得key的hashcode，
* 在链表上挨个比较key对象, 调用equals()方法，将key对象和链表上所有节点的key对象进行比较


2. 1.8 做了什么优化？ 当 Hash 冲突严重时，在桶上形成的链表会变的越来越长，这样在查询时的效率就会越来越低；时间复杂度为 O(N)。
 * HashMap在存储一个元素时，当对应链表长度大于8时，链表就转换为红黑树，这样又大大提高了查找的效率。

3. 是线程安全的嘛？
 * 简单总结下 HashMap：无论是 1.7 还是 1.8 其实都能看出 JDK 没有对它做任何的同步操作，所以并发会出问题

4. 不安全会导致哪些问题？
  * 多线程put后可能导致get死循环， put的时候transfer方法循环将旧数组中的链表移动到新数组
  * 多线程put的时候可能导致元素丢失

5. 如何解决？有没有线程安全的并发问题？ 
  * 使用Collections.synchronized(hashMap）
  *  直接使用ConcurrentHashMap。

### HashMap如何解决Hash冲突

Java中的HashMap采用的hash冲突解决方案就是单独链表法，也就是在hash表节点使用链表存储hash值相同的值

### hashmap源码：扩容具体实现；追问先扩容再插入元素还是先插入元素再扩容（答反了，应该是先插入元素，再判断是否需要进行扩容操作再进行扩容）如果插入式时候超过阈值，新的元素不还是照常插入linkedlist上吗

> HashMap扩容阈值

1.7
* Hashmap的扩容需要满足两个条件：当前数据存储的数量（即size()）大小必须大于等于阈值；当前加入的数据是否发生了hash冲突

1.8
* HashMap的扩容会调用resize()方法，扩容阈值为当前容量 * 负载因子，
* 如果默认情况下hashMap容量为16，负载因子是0.75，则元素个数大于12就会触发扩容，
* resize()先判断HashMap是不是初始化了，扩容的时候会创建一个新的数组，然后旧数组中的元素进行搬移
* transfer()方法将原有Entry数组的元素拷贝到新的Entry数组里

1. 判断初始化，resize()方法进行首次扩容
2. 根据key的hash值进行哈希算法找tab数组的索引，如果为空直接new个node节点插入
3. 如果不为空，先判断hash是否一样，在判断equals键是否一样。一样直接替换掉value；
4. 如果不为空，判断该节点是否为树节点，是，插入红黑树
5. 都不符合就是为链表，遍历插入链表尾部
6. 判断链表的长度是否到达阈值(8)，超过阈值变为红黑树
    *  在转换结构时，若tab的长度小于MIN_TREEIFY_CAPACITY，默认值为64，则会将数组长度扩大到原来的两倍，并触发transfer，重新调整节点位置
    *  当长度降到 6 就转换回去
8. 检查是否需要二次扩容





### 为什么并且原链表中的Entry结点并不一定仍然在新桶数组的同一链表
如果数组进行扩容，数组长度发生变化，而存储位置 index = h&(length-1),index也可能会发生变化，需要重新计算index

### HashMap的容量必须是2的幂，那么为什么要这么设计呢
当然是为了性能。
* 在HashMap通过键的哈希值进行定位桶位置的时候，调用了一个`indexFor(hash, table.length)`;进行了一个与操作得出了对应的桶的位置

### 你的hashmap里可以放long类型

可以，但一定要用包装类

```
Map<Object,String>  map = new HashMap<>();

Integer b = 123;

Long c =123L;

map.put(b, b+"int");

System.out.println(b.hashCode() == c.hashCode());  //true

System.out.println(map.get(c));   //null

map.put(c, c+"long");  //  size =2
```

* hashmap在存入的时候，先对key 做一遍 hash，以hash值作为数组下标，如果发现下标已有值，判断存的key跟传入的key是不是相同
* Integer 和 Long 肯定不是一个类型，返回 false，这时候 hashmap 以 hashkey 冲突来处理，存入链表，所以 Long 123 和 Integer 123 hashmap会认为是 hash冲突
* hashmap 在 get的时候，也是先做 hash处理，根据hash值查找对应的数组下标，如果找到，使用存入时候的判断条件,，所以 存入Integer 123  根据 Long 123 来获取返回的 是 NULL


## hashmap线程安全吗

假设两个线程A、B都在进行put操作，并且hash函数计算出的插入下标是相同的，
* 当线程A执行完第六行代码后由于时间片耗尽导致被挂起，
* 而线程B得到时间片后在该下标处插入了元素，完成了正常的插入，然后线程A获得时间片，
* 由于之前已经进行了hash碰撞的判断，所有此时不会再进行判断，而是直接进行插入，这就导致了线程B插入的数据被线程A覆盖了，从而线程不安全

## HashMap在并发情况下为什么造成死循环？
因为扩容的时候，链表用的头插法。

## 为什么重写了equals方法就必须重写hashCode方法呢？

如果只重写equals方法而不重写hashCode方法的话，即使这两个对象通过equals方法判断是相等的，但是因为没有重写hashCode方法，他们的hashCode是不一样的，这样就会被散列到不同的位置去，变成错误的结果了。所以hashCode和equals方法必须一起重写。 


## HashMap,HashTable和ConCurrentHashMap的区别？ConCurrentHashMap更加细粒度的锁怎么实现的？

Hashtable ： Hashtable 和 HashMap的实现原理几乎一样，差别无非是
*  Hashtable不允许key和value为null；
* Hashtable是线程安全的。

但是 Hashtable 线程安全的策略实现代价却太大了，简单粗暴，get/put 所有相关操作都是 synchronized 的，这相当于给整个哈希表加了一把大锁，多线程访问时候，只要有一个线程访问或操作该对象，那其他线程只能阻塞，相当于将所有的操作串行化，在竞争激烈的并发场景中性能就会非常差

ConcurrentHashMap 所采用的 "分段锁" 思想，而如果容器中有多把锁，每一把锁锁一段数据，这样在多线程访问时不同段的数据时，就不会存在锁竞争了
---
## TreeMap
> 实现了SotredMap接口，它是有序的集合。而且是一个红黑树结构，每个key-value都作为一个红黑树的节点
```
private final Comparator<? super K> comparator;  //比较器，是自然排序，还是定制排序 ，使用final修饰，表明一旦赋值便不允许改变
private transient Entry<K,V> root = null;  //红黑树的根节点
private transient int size = 0;     //TreeMap中存放的键值对的数量
private transient int modCount = 0;   //修改的次数
```
TreeMap提供了四个构造方法，实现了方法的重载。无参构造方法中比较器的值为null,采用自然排序的方法，如果指定了比较器则称之为定制排序.
```
自然排序：TreeMap的所有key必须实现Comparable接口，所有的key都是同一个类的对象
定制排序：创建TreeMap对象传入了一个Comparator对象，该对象负责对TreeMap中所有的key进行排序，采用定制排序不要求Map的key实现Comparable接口。等下面分析到比较方法的时候在分析这两种比较有何不同。
```

```
红黑树是一个更高效的检索二叉树，有如下特点：
每个节点只能是红色或者黑色
根节点永远是黑色的
所有的叶子的子节点都是空节点，并且都是黑色的
每个红色节点的两个子节点都是黑色的（不会有两个连续的红色节点）
从任一个节点到其子树中每个叶子节点的路径都包含相同数量的黑色节点（叶子节点到根节点的黑色节点数量每条路径都相同）
任意插入红黑树的节点必须是红色的
```

---
# ConcurrentHashMap

## ConcurrentHashMap 是如何实现的 1.7 
ConcurrentHashMap 采用了分段锁技术
* Segment数组的意义就是将一个大的table分割成多个小的table来进行加锁
* 每一个Segment元素存储的是HashEntry数组+链表
* Segment 继承于 ReentrantLock。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment

通过initialCapacity、loadFactor、concurrencyLevel 几个参数来初始化 segments 数组的长度ssize、段偏移量 segmentShift、段掩码 segmentMask ，每个 segment 里的 HashEntry 数组的长度 
```
concurrencyLevel是允许访问的最大并发数
ssize是segments数组的长度，即整个ConcurrentHashMap中锁的个数
modCount remove和clean方法里操作元素前都会将变量modCount进行加1，那么在统计size前后比较modCount是否发生变化，从而得知容器的大小是否发生变化
```
**PUT**
> 对于ConcurrentHashMap的数据插入，这里要进行两次Hash去定位数据的存储位置
1. 会进行第一次key的hash来定位Segment的位置,如果该Segment还没有初始化，即通过CAS操作进行赋值
2. 对整个segment加锁，lock()。
3. 先通过c > threshold判断是否需要进行扩容，如果需要，调用rehash()方法进行扩容
4. 进行第二次hash操作，找到相应的HashEntry的位置,找到所在链表的表头
5. 遍历链表，直到找到相同的key，或者遍历到链表末尾
6. 如果存在相同的key，则更新value；否则，将键值对通过头插法插入到链表中
7. 在finally语句块中，对整个segment进行解锁，unlock()。

**get** 
> 逻辑比较简单：get方法无需加锁，
1. 只需要将 Key 通过 Hash 之后定位到具体的 Segment ，再通过一次 Hash 定位到具体的元素上。
2. 由于 HashEntry中的value属性是用 volatile关键词修饰的，保证了内存可见性，所以每次获取时都是最新值。
3. ConcurrentHashMap 的 get 方法是非常高效的，因为整个过程都不需要加锁，除非读到的值是空的才会加锁重读

> 為什麼GET特別高效
* 将get操作中的共享变量count和value定义成volatile，使得它能够在线程之间保持可见性，能够多线程同时读，并且保证不会读到过期的值，但是只能单线程写
   *  定义成volatitle的变量既然能多线程同时读，单线程写，如果保证不会读到过期值？
        *  java 内存模型的 happens before 原则，对 volatile 字段的写入操作先于读操作，即使两个线程同时修改和获取 volatile 字段，get 操作也能拿到最新的值 
* get操作只需要读不需要写共享变量count和value，因此不需要加锁

**SIZE**
> 在你计算size的时候，他还在并发的插入数据，可能会导致你计算出来的size和你实际的size有相差
1. 他会使用不加锁的模式去尝试多次计算ConcurrentHashMap的size，最多三次，比较前后两次计算的结果，结果一致就认为当前没有元素加入，计算的结果是准确的 
   1. 每个 segment 维护了一个 count 变量，用于统计该 segment 中的键值对个数,
   2. 先通过两次不加锁的方式，对segment中的count变量进行累加
   3. 实际上，直接把所有segment的count相加得到的ConcurrentHashMap大小并不准确(两次操作结果不一致，是通过 modCount 变量进行判断的)
2. 如果第一种方案不符合，他就会给用for循环每个Segment加上锁，然后for循环计算ConcurrentHashMap的size返回


#### 扩容
> segment的扩容与HashEntryMap相比：
* 扩容的时间不同： HashMap是先将键值对插入后，再判断是否需要进行扩容；而ConcurrentHashMap的segment是先判断是否需要扩容，再插入键值对。HashMap很可能在扩容之后没有键值对再插入，这时就进行了一次无效的扩容。
* 扩容倍数相同： segment的扩容也是将HashEntry数组容量扩大两倍。
* 扩容的范围不同： 为了高效， ConcurrentHashMap 不会对整个容器进行扩容，而只对某个 segment 进行扩容。
----
# Set
## HashSet

HashSet底层完全就是在HashMap的基础上包了一层，只不过存储的时候value是默认存储了一个Object的静态常量，取的时候也是只返回key，所以看起来就像List一样。

```
private static final Object PRESENT = new Object();
public HashSet() {
        map = new HashMap<>();
    }

public Iterator<E> iterator() {
    return map.keySet().iterator();
}

public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
      
```
## TreeSet
TreeSet实现了SortedSet接口，它是一个有序的集合类
* TreeSet的底层是通过TreeMap实现的。
* TreeSet并不是根据插入的顺序来排序，而是根据实际的值的大小来排序。
* TreeSet也支持两种排序方式
    * 自然排序
    * 自定义排序
----
##  ConcurrentHashMap在JDK1.8中的
> 直接用Node数组+链表+红黑树的数据结构来实现，并发控制使用`Synchronized`和`CAS`来操作


**Node**
> Node是ConcurrentHashMap存储结构的基本单元，继承于HashMap中的Entry
* 就是一个链表，
* 但是只允许对数据进行查找，不允许进行修改


**TreeNode**
* TreeNode继承与Node，但是数据结构换成了二叉树结构，
* 它是红黑树的数据的存储结构，用于红黑树中存储数据，
* 当链表的节点数大于8时会转换成红黑树的结构，他就是通过TreeNode作为存储结构代替Node来转换成黑红树

**TreeBin**
* TreeBin从字面含义中可以理解为存储树形结构的容器
* 而树形结构就是指TreeNode，所以TreeBin就是封装TreeNode的容器
* 它提供转换黑红树的一些条件和锁的控制

```
 @SuppressWarnings("unchecked")
static final <K,V> Node<K,V> tabAt(Node<K,V>[] tab, int i) { // //获得在i位置上的node结点
    return (Node<K,V>)U.getObjectVolatile(tab, ((long)i << ASHIFT) + ABASE);
}

static final <K,V> boolean casTabAt(Node<K,V>[] tab, int i,
                                    Node<K,V> c, Node<K,V> v) { // 利用CAS算法设置i位置上的Node节点。之所以能实现并发是因为他指定了原来这个节点的值是多少
    return U.compareAndSwapObject(tab, ((long)i << ASHIFT) + ABASE, c, v);
}

static final <K,V> void setTabAt(Node<K,V>[] tab, int i, Node<K,V> v) {
    U.putObjectVolatile(tab, ((long)i << ASHIFT) + ABASE, v);
}
```


### PUT
1. 如果没有初始化就先调用initTable()方法来进行初始化过程
2. 计算当前桶位是否有值
    * 无，则CAS添加,失败后继续自旋,直到成功，结束自旋
    * 继续
3. 判断数组元素是否为转移节点（ForwardingNode）
    * 是，说明正在扩容，则帮助扩容,之后再新增
    * 无，继续
4. 桶位有值,对当前桶位加synchronize锁,判断是链表还是红黑树
    * 链表，新增节点到链尾
    * 红黑树，红黑树版方法新增
5. 如果添加成功就调用`addCount()`方法统计size，并且检查是否需要扩容

他在并发处理中使用的是乐观锁，当有冲突的时候才进行并发处理

### GET

1.计算hash值，定位到该table索引位置，如果是首节点符合就返回
2. 如果遇到扩容的时候，会调用标志正在扩容节点ForwardingNode的find方法，查找该节点，匹配就返回
3. 以上都不符合的话，就往下遍历节点，匹配就返回，否则最后就返回null


> JDK1.8为什么使用内置锁synchronized来代替重入锁ReentrantLock
* 因为粒度降低了 -> 低粒度加锁,synchronized并不比ReentrantLock差，在粗粒度加锁中ReentrantLock可能通过Condition来控制各个低粒度的边界，更加的灵活，而在低粒度中,Condition的优势就没有了
* JVM的开发团队从来都没有放弃synchronized，而且基于JVM的synchronized优化空间更大，使用内嵌的关键字比使用API更加自然


### 扩容（transfer）
在put方法最后检查是否需要扩容
* 从put方法的`addCount`方法进入`transfer`，
* 主要就是新建新的空数组，然后移动拷贝每个元素到新数组.在扩容过程中，依然支持并发更新操作；也支持并发插入。
* ConcurrentHashMap扩容的时候，如果有其他线程进行put操作，会帮助一起扩容 一个线程负责（按从后往前的顺序）一个stride部分，将数据迁移到新的table中 

> 什么时候会触发扩容？
新增节点后，addCount统计tab中的节点个数大于阈值（sizeCtl），会触发扩容。
* 如果新增节点之后，所在的链表的元素个数大于等于8，则会调用treeifyBin把链表转换为红黑树。
* 在转换结构时，若tab的长度小于MIN_TREEIFY_CAPACITY，默认值为64，则会将数组长度扩大到原来的两倍，并触发transfer，重新调整节点位置
* initTable中将数组初始化为16；若传了大小，则先经tableSizeFor改变大小确保为2的n次幂，之后赋给sizeCtl

整个扩容操作分为两个部分
1. 构建一个nextTable,它的容量是原来的两倍，这个操作是扩容的第一个线程完成的。会保证第一个发起数据迁移的线程，nextTab 参数为 null，之后再调用此方法的时候，nextTab 不会为 null。
2. 将原来table中的元素复制到nextTable中，这里允许多线程进行操作
   * 在数据转移的过程中会加 synchronized 锁，锁住头节点，同步化操作，防止 putVal 的时候向链表插入数据
   
```
/*控制标识符，用来控制table的初始化和扩容的操作，不同的值有不同的含义
 *当为负数时：-1代表正在初始化，-N代表有N-1个线程正在 进行扩容
 *当为0时：代表当时的table还没有被初始化
 *当为正数时：表示初始化或者下一次进行扩容的大小
 */
private transient volatile int sizeCtl;

```

### Size
> ConcurrentHashMap提供了 baseCount、counterCells 两个辅助变量和一个 CounterCell 辅助内部类。sumCount() 就是迭代 counterCells 来统计 sum 的过程

JDK1.8 size 是通过对 baseCount 和 counterCell 进行 CAS 计算，最终通过 baseCount 和 遍历 CounterCell 数组得出 size

Map的size可能超过 MAX_VALUE所以还有一个方法`mappingCount()`JDK的建议使用`mappingCount()`而不是`size()`

* addCount 有一种思想，即当一个集合发生过并发时，其后续发生的并发可能性也越高，所以会先判断 CounterCells 是否为空，
    * 如果不为空，则后续不再考虑在 baseCount 上操作。这种思想应当应用在大部分场景中
*  使用一个volatile类型的变量baseCount记录元素的个数，当插入新数据put()或则删除数据remove()时，会通过addCount()方法更新baseCount
*  初始化时counterCells为空，在并发量很高时，如果存在两个线程同时执行CAS修改baseCount值，则失败的线程会继续执行方法体中的逻辑，执行fullAddCount(x, uncontended)方法，这个方法其实就是初始
*  如果并发导致 baseCount CAS 失败了使用 counterCells
* counterCells这个数组，实际上size和table一致，这样Counter中的value就是这个数组中index对应到table中bucket的长度
* JDK1.8 size是通过对`baseCount`和`counterCell`进行 CAS 计算，最终通过`baseCount`和遍历`CounterCell`数组得出 size
* 所以counterCells存储的都是value为1的CounterCell对象，而这些对象是因为在CAS更新baseCounter值时，由于高并发而导致失败，最终将值保存到CounterCell中，放到counterCells里。这也就是为什么sumCount()中需要遍历counterCells数组，sum累加CounterCell.value值了




---
## 那你什么场景用concurrenthashmap 什么场景用hashmap
* ConcurrentHashMap推荐应用场景 多线程对HashMap数据添加删除操作时，可以采用ConcurrentHashMap。
* 多个线程的共享资源，虽然在操作中有可能增加和删除数据，但是主要目的是共享
