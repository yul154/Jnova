# Java 线程
* 线程的实现
* 线程状态
* 基础线程机制
* 中断
* 线程的协作
----
# 线程的实现
1. `Runnable --> run()` 实现Runnable接口
2. `Callable --> call()` 实现Callable接口
3. `Thread --> new Thread()` 继承Thread类，重写run方法

Runnable 和 Callable 只能当做一个可以在线程中运行的任务，并不是真正的线程。所以需要 Thread 类来调用

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

## `Callable`的底层实现
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

###  `Callable`怎么实现异步结果的
如果出现一个计算量稍大用时较长而后面需要结果的情况，可以先把这个任务提交到另外的线程异步执行，主线程仍然继续做运行等到需要计算结果的时候再来获取
* Java中提供了Future与Callable来实现可以获取异步执行结果的一套框架
* Callable任务被包装成了RunnableFuture对象
* FutureTask被生产者和消费者共享，生产者运行run方法计算结果，消费者通过get方法获取结果
  * 状态位变化，并唤醒线程
  *

###  Callable和Runnable的区别
* `Callable`重写的是`call()`方法，`Runnable`重写的方法是`run()`方法
* `call()`方法执行后可以有返回值，`run()`方法没有返回值
* `call()`方法可以抛出异常，`run()`方法不可以
* 运行`Callable`任务可以拿到一个`Future`对象，表示异步计算的结果。通过`Future`对象可以了解任务执行情况，可取消任务的执行，还可获取执行结果


### 实现Runnable/Callable接口相比继承Thread类的优势
* 适合多个线程进行资源共享
* 可以避免java中单继承的限制
* 线程池只能放入Runable或Callable

### 三种方式的区别
* 实现 Runnable 接口可以避免 Java 单继承特性而带来的局限；增强程序的健壮性，代码能够被多个线程共享，代码与数据是独立的；适合多个相同程序代码的线程区处理同一资源的情况。
* 继承 Thread 类和实现 Runnable 方法启动线程都是使用 start() 方法，然后 JVM 虚拟机将此线程放到就绪队列中，如果有处理机可用，则执行 run() 方法。
* 实现 Callable 接口要实现 call() 方法，并且线程执行完毕后会有返回值。其他的两种都是重写 run() 方法，没有返回值。
---
# 线程状态

|Status|Transfer|
|-------|--------|
|`new`| 新创建的一个线程，处于等待状态,`start()`方法标识启动一个线程，并分配资源,让其状态变成`runnable`|
|`runnable`|可运行状态，并不是已经运行，具体的线程调度各操作系统决定. 当线程调用了 start() 方法后，线程则处于就绪 Ready 状态，等待操作系统分配 CPU 时间片，分配后则进入 Running 运行状态,`yield()` 让当前运行线程回到可运行状态，交出时间分片让高优先级的线程先执行，如果持有锁的话是不会释放掉的。
|`waiting`|可被唤醒的等待状态,此状态可以通过 synchronized 获得锁，调用 `wait()` `await` `join()` 方法进入等待状态。最后通过 `notify、notifyall` and `signal,signalAll`唤醒|
|`timeWaiting()`|指定时间内让出CPU资源，此时线程不会被执行，也不会被系统调度，直到等待时间到期后才会被执行,`Thread.sleep(long)`、`Object.wait(long)`、`Thread.join(long)`
|`blocked()`| 当发生锁竞争状态下，没有获得锁的线程会处于挂起状态。比如正在等待另一个线程的 synchronized 块的执行释|
|`terminated`| 从`New` 到`Terminated`是不可逆的。一般是程序流程正常结束或者发生了异常 |

![image](https://user-images.githubusercontent.com/27160394/142201395-66643e61-3041-4b89-b754-9c6fb8cc457c.png)


## Difference ammong `Thread.sleep`、`Object.wait`、`Thread.join`,`yield`
* `join()`方法可以使得一个线程在另一个线程结束后再执行,如果线程A调用了线程B的join方法，线程A将被阻塞，等待线程B执行完毕后线程A才会被执行
* `yield`只是让当前对象回到就绪状态，还是有可能马上被再次被调用执行，该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行
* `wait` 让出锁, `sleep` 不让出锁,`yield`也不让出锁
* `yield` 不能被中断，而 `sleep` 则可以接受中断。
* `yield` 直接进入ready的状态，`wait`唤醒后进入ready状态，`sleep`直接进入`running`状态
*  `join()`的底层确实是`wait()`，`wait(`)也确实释放锁，但是释放的是thread的对象锁


## 线程可以阻塞于四种状态：
* 当线程执行 Thread.sleep() 时，它一直阻塞到指定的毫秒时间之后，或者阻塞被另一个线程打断；
* 当线程碰到一条 wait() 语句时，它会一直阻塞到接到通知 notify()、被中断或经过了指定毫秒时间为止（若制定了超时值的话）
* 线程阻塞与不同 I/O 的方式有多种。常见的一种方式是 InputStream 的 read() 方法，该方法一直阻塞到从流中读取一个字节的数据为止，它可以无限阻塞，因此不能指定超时时间；
* 线程也可以阻塞等待获取某个对象锁的排他性访问权限（即等待获得 synchronized 语句必须的锁时阻塞）。

----
# 基础线程机制

## Executor
> Executor 管理多个异步任务的执行，而无需程序员显式地管理线程的生命周期。这里的异步是指多个任务的执行互不干扰，不需要进行同步操作。

主要有三种 Executor：

CachedThreadPool：一个任务创建一个线程；
FixedThreadPool：所有任务只能使用固定大小的线程；
SingleThreadExecutor：相当于大小为 1 的 FixedThreadPool。

**为什么引入Executor线程池框架**
new Thread() 的缺点
* 每次 new Thread() 耗费性能
* 调用 new Thread() 创建的线程缺乏管理，被称为野线程，而且可以无限制创建，之间相互竞争，会导致过多占用系统资源导致系统瘫痪。
* 不利于扩展，比如如定时执行、定期执行、线程中断

采用线程池的优点
* 重用存在的线程，减少对象创建、消亡的开销，性能佳
* 可有效控制最大并发线程数，提高系统资源的使用率，同时避免过多资源竞争，避免堵塞
* 提供定时执行、定期执行、单线程、并发数控制等功能

## Daemon
> 守护线程是程序运行时在后台提供服务的线程，不属于程序中不可或缺的部分。

用户线程即运行在前台的线程，而守护线程是运行在后台的线程.守护线程作用是为其他前台线程的运行提供便利服务，而且仅在普通、非守护线程仍然运行时才需要，比如垃圾回收线程就是一个守护线程。当 JVM 检测仅剩一个守护线程，而用户线程都已经退出运行时，JVM 就会退出，因为没有如果没有了被守护这，也就没有继续运行程序的必要了。如果有非守护线程仍然存活，JVM 就不会退出。

* `setDaemon(true)`必须在调用线程的 start() 方法之前设置，否则会跑出 IllegalThreadStateException 异常。
* 在守护线程中产生的新线程也是守护线程
* 不要认为所有的应用都可以分配给守护线程来进行服务，比如读写操作或者计算逻辑
---
# 中断
> A thread terminates when its run method returns, or if an exception occurs that is not caught in the method.

通过调用一个线程的 interrupt() 来中断该线程，如果该线程处于阻塞、限期等待或者无限期等待状态，那么就会抛出 InterruptedException，从而提前结束该线程。但是不能中断 I/O 阻塞和 synchronized 锁阻塞

|Method|Functionality|
|-------|--------|
|`interrupt()` |其实调用Thread对象的interrupt函数并不是立即中断线程，只是将线程中断状态标志设置为true，当线程运行中有调用其阻塞的函数（Thread.sleep，Object.wait，Thread.join等）时，阻塞函数调用之后，会不断地轮询检测中断状态标志是否为true，如果为true，则停止阻塞并抛出InterruptedException异常，同时还会重置中断状态标志；如果为false，则继续阻塞，直到阻塞正常结束|
|`isInterrupted`|函数只是检测线程中断状态标志。中断状态不会被清除|
|`Thread.interrupted(`|检测当前线程是否被中断，并且中断状态会被清除（即重置为false）；由于它是静态方法，因此不能在特定的线程上使用，只能报告调用它的线程的中断状态|

>`stop`
它在终止一个线程时会强制中断线程的执行，不管run方法是否执行完了，并且还会释放这个线程所持有的所有的锁对象，导致数据不一致

### Executor 的中断操作
* 调用`Executor`的`shutdown()`方法会等待线程都执行完毕之后再关闭，但是如果调用的是 shutdownNow() 方法，则相当于调用每个线程的 interrupt() 方法
* 如果只想中断`Executor`中的一个线程，可以通过使用`submit()`方法来提交一个线程，它会返回一个 Future<?> 对象，通过调用该对象的 cancel(true) 方法就可以中断线程。
