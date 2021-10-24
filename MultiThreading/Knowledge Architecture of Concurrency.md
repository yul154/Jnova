* Thread
  * Implementation
  * Thread status
  * Interupt thread
  * thread task execution
* Lock
  * Two lock implementations
  * ReentrantLock
  * Sychronized

* Cooperation between threads
  * `join`
  * `wait()` and `notify`,`notifyAll`
  * `await()` and `signal`,`signalAll`

* `java.util.concurrent`
* Thread Pool
* Thread-Safe Collections
----
# Thread
## Thread Implementation
1. `Runnable` --> `run()`
2. `Callable` --> `call()`
3. `Thread`  --> `new Thread()`

`Runnable` 和 `Callable` 只能当做一个可以在线程中运行的任务，并不是真正的线程。所以需要 `Thread` 类来调用。任务是通过线程来执行的

```
MyRunnable instance = new MyRunnable();
Thread thread = new Thread(instance);
thread.start();

MyCallable mc = new MyCallable();
FutureTask<Integer> ft = new FutureTask<>(mc);
Thread thread = new Thread(ft);
thread.start();
System.out.println(ft.get());

MyThread mt = new MyThread();
mt.start();
```

### 实现Runnable/Callable接口相比继承Thread类的优势
* 适合多个线程进行资源共享
* 可以避免java中单继承的限制
* 线程池只能放入Runable或Callable接口实现类，不能直接放入继承Thread的类

### Callable和Runnable的区别
* Callable重写的是call()方法，Runnable重写的方法是run()方法
* call()方法执行后可以有返回值，run()方法没有返回值
* call()方法可以抛出异常，run()方法不可以
* 运行Callable任务可以拿到一个Future对象，表示异步计算的结果。通过Future对象可以了解任务执行情况，可取消任务的执行，还可获取执行结果

### `Future` and `FutureTask`
* `Future<V>` 接口是用来获取异步计算结果的,Future只是一个接口，我们无法直接创建对象使用的.
*  `FutureTask` which implements both the `Future` and `Runnable` interfaces,由此看出FutureTask它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值.

## Thread status

|Status|Transfer|
|-------|--------|
|`new`| 新创建的一个线程，处于等待状态,`start()`方法标识启动一个线程，并分配资源,让其状态变成`runnable`|
|`runnable`|可运行状态，并不是已经运行，具体的线程调度各操作系统决定. 当线程调用了 start() 方法后，线程则处于就绪 Ready 状态，等待操作系统分配 CPU 时间片，分配后则进入 Running 运行状态,`yield()` 让当前运行线程回到可运行状态，交出时间分片让高优先级的线程先执行，如果持有锁的话是不会释放掉的。
|`waiting`|可被唤醒的等待状态,此状态可以通过 synchronized 获得锁，调用 `wait` `await` `join` 方法进入等待状态。最后通过 `notify、notifyall` and `signal,signalAll`唤醒|
|`timeWaiting()`|指定时间内让出CPU资源，此时线程不会被执行，也不会被系统调度，直到等待时间到期后才会被执行,`Thread.sleep`、`Object.wait`、`Thread.join`
|`blocked()`| 当发生锁竞争状态下，没有获得锁的线程会处于挂起状态。比如正在等待另一个线程的 synchronized 块的执行释|
|`terminated`| 从 New 到 Terminated 是不可逆的。一般是程序流程正常结束或者发生了异常 |

### Difference ammong `Thread.sleep`、`Object.wait`、`Thread.join`,`yield`
* `join()`方法可以使得一个线程在另一个线程结束后再执行,如果线程A调用了线程B的join方法，线程A将被阻塞，等待线程B执行完毕后线程A才会被执行
* `yield`只是让当前对象回到就绪状态，还是有可能马上被再次被调用执行，该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行
* `wait` 让出锁, `sleep` 不让出锁,`yield`也不让出锁
* `yield` 不能被中断，而 `sleep` 则可以接受中断。
* `yield` 直接进入 ready的状态，`wait`唤醒后禁图ready状态，`sleep`直接进入`running`状态
*  `join()`的底层确实是`wait()`，`wait(`)也确实释放锁，但是释放的是thread的对象锁

### 线程可以阻塞于四种状态：
* 当线程执行 Thread.sleep() 时，它一直阻塞到指定的毫秒时间之后，或者阻塞被另一个线程打断；
* 当线程碰到一条 wait() 语句时，它会一直阻塞到接到通知 notify()、被中断或经过了指定毫秒时间为止（若制定了超时值的话）
* 线程阻塞与不同 I/O 的方式有多种。常见的一种方式是 InputStream 的 read() 方法，该方法一直阻塞到从流中读取一个字节的数据为止，它可以无限阻塞，因此不能指定超时时间；
* 线程也可以阻塞等待获取某个对象锁的排他性访问权限（即等待获得 synchronized 语句必须的锁时阻塞）。


##  Interrupt thread
A thread terminates when its run method returns, or if an exception occurs that is not caught in the method.

|Method|Functionality|
|-------|--------|
|`interrupt()` |其实调用Thread对象的interrupt函数并不是立即中断线程，只是将线程中断状态标志设置为true，当线程运行中有调用其阻塞的函数（Thread.sleep，Object.wait，Thread.join等）时，阻塞函数调用之后，会不断地轮询检测中断状态标志是否为true，如果为true，则停止阻塞并抛出InterruptedException异常，同时还会重置中断状态标志；如果为false，则继续阻塞，直到阻塞正常结束|
|`isInterrupted`|函数只是检测线程中断状态标志。中断状态不会被清除|
|`Thread.interrupted(`|检测当前线程是否被中断，并且中断状态会被清除（即重置为false）；由于它是静态方法，因此不能在特定的线程上使用，只能报告调用它的线程的中断状态|


调用 `Executor` 的 `shutdown()` 方法会等待线程都执行完毕之后再关闭，但是如果调用的是 `shutdownNow()` 方法，则相当于调用每个线程的 `interrupt()` 方法。

## thread task execution
* Executor(Thread pool) :  管理多个异步任务的执行，而无需程序员显式地管理线程的生命周期。这里的异步是指多个任务的执行互不干扰，不需要进行同步操作。
* Daemon: 守护线程是程序运行时在后台提供服务的线程,守护线程作用是为其他前台线程的运行提供便利服务,当所有非守护线程结束时，程序也就终止，同时会杀死所有守护线程


* thread.setDaemon(true)必须在start()之前设置，否则会抛IllegalThreadStateException异

---
# Lock
Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问
1. 第一个是 JVM 实现的 synchronized
2. 而另一个是 JDK 实现的 ReentrantLock。

## synchronized
它只作用于同一个对象，如果调用两个对象上的同步代码块，就不会进行同步,两个线程调用了不同对象的同步代码块，因此这两个线程就不需要同步
* 作用于整个类，也就是说两个线程调用同一个类的不同对象上的这种同步语句，也会进行同步
* 对于静态方法的同步，锁是当前类的Class对象。

`Synchronized` 原理
* 对一个对象的监视器（monitor）的获取。任意一个对象都拥有自己的监视器，
* 当同步代码块或同步方法时，执行方法的线程必须先获得该对象的监视器才能进入同步块或同步方法，没有获取到监视器的线程将会被阻塞，并进入同步队列，状态变为BLOCKED。
* 当成功获取监视器的线程释放了锁后，会唤醒阻塞在同步队列的线程，使其重新尝试对监视器的获取。


## ReentrantLock
ReentrantLock是一种递归无阻塞的同步机制。

ReentrantLock 是 （J.U.C）包中的锁，相比于 synchronized，它多了以下高级功能：
* 等待可中断:当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。
* 可实现公平锁: 公平锁是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁。synchronized 中的锁是非公平的，ReentrantLock 默认情况下也是非公平的，但可以通过带布尔值的构造函数要求使用公平锁。
* 锁绑定多个条件 : 一个 ReentrantLock 对象可以同时绑定多个 Condition 对象。

### synchronized和 ReentrantLock 的比较

1.区别：
* Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；
* synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
* Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；`lockInterruptibly()`的用法体现了Lock的可中断性
* 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
* Lock可以提高多个线程进行读操作的效率。
* 总结：ReentrantLock相比synchronized，增加了一些高级的功能。但也有一定缺陷。

### 使用场景
真正的高级用户喜欢选择能够找到的最简单工具，直到他们认为简单的工具不适用为止。

在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而 当竞争资源非常激烈时（即有大量线程同时竞争），此时ReentrantLock的性能要远远优于synchronized。在确实需要一些 synchronized 所没有的特性的时候，比如时间等候、可中断锁等候、无块结构锁、多个条件变量或者轮询锁。 ReentrantLock 还具有可伸缩性的好处，应当在高度争用的情况下使用它，但是请记

---
# Cooperation between threads
**`join()`**
*  在线程中调用另一个线程的`join()`方法，会将当前线程挂起，而不是忙等待，直到目标线程结束。

**`wait`,`notify`,`notifyAll`**
* 调用 wait()使得线程等待某个条件满足，线程在等待时会被挂起，当其他线程的运行使得这个条件满足时，其它线程会调用 notify() 或者 notifyAll()来唤醒挂起的线程。
* 它们都属于 Object 的一部分，而不属于 Thread。
* 只能用在同步方法或者同步控制块中使用！否则会在运行时抛出 IllegalMonitorStateExeception。

**`await`,`signal`,`signalAll`**
* `java.util.concurrent` 类库中提供了 Condition 类来实现线程之间的协调
* 可以在 Condition 上调用 `await()` 方法使线程等待，其它线程调用` signal()` 或 `signalAll()`方法唤醒等待的线程。

---
# JUC `java.util.concurrent`
## AbstractQueuedSynchronizer （AQS）
AQS框架是J.U.C中实现锁及同步机制的基础，其底层是通过调用 LockSupport .unpark()和 LockSupport .park()实现线程的阻塞和唤醒

* 它底层使用的是双向列表，是队列的一种实现 , 因此也可以将它当成一种队列。

AQS分为两种模式：独占模式和共享模式，
* 像ReentrantLock是基于独占模式模式实现的，
* CountDownLatch、CyclicBarrier等是基于共享模式

* AQS其实就是一个可以给我们实现锁的框架, 内部实现的关键是：先进先出的队列、state 状态

J.U.C中的同步器主要用于协助线程同步，有以下四种：
1. 闭锁 CountDownLatch: 利用它可以实现类似计数器的功能,主要用于让一个主线程等待一组事件发生后继续执行。
2. 栅栏 CyclicBarrier: 主要用于等待其它线程，且会阻塞自己当前线程,它的计数器是递增的,直到计数器的值和设置的值相等，等待的所有线程才会继续执行
3. 信号量 Semaphore: 信号量主要用于控制访问资源的线程个数，常常用于实现资源池,可以控制对互斥资源的访问线程数
4. 交换器 Exchanger: 交换器主要用于线程之间进行数据交换

Summary
* CountDownLatch 和 CyclicBarrier 都能够实现线程之间的等待，只不过它们侧重点不同：
   * CountDownLatch 一般用于某个线程A等待若干个其他线程执行完任务之后，它才执行；
   * CyclicBarrier 一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行；
   * 另外，CountDownLatch 是不能够重用的，而 CyclicBarrier 是可以重用的。*
* Semaphore 其实和锁有点类似，它一般用于控制对某组资源的访问权限。


**ForkJoin**
主要用于并行计算中，和 MapReduce 原理类似，都是把大的计算任务拆分成多个小任务并行计算。
* ForkJoinPool 实现了工作窃取算法来提高 CPU 的利用率。每个线程都维护了一个双端队列，用来存储需要执行的任务。工作窃取算法允许空闲的线程从其它线程的双端队列中窃取一个任务来执行。窃取的任务必须是最晚的任务，避免和队列所属线程发生竞争

---
# ThreadPool

什么是线程池

* 线程池顾名思义就是事先创建若干个可执行的线程放入一个池（容器）中，需要的时候从池中获取线程不用自行创建，使用完毕不需要销毁线程而是放回池中，从而减少创建和销毁线程对象的开销

线程池原理
* 线程池中的核心线程数，当提交一个任务时，线程池创建一个新线程执行任务，直到当前线程数等于corePoolSize；
* 如果当前线程数为 corePoolSize，继续提交的任务被保存到阻塞队列中，等待被执行；如果阻塞队列满了，那就创建新的线程执行当前任务；直到线程池中的线程数达到 maxPoolSize，这时再有任务来，只能执行 reject() 处理该任务。

*线程池分类 *
* `newFixedThreadPool()` 说明：初始化一个指定线程数的线程池，其中 corePoolSize == maxiPoolSize，使用 LinkedBlockingQuene 作为阻塞队列 特点：即使当线程池没有可执行任务时，也不会释放线程。(用于负载比较重的服务器，为了资源的合理利用，需要限制当前线程数量)
* `newCachedThreadPool()` 说明：初始化一个可以缓存线程的线程池，默认缓存60s，线程池的线程数可达到 Integer.MAX_VALUE，即 2147483647，内部使用 SynchronousQueue 作为阻塞队列； 特点：在没有任务执行时，当线程的空闲时间超过 keepAliveTime，会自动释放线程资源；当提交新任务时，如果没有空闲线程，则创建新线程执行任务，会导致一定的系统开销； 因此，使用时要注意控制并发的任务数，防止因创建大量的线程导致而降低性能。(用于并发执行大量短期的小任务，或者是负载较轻的服务器；)
* `newSingleThreadExecutor()` 说明：初始化只有一个线程的线程池，内部使用 LinkedBlockingQueue 作为阻塞队列。 特点：如果该线程异常结束，会重新创建一个新的线程继续执行任务，唯一的线程可以保证所提交任务的顺序执行 ( 用于串行执行任务的场景，每个任务必须按顺序执行，不需要并发执行)
* `newScheduledThreadPool()` 特点：初始化的线程池可以在指定的时间内周期性的执行所提交的任务，在实际的业务场景中可以使用该线程池定期的同步数据

关于 workQueue 参数，有四种队列可供选择：
* ArrayBlockingQueue：基于数组结构的有界阻塞队列，按 FIFO 排序任务；
* LinkedBlockingQuene：基于链表结构的阻塞队列，按 FIFO 排序任务；
* SynchronousQuene：一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于 ArrayBlockingQuene；
* PriorityBlockingQuene：具有优先级的无界阻塞队列

**线程池的核心参数,自己创建的话，什么情况下使用什么参数**
* corePoolSize 核心线程数
* maximumPoolSize 最大线程数
* keepAliveTime 空闲线程存活时间
* unit 对应keepAliveTime的计量单位
* workQueue 工作队列
* threadFactory 线程工厂，用于创建线程可以指定线程名、是否为daemon线程等
* handler 拒绝策略

**关于 handler 参数，线程池的饱和策略，当阻塞队列满了，且没有空闲的工作线程，如果继续提交任务，必须采取一种策略处理该任务，线程池提供了 4 种策略**

* ThreadPoolExecutor.AbortPolicy：丢弃任务并抛出RejectedExecutionException异常。
* ThreadPoolExecutor.DiscardPolicy：丢弃任务，但是不抛出异常。
*ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
* ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务

