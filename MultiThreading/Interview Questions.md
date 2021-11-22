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



# Threadlocal
> Threadlocal 是什么，有什么用

ThreadLocal类的目的是为每个线程单独维护一个变量的值，避免线程间对同一变量的竞争访问，适用于一个变量在每个线程中需要有自己独立的值的场合

ThreadLocal是通过将变量设置成Thread的局部变量，即使用该变量的线程提供一个独立的副本，可以独立修改，不会影响其他线程的副本，这样来解决多线程的并发问题。
* 这种变量只在线程的生命周期内有效
* ThreadLocal变量的活动范围为某线程，是该线程“专有的，独自霸占”的，对该变量的所有操作均由该线程完成！
* ThreadLocal不是用来解决共享对象的多线程访问的竞争问题的,因为ThreadLocal.set()到线程中的对象是该线程自己使用的对象,其他线程是访问不到的。当线程终止后，这些值会作为垃圾回收。
* 一般作为类的 private static的变量来给标记一些线程的状态


> Threadlocal的底层实现

* `ThreadLocalMap`是`ThreadLocal`的静态内部类，也是实际保存变量的类。
*  `Entry`是`ThreadLocalMap`的静态内部类。`ThreadLocalMap`持有`一个Entry`数组
  *  每个Entry都有k--v,ThreadLocalMap里面存储，key是ThreadLocal类的实例对象，value为用户的值
* Thread类中有一个成员变量属于ThreadLocalMap类((`threadlocals`)
* 每个线程的内部都会有一个ThreadLocalMap的变量
* 多个ThreadLocal类去操作同一个线程，那么线程中的ThreadLocalMap就会有多个键值对


```
public void set(T value) 
{
      Thread t = Thread.currentThread();
      ThreadLocalMap map = getMap(t);
      if (map != null)
          map.set(this, value);
      else
          createMap(t, value);
 }

```
> Threadlocal 使用场景
提供一个线程内公共变量（比如本次请求的用户信息减少同一个线程内多个函数或者组件之间一些公共变量的传递的复杂度，或者为线程提供一个私有的变量副本，这样每一个线程都可以随意修改自己的变量副本

当我们只想在本身的线程内使用的变量，可以用 ThreadLocal 来实现,存到ThreadLocal中只是为了方便在程序中同一个线程之间传递这个变量。

* 线程安全，包裹线程不安全的工具类， ThreadLocal主要是线程不安全的类在多线程中使用
* 在Spring项目中Dao层中装配的Connection肯定是线程安全的，其解决方案就是采用ThreadLocal方法：当每个请求线程使用Connection的时候， 都会从ThreadLocal获取一次，如果为null，说明没有进行过数据库连接，连接后存入ThreadLocal中，如此一来，每一个请求线程都保存有一份 自己的Connection。于是便解决了线程安全问题
* Session 是在处理一个用户会话过程中产生并使用的：
* 与前端对接的接口中用到了ThreadLocal。大概流程是，前端在请求后端接口时在header带上token，拦截器通过token获取到用户信息，通过ThreadLocal保存。主要代码如下：
 * 每一次接口请求都是一个线程，在校验接口合法后把userid存入ThreadLocal 
 * 全局存储用户信息：拦截器中解析Token，获取用户信息，调用自定义的类(AuthNHolder)存入ThreadLocal中，当请求结束的时候，将ThreadLocal存储数据清空



> ThreadLocal是解决线程安全的吗？
线程安全问题产生的两个前提条件： 
1. 数据共享。多个线程访问同样的数据。 
2. 共享数据是可变的。多个线程对访问的共享数据作出了修改。

一般用ThreadLocal都不会将一个共享变量放到线程的ThreadLocal中

> Threadlocal 弱引用
为了处理非常大和生命周期非常长的线程

如果线程没有结束，但引用 ThreadLocal 的对象被回收，ThreadLocalMap 如果持有 ThreadLocal 的强引,内存泄漏了

如果key 使用强引用
* 对于ThreadLocal来说，即使我们使用结束，引用的ThreadLocal的对象被回收了，也会因为线程本身存在该对象的引用，处于对象可达状态，垃圾回收器无法回收。
 * 这个时候当ThreadLocal太多的时候，这个threadlocal就会因为和entry存在强引用无法被回收！造成内存泄漏，除非线程结束，线程被回收了，map也跟着回收
 
* ThreadLocalMap本身并没有为外界提供取出和存放数据的API，我们所能获得数据的方式只有通过ThreadLocal类提供的API来间接的从ThreadLocalMap取出数据，
 * 所以，当我们用不了key（ThreadLocal对象）的API也就无法从ThreadLocalMap里取出指定的数据。
 * Threaddlocal对象被回收，就没法从ThreadLocalMap里取出数据了


如果Key是弱引用
* 最好的方法是在A对象被回收后，系统自动回收对应的Entry对象
* 所以当把threadlocal实例置为null以后,没有任何强引用指向threadlocal实例,所以threadlocal就可以顺利被gc回


> 内存泄漏

ThreadLocal 在没有外部强引用时，发生 GC时会被回收，
* 那么 ThreadLocalMap 中保存的 key 值就变成了 null，而 Entry 又被 threadLocalMap 对象引用，threadLocalMap 对象又被 Thread 对象所引用，那么当 Thread 一直不终结的话，value 对象就会一直存在于内存中，
* 也就导致了内存泄漏，直至 Thread 被销毁后，才会被回收

ThreadLocal变量最终是设置到了Thread的ThreadLocalMap中。Key是ThreadLoacl变量的引用，Value是设置到ThreadLocal中的值(Map的key是ThreadLocal类的实例对象，value为用户的值)
* 这个Key是一个弱引用,value是个强硬用哪个
* 如果ThreadLocal引用变为null之后，不再对这个ThreadLocal变量进行操作了。那么就会由于Value这个强引用一直存在，一直都会有内存泄露的问题
* 最好的办法就是ThreadLocal使用完毕之后，手动调用remove方法将其回收掉。
* ThreadLocal的set/get/remove方法中在遇到key==null的节点时（被称为stale腐烂节点），会进行清理等处理逻辑
* 一般情况下我们会使用线程池，这样会在执行完后表现为线程结束，实际上线程只是回到了池子中等待下次调度的时候再次使用，这种情况时ThreadLocal是会被复用的

> 为什么value 设置成强引用

* 不设置为弱引用，是因为不清楚这个Value除了map的引用还是否还存在其他引用，
* 如果不存在其他引用，当GC的时候就会直接将这个Value干掉了，而此时我们的ThreadLocal还处于使用期间，就会造成Value为null的错误，所以将其设置为强引用。
* 我们可以看到一个现象：在set,get,remove的时候都调用了`expungeStaleEntry`来将所有失效的Entity移除


> 为什么 使用 ThreadLocal 的时候，最好要声明为静态的；

ThreadLocal设计初衷是一个线程级别的变量

因为如果它是一个实例级字段，那么它实际上是"每个线程-每个实例"，而不仅仅是保证的"每个线程"。通常这不是您要查找的语义。

通常，它所保存的对象之类的对象仅限于用户对话，Web请求等。您不希望它们也被细分为类的实例。
一个Web请求=>一个Persistence会话。
没有一个Web请求=>每个对象一个持久性会话


> Threadlocal 应用在你项目里哪里
* 项目中封装的日期工具类 -> 每个有自己的副本，然后根据模拟时间的长度，创建当前线程的结束时间

> 为啥threalocalmap写在threadlocal里不是thread里

* 将ThreadLocalMap定义在Thread类内部看起来更符合逻辑，
* 但是ThreadLocalMap并不需要Thread对象来操作，所以定义在Thread类内只会增加一些不必要的开销。
* 定义在ThreadLocal类中的原因是ThreadLocal类负责ThreadLocalMap的创建，仅当线程中设置第一个ThreadLocal时，才为当前线程创建ThreadLocalMap，之后所有其他ThreadLocal变量将使用一个ThreadLocalMap

> 为什么ThreadLocalMap 设计为ThreadLocal 内部类

主要是说明ThreadLocalMap 是一个线程本地的值，它所有的方法都是private的，也就意味着除了ThreadLocal 这个类，其他类是不能操作ThreadLocalMap 中的任何方法的，这样就可以对其他类是透明的


> ThreadLocal与synchronized的区别

* ThreadLocal 是通过让每个线程独享自己的副本，避免了资源的竞争。
* synchronized 主要用于临界资源的分配，在同一时刻限制最多只有一个线程能访问该资源。
* ThreadLocal 并不是用来解决共享资源的多线程访问的问题，因为每个线程中的资源只是副本，并不共享。因此ThreadLocal适合作为线程上下文变量，简化线程内传参


> java 自带的threadlocal 可以在父子线程传递吗
```
threadlocal.ThreadlocalMap.inheritaleThreadlcals.
```
实现父线程创建子线程时，将值由父线程传递到子线程

* 为了解决子线程复用主线程ThreadLocal的问题，Thread类中还有另一个ThreadLocalMap：inheritableThreadLocals，里面保存的是InheritableThreadLocal，它是ThreadLocal的子类，Thread类初始化时会默认从父线程继承inheritableThreadLocals；

* 原理是子线程是通过在父线程中通过调用new Thread()方法来创建子线程，Thread#init方法在Thread的构造方法中被调用。在init方法中拷贝父线程数据到子线程中。
* 但是注意！我们现在一般都会使用线程池创建新线程，这种时候所谓的创建新线程只是复用了线程池中已有的线程，并不会调用new Thread()方法，因此使用InheritableThreadLocal往往是没效果的


## AQS

> 什么情况可以判断我可以继续持有这把锁
`getExclusiveOwnerThread`
`ReentrantLock.isHeldByCurrentThread().`

* 当线程尝试获取锁时，可重入锁先尝试获取并更新status值，如果status == 0表示没有其他线程在执行同步代码，则把status置为1，当前线程开始执行。如果status != 0，则判断当前线程是否是获取到这个锁的线程，如果是的话执行status+1，且当前线程可以再次获取锁。
* 而非可重入锁是直接去获取并尝试更新当前status的值，如果status != 0的话会导致其获取锁失败，当前线程阻塞。

* 释放锁时，可重入锁同样先获取当前status的值，在当前线程是持有锁的线程的前提下。如果status-1 == 0，则表示当前线程所有重复获取锁的操作都已经执行完毕，然后该线程才会真正释放锁。
* 而非可重入锁则是在确定当前线程是持有锁的线程之后，直接将status置为0，将锁释放。

> 怎么判断一个读写锁

* 在独享锁中这个值通常是0或者1（如果是重入锁的话state值就是重入的次数），在共享锁中state就是持有锁的数量。
* 但是在ReentrantReadWriteLock中有读、写两把锁，所以需要在一个整型变量state上分别描述读锁和写锁的数量（或者也可以叫状态）。于是将state变量“按位切割”切分成了两个部分，高16位表示读锁状态（读锁个数），低16位表示写锁状态（写锁个数）


