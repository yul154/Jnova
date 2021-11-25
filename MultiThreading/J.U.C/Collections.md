
# 线程安全的集合
* 大部分在java.util包下的实现类都没有保证线程安全为了保证性能的优越，除了Vector和Hashtable以外
* 通过`Collections`可以创建线程安全类，但是他们的性能都比较差。
* 同步集合既保证线程安全也在给予不同的算法上保证了性能，他们都在java.util.concurrent包中。

## `Collections.synchronizedList(List list)`

这个方法根据传入的 List 返回一个支持同步（`sychronize`）的 List。接下来就可以利用这个返回的 List 进行串行访问了。
* SynchronizedList对部分操作加上了synchronized关键字以保证线程安全。但其iterator()操作还不是线程安全的。
*   可能发生错误当遍历返回列表时(`Iterator`)，必须手动对其进行同步

*` synchronizedList`用于`ArrayList`
* `synchronizedMap`用于`HashMap`
* `synchronizedSet`用于`HashSet`

```
public E get(int index) {

   synchronized(mutex) {return list.get(index);} //方法内部通过一个 mutex 对象锁的代码块保证线程安全

}
```

> Vector 和 SychronizeList 区别
* Vector是List接口的一个实现类，SychronizedList是Collections的静态内部类
* Vector使用同步方法实现线程安全，而SynchronizedList使用代码块实现
* 扩容机制：Vector的扩容机制是固定的，而SynchronizedList可以根据出传入List的不同，适配不同List的扩容规则
* SynchronizedList中的`listIterator`和`listIterator(int index)`并没有做同步处理，而Vector却对它们上了锁，也就是说在使用SynchronizedList进行遍历的时候要手动加锁
* 将继承了List类的子类转换成SynchronizedList,是不需要改变它的底层数据结构,可以直接使用它本身的方法.而Vector底层结构就是使用数组实现的，所以Vector不能像SynchronizedList那样适配其他List类
```
同步方法直接在方法上加synchronized实现加锁，同步代码块则在方法内部加锁，但一般锁的范围大小和性能是成反比的。
当一个线程访问object的一个synchronized(this)同步代码块时，另一个线程仍然可以访问该object中的非synchronized(this)同步代码块
同步块可以更加精准地控制锁的作用域，而同步方法的锁的作用域是整个方法。
同步代码块可以选择对哪个对象加锁，但是同步方法只能给this对象加锁。
```

---
## JUC

### Blocking Queue
阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。这两个附加的操作支持阻塞的插入和移除方法。

* 支持阻塞的插入方法：意思是当队列满时，队列会阻塞插入元素的线程，直到队列不满。
* 支持阻塞的移除方法：意思是在队列为空时，获取元素的线程会等待队列变为非空。

阻塞队列常用于生产者和消费者的场景，生产者是向队列里添加元素的线程，消费者是从队列里取元素的线程。阻塞队列就是生产者用来存放元素、消费者用来获取元素的容器。



|Collection|Describtion|
|----|----|
|`ArrayBlockingQueue<E>`|A bounded blocking queue backed by an array.|
|`LinkedBlockingDeque<E>`|An optionally-bounded blocking deque based on linked nodes.|
|`LinkedBlockingQueue<E>`	|An optionally-bounded blocking queue based on linked nodes.|
|`PriorityBlockingQueue<E>`|An unbounded blocking queue that uses the same ordering rules as class PriorityQueue and supplies blocking retrieval operations.|
|`SynchronousQueue<E>	`|A blocking queue in which each insert operation must wait for a corresponding remove operation by another thread, and vice versa.|
|`DelayQueue<E extends Delayed>`|An unbounded blocking queue of Delayed elements, in which an element can only be taken when its delay has expired.|


### Concurrency series

|Collection|describtion|
|----------|-----------|
|`ConcurrentHashMap<K,V>`	|A hash table supporting full concurrency of retrievals and high expected concurrency for updates.|
|`ConcurrentHashMap.KeySetView<K,V>`|A view of a ConcurrentHashMap as a Set of keys, in which additions may optionally be enabled by mapping to a common value.|
|`ConcurrentLinkedDeque<E>`|An unbounded concurrent deque based on linked nodes.|
|`ConcurrentLinkedQueue<E>`|An unbounded thread-safe queue based on linked nodes.|
|`ConcurrentSkipListMap<K,V>`|A scalable concurrent ConcurrentNavigableMap implementation.|
|`ConcurrentSkipListSet<E>`|A scalable concurrent NavigableSet implementation based on a ConcurrentSkipListMap.|


### CopyOnWrite
|Collection|Describtion|
|---|---|
|`CopyOnWriteArrayList<E>`|A thread-safe variant of ArrayList in which all mutative operations are implemented by making a fresh copy of the underlying array.|
|`CopyOnWriteArraySet<E>`|	A Set that uses an internal CopyOnWriteArrayList for all of its operations.|

当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，
*  当对象进行写操作时，使用了Lock锁做同步处理，内部拷贝了原数组，并在新数组上进行添加操作，最后将新数组替换掉旧数组；若进行的读操作，则直接返回结果，
*  添加完元素之后，再将原容器的引用指向新的容器

```
private transient volatile Object[] array;

final Object[] getArray() {
    return array;
}

public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        ...
        return true;
    } finally {
        lock.unlock();
    }
}

public E get(int index) {
    return get(getArray(), index);
}
```

特点
* 是一种读写分离的并发策略
  * 为了将读取的性能发挥到极致，读取是完全不用加锁的，并且更厉害的是：写入也不会阻塞读取操作。只有写入和写入之间需要进行同步等待

优点
* Java的List在遍历时，别的线程对List容器进行修改，则会抛出`ConcurrentModificationException`异常。
  * 而CopyOnWriteArrayList由于其"读写分离"的思想，遍历和修改操作分别作用在不同的List容器，所以在使用迭代器进行遍历时候，也就不会抛出ConcurrentModificationException异常了

CopyOnWrite的缺点
* 内存占用问题: 所以在进行写操作的时候，内存里会同时驻扎两个对象的内存
* 数据一致性问题:CopyOnWrite 容器只能保证数据的最终一致性，不能保证数据的实时一致性。所以如果你希望写入的的数据，马上能读到，请不要使用 CopyOnWrite 容器
  * 读的时候不需要加锁，如果读的时候有多个线程正在向CopyOnWriteArrayList添加数据，读还是会读到旧的数据，因为开始读的那一刻已经确定了读的对象是旧对象 

使用场景
* 我们系统中处理的都是读多写少的并发场景

> CopyOnWrite VS Vector
* `get()`,Vector 使用了同步关键字，所有的 get() 操作都必须先取得对象锁才能进行。反观CopyOnWriteArrayList 的get() 实现，并没有任何的锁操作
* `add()`,`CopyOnWriteArrayList`的写操作性能不如Vector，原因也在于`Copy-On-Write`，写入时不止加锁，还使用了`Arrays.copyOf()`进行了数组复制,遇到大对象也会导致内存占用较大
* 在读多写少的高并发环境中，使用`CopyOnWriteArrayList`可以提高系统的性能，但是，在写多读少的场合，`CopyOnWriteArrayList`的性能可能不如 Vector



----
## Vector

Vector和List大同小异,底层都是用数组实现，只是在它的大部分方法上添加了`synchronized`关键字，用来保证线程安全；

```
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}

]
```

> 和 ArrayList 和 Vector 一样，同样的类似关系的类还有 HashMap 和 HashTable，StringBuilder 和 StringBuffer，后者是前者线程安全版本的实现，只是加了个 Synchronized 关键字

