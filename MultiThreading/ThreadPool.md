

# 线程池
> 线程池顾名思义就是事先创建若干个可执行的线程放入一个池（容器）中，需要的时候从池中获取线程不用自行创建，使用完毕不需要销毁线程而是放回池中，从而减少创建和销毁线程对象的开销

<img width="273" alt="Screen Shot 2021-11-17 at 11 27 41 PM" src="https://user-images.githubusercontent.com/27160394/142229716-c4e527eb-80bd-47a2-91e9-830cd310059a.png">


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

### 线程池的状态
* RUNNING：-1 << COUNT_BITS，即高 3 位为 111，该状态的线程池会接收新任务，并处理阻塞队列中的任务；
* SHUTDOWN： 0 << COUNT_BITS，即高 3 位为 000，该状态的线程池不会接收新任务，但会处理阻塞队列中的任务；
* STOP ： 1 << COUNT_BITS，即高 3 位为 001，该状态的线程不会接收新任务，也不会处理阻塞队列中的任务，而且会中断正在运行的任务；
* TIDYING ： 2 << COUNT_BITS，即高 3 位为 010，该状态表示线程池对线程进行整理优化；
* TERMINATED： 3 << COUNT_BITS，即高 3 位为 011，该状态表示线程池停止工作；