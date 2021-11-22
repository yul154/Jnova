# 线程池的框架

<img width="433" alt="Screen Shot 2021-11-22 at 7 06 28 PM" src="https://user-images.githubusercontent.com/27160394/142850857-36b6b71a-d70f-4ecc-a954-3e7084514079.png">


**`Executor 执行器`**
* 可执行任意一个Runnable任务
* 此接口的目的是将任务提交和任务执行进行解耦
* 用户无需关注如何创建线程，如何调度线程来执行任务，用户只需提供Runnable对象，将任务的运行逻辑提交到执行器(Executor)中
* `execute(Runnable command)` 在将来的某个时间执行给定的命令。 该命令可以在一个新线程，一个合并的线程中或在调用线程中执行

```
public interface Executor {
	// @since 1.5
	void execute(Runnable command);
}
```
```
new Thread(new(RunnableTask)).start()
Executor ex = anExcutor;
ex.execut(new RunnableTask());
```


**`ExecutorService`**
* 提供了生命周期管理的方法，以及可以跟踪异步任务执行状况返回 Future 的方法
```
public interface ExecutorService extends Executor {

	// 关闭。执行已经提交的任务，但不接受新任务。
	// 此方法用于关闭不需要使用的执行器，内部会做资源回收的操作，如回收线程池
	void shutdown();
	// 试图停止所有正在执行的活动任务，暂停处理正在等待的任务，并返回等待执行的任务列表
	// 此方法一般不使用
	List<Runnable> shutdownNow();
	// 执行器是否已经被关闭
	boolean isShutdown();
	// 只有当shutdown()或者shutdownNow()被调用，而且所有任务都执行完成后才会返回true
	boolean isTerminated();
	// 阻塞。直到所有的任务都被执行完，或者超时
	boolean awaitTermination(long timeout, TimeUnit unit) throws InterruptedException;

	// =====下面为任务提交方法(使用Future跟踪任务执行情况)=====
	// 以下三个是最最最最最最最常用的方法：执行任务
	// Callable任务有返回值。所以Future.get()的时候可以拿到此返回值
	<T> Future<T> submit(Callable<T> task);
	// Runnable任务。执行完后Future.get()永远返回的是result这个值
	<T> Future<T> submit(Runnable task, T result);
	// 执行完后Future.get()永远返回的是null
	Future<?> submit(Runnable task);

	// 这几个方法在**批量执行**或**多选一**的业务场景中非常方便。
	// invokeAll：所有任务都完成（包括成功/被中断/超时）后才会返回，所以它会阻塞哦（时间由最长的决定）
	// invokeAny()在任意一个任务成功（被中断/超时）后就会返回，只需要成功一个就返回（时间由最短的决定）
	<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) throws InterruptedException;
	<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException;
	<T> T invokeAny(Collection<? extends Callable<T>> tasks) throws InterruptedException, ExecutionException;
	<T> T invokeAny(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) throws InterruptedException, ExecutionException, TimeoutException;
}
```
ExecutorService的实现分为两个分支：
* `AbstractExecutorService` -> 一般为基于线程池的实现
* `ScheduledExecutorService` -> 延迟/周期执行服务

**`AbstractExecutorService`**
* 对`ExecutorService`的抽象实现
* 它实现了接口的部分方法，但是它并没有存放任务或者线程的数组或者Collection，也就是说它依旧和线程池没有半毛钱关系

```
public abstract class AbstractExecutorService implements ExecutorService {

	@Override
    public Future<?> submit(Runnable task) {
        if (task == null) throw new NullPointerException();
        RunnableFuture<Void> ftask = newTaskFor(task, null);
        execute(ftask);
        return ftask;
    }
    ... // 其它submit方法原理一样，底层执行调用的均是Executor#execute方法
}
```

**`ThreadPoolExecutor 带线程池的执行器`**
* 它是一个内置线程池的执行器, 它会把`Runnable`任务的执行均扔进线程池里面进行执行，效率最高
* 主要功能是创建线程池，给任务分配线程资源，执行任务

```
public class ThreadPoolExecutor extends AbstractExecutorService {
	// 核心线程数
	private volatile int corePoolSize;
	// 最大线程数
	private volatile int maximumPoolSize;
	// 任务队列（当任务太多了，就放在这里排队）
	private final BlockingQueue<Runnable> workQueue;
	// 空闲线程的超时时间，超时就回收它（非core线程）
	private volatile long keepAliveTime;
	// 表示keepAliveTime的单位。
	TimeUnit unit
	// 为了方便的管理我们的线程，我们可以自定义线程创建工厂，来对我们的线程进行个性化的设置
	private volatile ThreadFactory threadFactory;
	// 线程池拒绝处理函数（如任务太多了，如何拒绝）
	// 默认策略是：AbortPolicy。要决绝是抛出异常：RejectedExecutionException
	private volatile RejectedExecutionHandler handler;
	
}
```
* `execute()`它决定了你最终如何去执行任务（同步or异步？用老的线程还是用新的线程等等）
```
public void execute(Runnable command) {
        if (command == null) throw new NullPointerException();
        
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        } else if (!addWorker(command, false))
            reject(command);
    }
```

**`ScheduledExecutorService`**
* 它在ExecutorService接口上再扩展
* 额外增加了定时、周期执行的能力。
```
public interface ScheduledExecutorService extends ExecutorService {
	
	// ========这个两个方法提交的任务只会执行一次========
	// 创建并执行启用的一次性操作在给定的延迟之后。
  public ScheduledFuture<?> schedule(Runnable command, long delay, TimeUnit unit);
  // 创建并执行启用的一次性操作在给定的延迟之后。
  public <V> ScheduledFuture<V> schedule(Callable<V> callable, long delay, TimeUnit unit);


	// ========这个两个方法提交的任务会周期性的执行多次========
	// 在给定的初始延迟之后，以给定的时间间隔执行周期性动作。
	// 即在 initialDelay 初始延迟后initialDelay + period 执行第一次
	// initialDelay + 2 * period  执行第二次，依次类推
	// 特点：下一次任务的执行并不管你上次任务是否执行完毕
	// 所以它名叫FixedRate：固定的频率执行（每次都是独立事件）
    public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,
                                                  long initialDelay,
                                                  long period,
                                                  TimeUnit unit);
	// 给定的初始延迟之后首先启用的定期动作
	// 随后**上一个执行的终止**和**下一个执行的开始之间**给定的延迟。
	// 也就是说delay表示的是上一次的end和下一次start之间的时间，两次之间是有关系的，不同于上个方法
	// 所以它名叫FixedDelay：两次执行间固定的延迟
    public ScheduledFuture<?> scheduleWithFixedDelay(Runnable command,
                                                     long initialDelay,
                                                     long delay,
                                                     TimeUnit unit);

}
```
* 定时执行和周期性执行是两码事
* 通过`ScheduledExecutorService#schedule()`方法调度执行的任务有且仅会执行一次 

**`ScheduledThreadPoolExecutor`**
* 在线程池里执行任务，并且还可以定时、周期性的执行
```
public class ScheduledThreadPoolExecutor extends ThreadPoolExecutor implements ScheduledExecutorService { ... }

```
**`Executors`**
通过命名可知它是用于创建Executor执行器的工具类（均为静态方法）：
* 可快捷创建带有线程池能力的执行器ThreadPoolExecutor（ExecutorService）
* 可快速创建具有线程池，且定时、周期执行任务能力的ScheduledThreadPoolExecutor（ScheduledExecutorService）


> 为什么不推荐用`executors`创建线程池

* 因为Executors 创建的线程池会 有OOM的风险: 缓存队列 LinkedBlockingQueue 没有设置固定容量大小
  *  `Executors.newCachedThreadPool()`: 最大线程数是Integer.MAX_VALUE, 并且任务队列是SynchronousQueue
  *  `Executors.newFixedThreadPool(1)`: 它定死了线程数量， 所以线程数量是不会超出的，但是它的任务队列是无界的LinkedBlockingQueue, 
  *  `Executors.newSingleThreadExecutor()`: 任务队列和上面一样， 没有限制， 很容易就使用不当导致OOM
  *  `Executors.newScheduledThreadPool(2)`: 没有定义线程创建数量的上线， 同时任务队列也没有定义上限

> submit execute 区别
* execute只能提交Runnable类型的任务，而submit既能提交Runnable类型任务也能提交Callable类型任务
* execute会直接抛出任务执行时的异常，submit会吃掉异常，可通过Future的get方法将任务执行时的异常重新抛出。
* execute所属顶层接口是Executor,submit所属顶层接口是ExecutorService
----
# 线程池
> 线程池顾名思义就是事先创建若干个可执行的线程放入一个池（容器）中，需要的时候从池中获取线程不用自行创建，使用完毕不需要销毁线程而是放回池中，从而减少创建和销毁线程对象的开销


* 避免了处理任务时创建销毁线程开销的代价
* 避免了线程数量膨胀导致的过分调度问题，保证了对内核的充分利用


线程池原理
* 线程池中的核心线程数，当提交一个任务时，线程池创建一个新线程执行任务，直到当前线程数等于corePoolSize；
* 如果当前线程数为 corePoolSize，继续提交的任务被保存到阻塞队列中，等待被执行；如果阻塞队列满了，那就创建新的线程执行当前任务；直到线程池中的线程数达到 maxPoolSize，这时再有任务来，只能执行 reject() 处理该任务。


```
线程池在内部实际上构建了一个生产者消费者模型，
将线程和任务两者解耦，并不直接关联，从而良好的缓冲任务，复用线程。
线程池的运行主要分成两部分：任务管理、线程管理。
任务管理部分充当生产者的角色，当任务提交后，线程池会判断该任务后续的流转：（1）直接申请线程执行该任务；（2）缓冲到队列中等待线程执行；（3）拒绝该任务。
线程管理部分是消费者，它们被统一维护在线程池内，根据任务请求进行线程的分配，当线程执行完任务后则会继续获取新的任务去执行，最终当线程获取不到任务的时候，线程就会被回收。
```

### 任务执行机制

线程池内部使用一个变量维护两个值：运行状态(runState)和线程数量 (workerCount)
```
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
```
* 对线程池的运行状态和线程池中有效线程的数量进行控制的一个字段
* 高3位保存runState，低29位保存workerCount

**线程池的状态**
* RUNNING：-1 << COUNT_BITS，即高 3 位为 111，该状态的线程池会接收新任务，并处理阻塞队列中的任务；
* SHUTDOWN： 0 << COUNT_BITS，即高 3 位为 000，该状态的线程池不会接收新任务，但会处理阻塞队列中的任务；
* STOP ： 1 << COUNT_BITS，即高 3 位为 001，该状态的线程不会接收新任务，也不会处理阻塞队列中的任务，而且会中断正在运行的任务；
* TIDYING ： 2 << COUNT_BITS，即高 3 位为 010，该状态表示线程池对线程进行整理优化；
* TERMINATED： 3 << COUNT_BITS，即高 3 位为 011，该状态表示线程池停止工作；

<img width="４54" alt="Screen Shot 2021-11-22 at 8 23 57 PM" src="https://user-images.githubusercontent.com/27160394/142861284-cab45e94-39b2-4a0c-b63b-4b9e5a11d83e.png">


#### 1.任务调度
> 当用户提交了一个任务，接下来这个任务将如何执行
1.所有任务的调度都是由`execute`方法完成的，这部分完成的工作是：检查现在线程池的运行状态、运行线程数、运行策略，决定接下来执行的流程，是直接申请线程执行，或是缓冲到队列中执行，亦或是直接拒绝该任务
  * 首先检测线程池运行状态，如果不是RUNNING，则直接拒绝，线程池要保证在RUNNING的状态下执行任务。
  * 如果workerCount < corePoolSize，则创建并启动一个线程来执行新提交的任务。
  * 如果workerCount >= corePoolSize，且线程池内的阻塞队列未满，则将任务添加到该阻塞队列中。
  * 如果workerCount >= corePoolSize && workerCount < maximumPoolSize，且线程池内的阻塞队列已满，则创建并启动一个线程来执行新提交的任务。
  * 如果workerCount >= maximumPoolSize，并且线程池内的阻塞队列已满, 则根据拒绝策略来处理该任务, 默认的处理方式是直接抛异常。

#### 2.任务缓冲
> 线程池中是以生产者消费者模式，通过一个阻塞队列来实现的任务缓冲模块

**阻塞队列(BlockingQueue)**
* 阻塞队列缓存任务，工作线程从阻塞队列中获取任务。
* 支持两个附加操作的队列。
  1.在队列为空时，获取元素的线程会等待队列变为非空
  2.当队列满时，存储元素的线程会等待队列可用
* 阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程


<img width="564" alt="Screen Shot 2021-11-22 at 8 35 02 PM" src="https://user-images.githubusercontent.com/27160394/142862703-f83e8adb-4d2f-45ce-90e3-f2182ed5170c.png">

#### 3.任务申请
任务的执行有两种可能
1. 是任务直接由新创建的线程执行。
2. 线程从任务队列中获取任务然后执行,执行完任务的空闲线程会再次去从队列中申请任务再去执行

线程需要从任务缓存模块中不断地取任务执行，帮助线程从阻塞队列中获取任务，
* 实现线程管理模块和任务管理模块之间的通信
* 这部分策略由getTask方法实现
* getTask这部分进行了多次判断，为的是控制线程的数量，使其符合线程池的状态

<img width="509" alt="Screen Shot 2021-11-22 at 8 41 21 PM" src="https://user-images.githubusercontent.com/27160394/142863618-3c82f4c9-197f-4ec7-bc7f-f70b2e9f3ec8.png">


#### 4. 任务拒绝

任务拒绝模块是线程池的保护部分，线程池有一个最大的容量，当线程池的任务缓存队列已满，并且线程池中的线程数目达到maximumPoolSize时，就需要拒绝掉该任务，采取任务拒绝策略

* AbortPolicy         -- 当任务添加到线程池中被拒绝时，它将抛出 RejectedExecutionException 异常。
* CallerRunsPolicy    -- 当任务添加到线程池中被拒绝时，会在线程池当前正在运行的Thread线程池中处理被拒绝的任务。
* DiscardOldestPolicy -- 当任务添加到线程池中被拒绝时，线程池会放弃等待队列中最旧的未处理任务，然后将被拒绝的任务添加到等待队列中。
* DiscardPolicy       -- 当任务添加到线程池中被拒绝时，线程池将丢弃被拒绝的任务


### Worker线程管理

```
private final class Worker extends AbstractQueuedSynchronizer implements Runnable{
    final Thread thread;//Worker持有的线程
    Runnable firstTask;//初始化的任务，可以为null
}
```
* thread是在调用构造方法时通过ThreadFactory来创建的线程，可以用来执行任务
* firstTask用它来保存传入的第一个任务，这个任务可以有也可以为null。如果这个值是非空的，那么线程就会在启动初期立即执行这个任务

<img width="466" alt="Screen Shot 2021-11-22 at 8 48 12 PM" src="https://user-images.githubusercontent.com/27160394/142864494-3354bbed-3494-4d14-8b24-256927150d75.png">

如何判断线程是否在运行？
× Worker是通过继承AQS，使用AQS来实现独占锁这个功能，为的就是实现不可重入的特性去反应线程现在的执行状态
1. lock方法一旦获取了独占锁，表示当前线程正在执行任务中
2. 如果正在执行任务，则不应该中断线程。 
3. 如果该线程现在不是独占锁的状态，也就是空闲的状态，说明它没有在处理任务，这时可以对该线程进行中断。 
4. 线程池在执行`shutdown`方法或`tryTerminate`方法时会调用`interruptIdleWorkers`方法来中断空闲的线程，`interruptIdleWorkers`方法会使用`tryLock`方法来判断线程池中的线程是否是空闲状态；如果线程是空闲状态则可以安全回收。

**Worker线程增加**
>增加线程是通过线程池中的`addWorker`方法，该方法的功能就是增加一个线程，该方法不考虑线程池是在哪个阶段增加的该线程

`addWorker`方法有两个参数
* `firstTask`参数用于指定新增的线程执行的第一个任务，该参数可以为空；
* `core`参数为true表示在新增线程时会判断当前活动线程数是否少于`corePoolSize`，false表示新增线程前需要判断当前活动线程数是否少于`maximumPoolSize`



**Worker线程回收**
线程池中线程的销毁依赖JVM自动的回收
* 线程池做的工作是根据当前线程池的状态维护一定数量的线程引用，防止这部分线程被JVM回收，
* 当线程池决定哪些线程需要回收时，只需要将其引用消除即可。
* Worker被创建出来后，就会不断地进行轮询，然后获取任务去执行
* 核心线程可以无限等待获取任务，非核心线程要限时获取任务。
* 当Worker无法获取到任务，也就是获取的任务为空时，循环会结束，Worker会主动消除自身在线程池内的引用。
```
try {
  while (task != null || (task = getTask()) != null) {
    //执行任务
  }
} finally {
  processWorkerExit(w, completedAbruptly);//获取不到任务时，主动回收自己
}
```
线程回收的工作是在processWorkerExit方法完成的。

**Worker线程执行任务**
在Worker类中的run方法调用了runWorker方法来执行任务
1.while循环不断地通过getTask()方法获取任务。 
2.getTask()方法从阻塞队列中取任务。 
3.如果线程池正在停止，那么要保证当前线程是中断状态，否则要保证当前线程不是中断状态。 
4.执行任务。 
5.如果getTask结果为null则跳出循环，执行processWorkerExit()方法，销毁线程。

----
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


> 线程池中线程数的设置多少合理

线程池构造参数有8个，但是最核心的是3个：corePoolSize、maximumPoolSize，workQueue，它们最大程度地决定了线程池的任务分配和线程分配策略

1. 并行执行子任务，提高响应速度。这种情况下，应该使用同步队列，没有什么任务应该被缓存下来，而是应该立即执行。
2. 并行执行大批次任务，提升吞吐量。这种情况下，应该使用有界队列，使用队列去缓冲大批量的任务，队列容量必须声明，防止任务无限制堆积

* 在Java线程池留有高扩展性的基础上，封装线程池，允许线程池监听同步外部的消息，根据消息进行修改配置
* 将线程池的配置放置在平台侧，允许开发同学简单的查看、修改线程池配置
* 在线程池执行任务的生命周期添加监控能力，帮助开发同学了解线程池状态

动态化线程池提供如下功能：
1. 动态调参：支持线程池参数动态调整、界面化操作；包括修改线程池核心大小、最大核心大小、队列长度等；参数修改后及时生效。 
2. 任务监控：支持应用粒度、线程池粒度、任务粒度的Transaction监控；可以看到线程池的任务执行情况、最大任务执行时间、平均任务执行时间、95/99线等。 
3. 负载告警：线程池队列任务积压到一定值的时候会通过大象（美团内部通讯工具）告知应用开发负责人；当线程池负载数达到一定阈值的时候会通过大象告知应用开发负责人。 
4. 操作监控：创建/修改和删除线程池都会通知到应用的开发负责人。 
5. 操作日志：可以查看线程池参数的修改记录，谁在什么时候修改了线程池参数、修改前的参数值是什么。 权限校验：只有应用开发负责人才能够修改应用的线程池参数。

-----
|任务类型|任务描述|数值运算|
|-------|------|-------|
|CPU密集型|比如加密、解密、压缩、计算等一系列需要大量耗费 CPU 资源的任务|最佳线程数 = CPU 核心数+1 - 2N|
|IO密集型|比如数据库、文件的读写，网络通信等任务，这种任务的特点是并不会特别消耗 CPU 资源，但是 IO 操作很耗时，总体会占用比较多的时间|线程数 = CPU 核心数 * (1+ IO 耗时/CPU 耗时)|
