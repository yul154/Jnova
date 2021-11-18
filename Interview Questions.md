
> java中==和equals和hashCode的区别，重写euqals需要重写hash吗

`==`
1.若是基本数据类型比较，是比较值，
2.若是引用类型，则比较的是他们在内存中存放的地址。
对象是放在堆中，栈中存放的对象的引用，所以==是对栈中的值进行比较，若返回true，代表变量的内存地址相等

* equals() 定义在JDK的Object.java中。Object类的equals方法用于判断对象的内存地址引用是不是同一个地址。若是 类中覆盖了equals方法，就要根据具体代码来确定
  * 使用默认的“equals()”方法，等价于“==”方法
  * 若是类中覆盖了equals方法，就要根据具体代码来确定，一般覆盖后都是通对象的内容是否相等来判断对象是否相等
* hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。
  * 因为Hash比equals方法的开销要小，速度更快，所以在涉及到hashcode的容器中（比如HashSet），判断自己是否持有该对象时，会先检查hashCode是否相等，如果hashCode不相等，就会直接认为不相等，并存入容器中，不会再调用equals进行比较

* 如果你重写了equals，比如说是基于对象的内容实现的，而保留hashCode的实现不变，那么很可能某两个对象明明是“相等”，而hashCode却不一样


> int、char、long各占多少字节数
> int与integer的区别
> 对java多态的理解
> 抽象类和接口区别
> 抽象类与接口的应用场景
> 抽象类是否可以没有方法和属性？
> 泛型中extends和super的区别
> 静态属性和静态方法是否可以被继承？是否可以被重写？以及原因？

不能，父类的静态方法能够被子类继承，但是不能够被子类重写，即使子类中的静态方法与父类中的静态方法完全一样，也是两个完全不同的方法
* 实际上，子类的静态方法隐藏了父类的静态方法，因此父类的子类的静态方法同时存在，只不过父类通过类名（或对象名）调用的是父类的静态方法，子类通过类名（或对象名）调用的是子类的静态方法

> 静态内部类的设计意图

> string 转换成 integer的方式及原理
* `integer.parseInt(string str)`方法调用Integer内部的`parseInt(string str,10)`方法,默认基数为10，
* parseInt内部首先判断字符串是否包含符号（-或者+），则对相应的negative和limit进行
* 赋值，然后再循环字符串，对单个char进行数值计算Character.digit(char ch, int radix)
* 在这个方法中，函数肯定进入到0-9字符的判断（相对于string转换到int），
* 否则会抛出异常，数字就是如上面进行拼接然后生成的int类型数值


> 说说你对Java反射的理解

# Collections
> List的线程安全类
* java.util.Collections.SynchronizedList： 把所有 List 接口的实现类转换成线程安全的List 
* CopyOnWriteArrayList
  * 在高并发情况下，读取元素时就不用加锁，写数据时才加锁，大大提升了读取性能 

> 
## HashMap 
> HashMap 原理

先从HashMap的结构开始吧，
* HashMap是一种散列表，以Key/Value形式存储，Key和Value都可以为空，在JDK 1.7时是由数组+链表组成，JDK 1.8则由数组+链表+红黑树组成，
* 这里主要介绍下JDK 1.8版本的HashMap，初始默认的容量是16，负载因子是0.75，当链表上的元素大于8 且数组容量大于64时链表进行红黑树化，当红黑树上元素减少到6时，红黑树会退化为链表。
* 数组的查询效率是O(1),链表的查询效率是O(N)，红黑树的查询效率是O(logN)。
HashMap的容量必须是2的次幂，
* 在构造方法中传入的容量如果不是2的次幂，那么HashMap会调用tableSizeFor()方法来获取一个最接近传入容量且大于传入容量的2的次幂的值，比如传入的容量是17则tableSizeFor()会返回32
* HashMap空参数的构造方法创建出来的HashMap对象是不会初始化空间的，当使用空参构造方法创建出对象后，HashMap将在第一次插入元素时进行空间的初始化

HashMap主要有2个重要方法，put()/get()，put()方法就是将元素无序的加入到HashMap中，也就是无法保证元素的插入顺序，get方法就是获取HashMap中的元素
* 当元素进行put时，首先要计算key的Hash值，为了更加分散，获取hash值后，HashMap会让hash值的高16位与hash进行异或
* 

> 如果查询多，插入少，怎么改进HashMap
> HashMap源码理解
> HashMap怎么手写实现？

>HashMap 扩容
* HashMap的扩容会调用resize()方法，扩容阈值为当前容量 * 负载因子，
* 如果默认情况下hashMap容量为16，负载因子是0.75，则元素个数大于12就会触发扩容，
* resize()先判断HashMap是不是初始化了，扩容的时候会创建一个新的数组，然后旧数组中的元素进行搬移，JDK1.7 的HashMap在并发场景下扩容有可能会造成死循环，cpu飙升100%

> ConcurrentHashMap的实现原理
在jdk1.7中是采用Segment + HashEntry + ReentrantLock的方式进行实现的，
 而1.8中放弃了Segment臃肿的设计，取而代之的是采用Node + CAS + Synchronized来保证并发安全进行实现。

ConcurrentHashMap 采用了分段锁技术，其中 Segment 继承于 ReentrantLock。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment
* 通过 key 定位到 Segmen，之后在对应的 Segment 中进行具体的 put。
* 虽然 HashEntry 中的 value 是用 volatile 关键词修饰的，但是并不能保证并发的原子性，所以 put 操作时仍然需要加锁处理。
* 首先第一步的时候会尝试获取锁，如果获取失败肯定就有其他线程存在竞争，则利用 scanAndLockForPut() 自旋获取锁。
* 将当前 Segment 中的 table 通过 key 的 hashcode 定位到 HashEntry。
* 遍历该 HashEntry，如果不为空则判断传入的 key 和当前遍历的 key 是否相等，相等则覆盖旧的 value。
* 为空则需要新建一个 HashEntry 并加入到 Segment 中，同时会先判断是否需要扩容。

> TreeMap具体实现
* TreeMap是一个有序的key-value集合，是非线程安全的，基于红黑树（Red-Black tree）实现。
* 其映射根据键的自然顺序进行排序，或者根据创建映射时提供的Comparator 进行排序，具体取决于使用的构造方法
> HashMap与HashSet的区别
它们都必须计算hashcode，但请考虑HashMap键的性质--它通常是一个简单的String，甚至是一个Integer。计算它的hashcode要比整个对象的默认hashcode计算快得多。如果HashMap的键与存储在HashSet中的键是同一个对象，那么在性能上就不会有真正的差别。区别在于HashMap的键是什么类型的对象

>HashSet与HashMap怎么判断集合元素重复？
* HashMap中的Key是根据对象的hashCode() 和 euqals()来判断是否唯一的。



# Java 8
> Stream实现原理
