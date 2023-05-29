![image](https://github.com/yul154/Jnova/assets/27160394/80753a68-8306-4174-baba-a47b808ebd89)


# Collections
- List
- Map
  - HashMap
    - 基本原理
    - 并发问题
  - TreeMap

-----
# Map

- TreeMap、HashMap、HashTable的实现区别？
- 请说明TreeMap、HashMap、HashTable的实现区别
- TreeMap怎么按照自己想要的顺序排序
- HashMap的原理及应用
- 请描述HashMap的原理及应用
- HashMap 的源码看过吗？ 讲讲hashmap扩容的步骤
- HashMap和ConcurrentHashMap区别
- 如何决定使用 HashMap 还是 TreeMap？	- HashMap的原理及应用
- 阐述ConcurrentHashMap 源码+底层数据结构分析
- 如何一边迭代一边修改
- 使用迭代器的过程中修改原容器会怎样
- Java 1.8的hashmap和1.7的有什么区别？
- 详细讲讲Java 1.7和1.8的hashmap resize 区别。
- 为什么hashmap计算 hashcode 要计算 hashcode 后高位运算再 &(old capacity)



## HashMap

**具体实现**
- put
  1. 获得key对象的hashcode
  2. 根据hashcode计算出hash值
  3. 生成Entry对象 key对象、value对象、hash值、指向下一个Entry对象的引用
  4. 将Entry对象放到table数组中
- get
  1. 获得key的hashcode，
  2. 在链表上挨个比较key对象, 调用equals()方法，将key对象和链表上所有节点的key对象进行比较



**哈希冲突**

* 不同的 key 可能经过 hash 运算可能会得到相同的地址，但是一个数组单位上只能存放一个元素，采用链地址法以后，如果遇到相同的 hash 值的 key 的时候，我们可以将它放到作为数组元素的链表上
* 待我们去取元素的时候通过 hash 运算的结果找到这个链表，再在链表中找到与 key 相同的节点，就能找到 key 相应的值了


**1.8 做了什么优化？**

* 当 Hash 冲突严重时，在桶上形成的链表会变的越来越长，这样在查询时的效率就会越来越低；时间复杂度为 O(N)。
* HashMap在存储一个元素时，当对应链表长度大于8时，链表就转换为红黑树，这样又大大提高了查找的效率。


## 扩容

**扩容时机**

1.7 
* Hashmap的扩容需要满足两个条件：当前数据存储的数量（即size()）大小必须大于等于阈值；当前加入的数据是否发生了hash冲突
  * 当前数量大于 容量* 负载因子 并且数组下标的值不为空

1.8
* HashMap的扩容会调用resize()方法，扩容阈值为当前容量 * 负载因子，
* 如果默认情况下hashMap容量为16，负载因子是0.75，则元素个数大于12就会触发扩容，
* resize()先判断HashMap是不是初始化了，扩容的时候会创建一个新的数组，然后旧数组中的元素进行搬移
* transfer()方法将原有Entry数组的元素拷贝到新的Entry数组里


**扩容过程**
1. 判断初始化，resize()方法进行首次扩容
2. 根据key的hash值进行哈希算法找tab数组的索引，如果为空直接new个node节点插入
3. 如果不为空，先判断hash是否一样，在判断equals键是否一样。一样直接替换掉value；
4. 如果不为空，判断该节点是否为树节点，是，插入红黑树
5. 都不符合就是为链表，遍历插入链表尾部
6. 判断链表的长度是否到达阈值(8)，超过阈值变为红黑树
    *  在转换结构时，若tab的长度小于MIN_TREEIFY_CAPACITY，默认值为64，则会将数组长度扩大到原来的两倍，并触发transfer，重新调整节点位置
    *  当长度降到 6 就转换回去
8. 检查是否需要二次扩容


**先插入还是先扩容**
* 在JDK1.7的时候是先扩容后插入的(先扩容，然后使用头插法，直接把要插入的Entry插入到扩容后数组中，头插法不需要遍历扩容后的数组或者链表)，
* 但是在1.8的时候是先插入再扩容的，优点其实是因为为了减少这一次无效的扩容，原因就是JDK1.8采用尾插法，如果先扩容，扩容后需要遍历一遍，再找到尾部进行插入


**HashMap的容量必须是2的幂，那么为什么要这么设计呢**

在HashMap通过键的哈希值进行定位桶位置的时候，调用了一个`indexFor(hash, table.length)`;进行了一个与操作得出了对应的桶的位置
* 位运算的代价比求模运算小的多，因此在进行这种计算时用位运算的话能带来更高的性能，key 的 hash 值对桶个数取模：hash%capacity，如果能保证 capacity 为 2 的 n 次方，那么就可以将这个操作转换为位运算
* length 为偶数时，length-1 为奇数，奇数的二进制最后一位是 1，这样便保证了 hash &(length-1) 的最后一位可能为 0，也可能为 1（这取决于 h 的值），即 & 运算后的结果可能为偶数，也可能为奇数，这样便可以保证散列的均匀性



## 线程安全

* 简单总结下 HashMap：无论是 1.7 还是 1.8 其实都能看出 JDK 没有对它做任何的同步操作，所以并发会出问题
* 多线程put后可能导致get死循环， put的时候transfer方法循环将旧数组中的链表移动到新数组
* 多线程put的时候可能导致元素丢失

---
# ConcurrentHashMap



