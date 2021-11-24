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

当map中包含的Entry的数量大于等于threshold = loadFactor * capacity的时候，且新建的Entry刚好落在一个非空的桶上，此刻触发扩容机制，将其容量扩大为2倍。
* 当size大于等于threshold的时候，并不一定会触发扩容机制，但是会很可能就触发扩容机制，只要有一个新建的Entry出现哈希冲突，则立刻resize。
* 旧桶数组中的某个桶的外挂单链表是通过头插法插入新桶数组中的，并且原链表中的Entry结点并不一定仍然在新桶数组的同一链表


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

## HashMap在并发情况下为什么造成死循环？一脸懵

## 为什么重写了equals方法就必须重写hashCode方法呢？

如果只重写equals方法而不重写hashCode方法的话，即使这两个对象通过equals方法判断是相等的，但是因为没有重写hashCode方法，他们的hashCode是不一样的，这样就会被散列到不同的位置去，变成错误的结果了。所以hashCode和equals方法必须一起重写。 


## HashMap,HashTable和ConCurrentHashMap的区别？ConCurrentHashMap更加细粒度的锁怎么实现的？

Hashtable ： Hashtable 和 HashMap的实现原理几乎一样，差别无非是：（1）Hashtable不允许key和value为null；（2）Hashtable是线程安全的。

但是 Hashtable 线程安全的策略实现代价却太大了，简单粗暴，get/put 所有相关操作都是 synchronized 的，这相当于给整个哈希表加了一把大锁，多线程访问时候，只要有一个线程访问或操作该对象，那其他线程只能阻塞，相当于将所有的操作串行化，在竞争激烈的并发场景中性能就会非常差

ConcurrentHashMap 所采用的 "分段锁" 思想，而如果容器中有多把锁，每一把锁锁一段数据，这样在多线程访问时不同段的数据时，就不会存在锁竞争了

---
# ConcurrentHashMap

## ConcurrentHashMap 是如何实现的 1.7 
ConcurrentHashMap 采用了分段锁技术，ConcurrentHashMap的主干是个Segment数组, 其中 Segment 继承于 ReentrantLock。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment

ConcurrentHashMap初始化方法是通过initialCapacity，loadFactor, concurrencyLevel几个参数来初始化segments数组，
**PUT**
1. 定位segment并确保定位的Segment已初始化 
2. 对整个segment加锁
3. 先通过c > threshold判断是否需要进行扩容，如果需要，调用rehash()方法进行扩容
4. 定位键值对在HashEntry数组中的位置，找到所在链表的表头
5. 遍历链表，直到找到相同的key，或者遍历到链表末尾
6. 如果存在相同的key，则更新value；否则，将键值对通过头插法插入到链表中。
7. 在finally语句块中，对整个segment进行解锁，unlock()。

**get** 逻辑比较简单：get方法无需加锁，
1. 只需要将 Key 通过 Hash 之后定位到具体的 Segment ，再通过一次 Hash 定位到具体的元素上。
2. 由于 HashEntry 中的 value 属性是用 volatile 关键词修饰的，保证了内存可见性，所以每次获取时都是最新值。
3. ConcurrentHashMap 的 get 方法是非常高效的，因为整个过程都不需要加锁，除非读到的值是空的才会加锁重读

##  ConcurrentHashMap在JDK1.8中的变化
JDK 1.8 使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized
* JDK 1.8 中，没有使用segment，而且HashEntry数组与JDK1.8中一致，采用数组 + 链表 + 红黑树的方式。


## ConcurrentHashMap扩容
HashMap是先将键值对插入后，再判断是否需要进行扩容；而ConcurrentHashMap的segment是先判断是否需要扩容，再插入键值对。HashMap很可能在扩容之后没有键值对再插入，这时就进行了一次无效的扩容。
* 扩容的时候首先会创建一个两倍于原容量的数组
* 然后将原数组里的元素进行再hash后插入到新的数组里
* 为了高效ConcurrentHashMap不会对整个容器进行扩容，而只对某个segment进行扩容

## SIZE
两次不加锁的方式进行统计，如果统计结果不一致，使用加锁的方式重新统计。
1. 在执行 size 操作时，需要遍历所有 Segment 然后把 count 累计起来。
2. ConcurrentHashMap 在执行 size 操作时先尝试不加锁，
3. 如果连续两次不加锁操作得到的结果一致，那么可以认为这个结果是正确的。


## 那你什么场景用concurrenthashmap 什么场景用hashmap
* ConcurrentHashMap推荐应用场景 多线程对HashMap数据添加删除操作时，可以采用ConcurrentHashMap。
* 多个线程的共享资源，虽然在操作中有可能增加和删除数据，但是主要目的是共享
