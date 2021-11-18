> Java有几种实现线程的方法
* Runnable --> run() 实现Runnable接口
* Callable --> call() 实现Callable接口
* Thread --> new Thread() 继承Thread类，重写run方法

> 线程的生命周期

|Status|Transfer|
|-------|--------|
|`new`| 新创建的一个线程，处于等待状态,`start()`方法标识启动一个线程，并分配资源,让其状态变成`runnable`|
|`runnable`|可运行状态，并不是已经运行，具体的线程调度各操作系统决定. 当线程调用了 start() 方法后，线程则处于就绪 Ready 状态，等待操作系统分配 CPU 时间片，分配后则进入 Running 运行状态,`yield()` 让当前运行线程回到可运行状态，交出时间分片让高优先级的线程先执行，如果持有锁的话是不会释放掉的。
|`waiting`|可被唤醒的等待状态,此状态可以通过 synchronized 获得锁，调用 `wait()` `await` `join()` 方法进入等待状态。最后通过 `notify、notifyall` and `signal,signalAll`唤醒|
|`timeWaiting()`|指定时间内让出CPU资源，此时线程不会被执行，也不会被系统调度，直到等待时间到期后才会被执行,`Thread.sleep(long)`、`Object.wait(long)`、`Thread.join(long)`
|`blocked()`| 当发生锁竞争状态下，没有获得锁的线程会处于挂起状态。比如正在等待另一个线程的 synchronized 块的执行释|
|`terminated`| 从`New` 到`Terminated`是不可逆的。一般是程序流程正常结束或者发生了异常 |


> callable 的底层实现

为了让创建线程的方式可以有返回值抛出异常等功能，JDK1.5 引入Callable接口，
* 此时创建线程只能通过`new Thread()`方式，而且`Thread`只有传`Runnable`的构造器（请允许我这么说，虽然还能传ThreadGroup，但并不常用），
* 于是JDK1.5 引入`FutureTask`适配器类，
  * `RunnableFuture`接口相当于整合和`Future`和`Runnable`接口,间接继承`Runnable`接口（继承RunnableFuture接口，RunnableFuture继承Runnable接口),
  * `Future` 接口用来处理线程执行时的状态，可以取消，可以获取执行的返回值
    * `get`获取返回值结果的方法,如果线程没有执行完任务，调用该方法会阻塞当前线程,以及取消执行任务`cancel`,查看任务是否执行完毕`isDone`,以及任务是否取消`isCancelled`。
  * `FutureTask`接口是一个生产者消费者模式,生产者运行run方法计算结果，消费者经过get方法获取结果
  * `FutureTask` 的`run`方法执行了`Callable`的`call`方法，然后将返回值赋值给了成员变量,将该返回值传入它的`get`方法
  * `FutureTask`中有两个很关键的属性，一个是callable接口，一个是存放call方法的返回值outcome属性
  * 如何保证在call方法能比get方法先执行完成返回值成功呢: 它是在Call方法中加了一个锁,然后在get方法中判断该锁是否已经被释放,如果被释放那就说明已经可以获取到该返回值

> callable 怎么实现异步的

> 线程间的通信

* `join`:在线程中调用另一个线程的 join() 方法，会将当前线程挂起，而不是忙等待，直到目标线程结束。
* `wait() notify() notifyAll()`:调用 wait()使得线程等待某个条件满足,线程在等待时会被挂起,当其他线程的运行使得这个条件满足时,其它线程会调用 notify() 或者 notifyAll() 来唤醒挂起的线程。
* `await() signal() signalAll()`:J.U.C类库中提供了 Condition 类来实现线程之间的协调，可以在 Condition 上调用 await() 方法使线程等待，其它线程调用 signal() 或 signalAll() 方法唤醒等待的线程
 * 相比于 wait() 这种等待方式，await() 可以指定等待的条件，因此更加灵活
 * `await`是lock相关，`wait`是`sychornize`相关 



> 什么是CAS
CAS 指的是现代 CPU 广泛支持的一种对内存中的共享数据进行操作的一种特殊指令。这个指令会对内存中的共享数据做原子的读写操作。

简单介绍一下这个指令的操作过程：
1.CPU 会将内存中将要被更改的数据与期望的值做比较。
2. 这两个值相等时，CPU 才会将内存中的数值替换为新的值。否则便不做操作。
3. 最后，CPU 会将旧的数值返回。最后，CPU 会将旧的数值返回。

CAS 有 3 个操作数，内存值 V，旧的预期值 A，要修改的新值 B。
* 当且仅当预期值 A 和内存值 V 相同时，将内存值 V 修改为 B，
* 否则返回 V。
* 这是一种乐观锁的思路，它相信在它修改之前，没有其它线程去修改它

> volatile 是什么，怎么实现的

如果一个字段被声明成volatile，Java线程内存模型确保所有线程看到这个变量的值是一致的

作用
* 保证共享变量的可见性：保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。(单例模式双检测用volatile关键字修饰的)
  * 被volatile关键字修饰的变量修改时会立即写入主内存
  * 被volatile关键字修饰的变量修改时，其余线程存放的该变量的副本会被置为无效

* volatile变量的禁止指令重排序
  * 为了提高程序执行的性能，编译器和执行器(处理器)通常会对指令做一些优化(重排序)   

volatile的底层是通过（lock前缀指令）内存屏障来实现的。

* 内存屏障（memory barrier）是一个CPU指令。这条指令可以确保一些特定指令的执行顺序，影响一些数据的可见性(可能是某些指令执行后的结果)。
* 在每一个volatile写操作前面插入一个StoreStore屏障：保证在volatile写之前，其前面的所有普通写操作都已经刷新到主内存中
* 在每一个volatile写操作后面插入一个StoreLoad屏障：避免volatile写与后面可能有的volatile读/写操作重排序
* 在每一个volatile读操作后面插入一个LoadLoad屏障：禁止处理器把上面的volatile读与下面的普通读重排序
* 在每一个volatile读操作后面插入一个LoadStore屏障：禁止处理器把上面的volatile读与下面的普通写重排序

如果对声明了volatile变量进行写操作时，JVM会向处理器发送一条Lock前缀的指令，将这个变量所在缓存行的数据写会到系统内存。这一步确保了如果有其他线程对声明了volatile变量进行修改，则立即更新主内存中数据。但这时候其他处理器的缓
* 确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成
* 强制将对缓存的修改操作立即写入主存，利用缓存一致性机制，并且缓存一致性机制会阻止同时修改由两个以上CPU缓存的内存区域数据
* 如果是写操作，它会导致其他CPU中对应的缓存行无效

Lock前缀指令实际上相当于一个内存屏障（也成内存栅栏），它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成




> JMM
Java虚拟机规范中定义了一种Java内存模型（Java Memory Model，即JMM）来屏蔽掉各种硬件和操作系统的内存访问差异，以实现让Java程序在各种平台下都能达到一致的并发效果
* 主要目标就是定义程序中各个变量的访问规则，即在虚拟机中将变量存储到内存和从内存中取出变量这样的细节。

JMM中规定所有的变量都存储在主内存（Main Memory）中，每条线程都有自己的工作内存（Work Memory），线程的工作内存中保存了该线程所使用的变量的从主内存中拷贝的副本
* 线程对于变量的读、写都必须在工作内存中进行，而不能直接读、写主内存中的变量
* 本线程的工作内存的变量也无法被其他线程直接访问，必须通过主内存完成

线程的可见性问题
* 对于普通共享变量，线程A将变量修改后，体现在此线程的工作内存。在尚未同步到主内存时，若线程B使用此变量，从主内存中获取到的是修改前的值，便发生了共享变量值的不一致，

> sychronized 是公平锁吗
* 因为Synchronized 获取锁的行为是不公平的，并非是按照申请对象锁的先后时间分配锁的，每次对象锁被释放时，每个线程都有机会获得对象锁，这样有利于提高执行性能，但是也会造成线程饥饿现象
* `RentrantLock`将在创建锁时将锁new为ReentrantLock(true)就可以,公平锁的lock方法在进行cas判断时多了一个hasQueuedPredecessors()方法

> 谈谈对Synchronized关键字，类锁，方法锁，重入锁的理解

> 线程池

> 线程池的状态


> 线程池的关键字和流程
> execute与submit的区别
* execute和submit都属于线程池的方法
* execute所属顶层接口是Executor,submit所属顶层接口是ExecutorService，实现类ThreadPoolExecutor重写了execute方法,抽象类AbstractExecutorService重写了submit方法
* execute只能提交Runnable类型的任务，而submit既能提交Runnable类型任务也能提交Callable类型任务。 
* execute会直接抛出任务执行时的异常，submit会吃掉异常，可通过Future的get方法将任务执行时的异常重新抛出

> Executor 与 ExecutorService 和 Executors
* Executor 是一个抽象层面的核心接口
* ExecutorService 接口 对 Executor 接口进行了扩展，提供了返回 Future 对象，终止，关闭线程池等方法
* Executors 是一个工具类，类似于 Collections。提供工厂方法来创建不同类型的线程池
* Executor 接口定义了 execute()方法用来接收一个Runnable接口的对象，而 ExecutorService 接口中的 submit()方法可以接受Runnable和Callable接口的对象
* Executor 中的 execute() 方法不返回任何结果，而 ExecutorService 中的 submit()方法可以通过一个 Future 对象返回运算结果


> 怎么实现多线程计数器，优化
> AtomicInteger 和使用sychronized有什么区别
> 如果用一个线程写，多个线程读，怎么实现
> 自转锁和独占锁的比较

> synchronize的原理

> 谈谈对Synchronized关键字，类锁，方法锁，重入锁的理解

> static synchronized 方法的多线程访问和作用

> 同一个类里面两个synchronized方法，两个线程同时访问的问题

> lock原理

> 死锁的四个必要条件？

> 怎么避免死锁？

> 对象锁和类锁是否会互相影响？

> 如何控制某个方法允许并发访问线程的个数？

> 什么导致线程阻塞？

> 如何保证线程安全？

> 如何实现线程同步？

> 两个进程同时要求写或者读，能不能实现？如何防止进程的同步？

> 谈谈对多线程的理解

> 多线程有什么要注意的问题？

> 谈谈你对并发编程的理解并举例说明

> 谈谈你对多线程同步机制的理解？

> 如何保证多线程读写文件的安全？

> Java的并发、多线程、线程模型
