JUC
 - `Package java.util.concurrent.locks`
    -  AQS
    	-  独占 -> ReentrantLock 
    	-  共享 -> CountDownLatch
    - ReadWriteLock
    	- ReentrantReadWriteLock
* 其它组件
-----
# AbstractQueuedSynchronizer - AQS
> AQS框架是J.U.C中实现锁及同步机制的基础,使用一个Volatile的int类型的成员变量来表示同步状态，通过内置的FIFO队列来完成资源获取的排队工作，通过CAS完成对State值的修改。
底层基本原理
* 它底层使用的是双向列表,是队列的一种实现,因此也可以将它当成一种队列。
* 如果当前线程获取同步状态失败，AQS会将该线程以及等待状态等信息构造成一个Node,将其加入同步队列的尾部,同时阻塞当前线程,当同步状态释放时,唤醒队列的头节点。


AbstractQueuedSynchronizer是一个抽象类，他采用模板方法的设计模式规定了独占和共享模式需要实现的方法
* 像ReentrantLock是基于独占模式模式实现的，
* CountDownLatch、CyclicBarrier等是基于共享模式

JUC中的应用场景
<img width="1527" alt="Screen Shot 2021-11-21 at 10 40 01 PM" src="https://user-images.githubusercontent.com/27160394/142766402-754c7811-74a5-40bd-8b46-9a5afc520727.png">


----
# AQS 实现

AQS其实就是一个可以给我们实现锁的框架，内部实现的关键是：先进先出的队列(CHL),(volatile)state 状态,

```
public abstract class AbstractQueuedSynchronizer extends AbstractOwnableSynchronizer implements java.io.Serializable {
    //头结点
    private transient volatile Node head;
    //尾节点
    private transient volatile Node tail;
    //共享状态
    private volatile int state;
    
    //内部类，构建链表的Node节点
    static final class Node {
        volatile Node prev;
        volatile Node next;
        volatile Thread thread;
    }
    
    protected final int getState() { //使用final修饰，限制子类不能对其重写
        return state;
    }
    protected final void setState(int newState) {
        state = newState;
    }
    protected final boolean compareAndSetState(int expect, int update) { //采用乐观锁思想的CAS算法，保证原子性操作。
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }
}

//AbstractQueuedSynchronizer的父类
public abstract class AbstractOwnableSynchronizer implements java.io.Serializable {
        //占用锁的线程
        private transient Thread exclusiveOwnerThread;
}
```

在AbstractQueuedSynchronizer的类里面有一个静态内部类Node。它代表的是队列中的每一个节点
```
static final class Node {

    //共享模式下的等待标记
    static final Node SHARED = new Node();
    //独占模式下的等待标记
    static final Node EXCLUSIVE = null;
    
    //表示当前节点的线程因为超时或者中断被取消
    static final int CANCELLED =  1;
    //表示当前节点的后续节点的线程需要运行，也就是通过unpark操作
    static final int SIGNAL    = -1;
    //表示当前节点在condition队列中
    static final int CONDITION = -2;
    //共享模式下起作用，表示后续的节点会传播唤醒的操作
    static final int PROPAGATE = -3;
    
    //状态，包括上面的四种状态值，初始值为0，一般是节点的初始状态
    volatile int waitStatus;
    //上一个节点的引用
    volatile Node prev;
    //下一个节点的引用
    volatile Node next;
    //保存在当前节点的线程引用
    volatile Thread thread;
    //condition队列的后续节点
    Node nextWaiter;
}
```

<img width="1211" alt="Screen Shot 2021-11-21 at 10 01 30 PM" src="https://user-images.githubusercontent.com/27160394/142764907-2a0f404a-52a0-490a-ad24-43d3da57e655.png">


## AQS下独占和共享的分析

### 独占模式

```
public final void acquire(int arg) {
    if (!tryAcquire(arg) && //tryAcquire()尝试直接去获取资源，如果成功则直接返回。
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg)) // 如果失败则调用addWaiter()方法把当前线程包装成Node(状态为EXCLUSIVE，标记为独占模式)插入到CLH队列末尾
                                         // 然后acquireQueued()方法使线程阻塞在等待队列中获取资源，一直获取到资源后才返回，如果在整个等待过程中被中断过，则返回true，否则返回false。
         selfInterrupt(); //线程在等待过程中被中断过，它是不响应的。只是获取资源后才再进行自我中断selfInterrupt()，将中断补上。
}


protected boolean tryAcquire(int arg) { //直接抛出异常，这是由子类进行实现的方法，体现了模板模式的思想
    throw new UnsupportedOperationException(); //公平锁有公平锁的获取方式，非公平锁有非公平锁的获取方式
}
```


### ReentrantLock
> 它是一个悲观锁,其次有两种模式分别是公平锁和非公平锁,最后它是重入锁,也就是能够对共享资源重复加锁。

<img width="1506" alt="Screen Shot 2021-11-21 at 10 09 09 PM" src="https://user-images.githubusercontent.com/27160394/142765145-7263845c-17a3-426c-9107-b402c5a1937e.png">


```
public class ReentrantLock implements Lock, java.io.Serializable {
  private static final long serialVersionUID = 7373984872572414699L;
  /** Synchronizer providing all implementation mechanics */
  private final Sync sync;

  /**
   * Base of synchronization control for this lock. Subclassed
   * into fair and nonfair versions below. Uses AQS state to
   * represent the number of holds on the lock.
   */
  abstract static class Sync extends AbstractQueuedSynchronizer { //Sync也是一个抽象类，其实现类为FairSync和NonfairSync，分别对应公平锁和非公平锁。
      ...
  }
  
  public ReentrantLock(boolean fair) { // 来确定使用公平锁还是非公平锁
        sync = fair ? new FairSync() : new NonfairSync();
     }

```

####  上锁
获取同步状态
1. `ReentrantLock`在初始化的时候`state=0`，表示资源未被锁定。当A线程执行`lock()`方法时，会调用`tryAcquire()`方法，将AQS中队列的模式设置为独占，并将独占线程设置为线程A，以及将`state+1`.
2. 这样在线程A没有释放锁前，线程B也来获取锁，调用`tryAcquire()`方法时都会失败，此时因为state=1，表示锁被占用.
3. 所以将B的线程信息和等待状态等信息构成出一个Node节点对象放入同步队列,head和tail分别指向队列的头部和尾部.
4. 同时阻塞线程B(这里的阻塞使用的是`LockSupport.park()`方法)。后续如果再有线程要获取锁，都会加入队列尾部并阻塞.

```
public void lock() {
    sync.lock();
}

```
lock方法先通过CAS尝试将同步状态(AQS的state属性)从0修改为1。若直接修改成功了，则将占用锁的线程设置为当前线程
```
static final class NonfairSync extends Sync {
      final void lock() {
                if (compareAndSetState(0, 1))
                    setExclusiveOwnerThread(Thread.currentThread());
                else
                    acquire(1);
         }
}
   
static final class FairSync extends Sync {
  ...  
      final void lock() {
        acquire(1);
      }
  ...
}
```

`tryAcquire`方法尝试获取锁,如果成功就返回,如果不成功,则把当前线程和等待状态信息构适成一个`Node`节点，并将结点放入同步队列的尾部。然后为同步队列中的当前节点循环等待获取锁，直到成功.

```
protected final boolean tryAcquire(int acquires) {
    //获取当前线程
    final Thread current = Thread.currentThread();
    //获取同步状态
    int c = getState();
    //判断同步状态是否为0
    if (c == 0) {
        //关键在这里，公平锁会判断是否需要排队
        if (!hasQueuedPredecessors() &&
            //如果不需要排队，则直接cas操作更新同步状态为1
            compareAndSetState(0, acquires)) {
            //设置占用锁的线程为当前线程
            setExclusiveOwnerThread(current);
            //返回true，表示上锁成功
            return true;
        }
    }
    //判断当前线程是否是拥有锁的线程，主要是可重入锁的逻辑
    else if (current == getExclusiveOwnerThread()) {
        //如果是当前线程，则同步状态+1
        int nextc = c + acquires;
        if (nextc < 0)
            throw new Error("Maximum lock count exceeded");
        //设置同步状态
        setState(nextc);
        return true;
    }
    //以上情况都不是，则返回false，表示上锁失败。上锁失败根据AQS的框架设计，会入队排队
    return false;
}

```

* 关键的区别就在于尝试获取锁的时候，公平锁会判断是否需要排队再去更新同步状态，非公平锁是直接就更新同步，不判断是否需要排队。
* 从性能上来说，公平锁的性能是比非公平锁要差的，因为公平锁要遵守FIFO(先进先出)的原则，这就会增加了上下文切换与等待线程的状态变换时间


### 线程加入等待队列
1. 生成NODE, 加入到尾部
```
private Node addWaiter(Node mode) {
    Node node = new Node(Thread.currentThread(), mode);
    // Try the fast path of enq; backup to full enq on failure
    Node pred = tail;
    if (pred != null) {
      node.prev = pred;
      if (compareAndSetTail(pred, node)) {
        pred.next = node;
        return node;
      }
    }
    enq(node);
    return node;
}
private final boolean compareAndSetTail(Node expect, Node update) {
	    return unsafe.compareAndSwapObject(this, tailOffset, expect, update);
}
```
2.出队列:判断前节点是头节点，不是话，是否阻塞(`lockSupport.park(this)`)
```
final boolean acquireQueued(final Node node, int arg) {
	// 标记是否成功拿到资源
	boolean failed = true;
	try {
		// 标记等待过程中是否中断过
		boolean interrupted = false;
		// 开始自旋，要么获取锁，要么中断
		for (;;) {
			// 获取当前节点的前驱节点
			final Node p = node.predecessor();
			// 如果p是头结点，说明当前节点在真实数据队列的首部，就尝试获取锁（别忘了头结点是虚节点）
			if (p == head && tryAcquire(arg)) {
				// 获取锁成功，头指针移动到当前node
				setHead(node);
				p.next = null; // help GC
				failed = false;
				return interrupted;
			}
			// 说明p为头节点且当前没有获取到锁（可能是非公平锁被抢占了）或者是p不为头结点，这个时候就要判断当前node是否要被阻塞（被阻塞条件：前驱节点的waitStatus为-1），防止无限循环浪费资源。具体两个方法下面细细分析
			if (shouldParkAfterFailedAcquire(p, node) && parkAndCheckInterrupt())
				interrupted = true;
		}
	} finally {
		if (failed)
			cancelAcquire(node);
	}
}
```

### CANCELLED状态节点生成
通过cancelAcquire方法，将Node的状态标记为CANCELLED
```
private void cancelAcquire(Node node) {
  // 将无效节点过滤
	if (node == null)
		return;
  // 设置该节点不关联任何线程，也就是虚节点
	node.thread = null;
	Node pred = node.prev;
  // 通过前驱节点，跳过取消状态的node
	while (pred.waitStatus > 0)
		node.prev = pred = pred.prev;
  // 获取过滤后的前驱节点的后继节点
	Node predNext = pred.next;
  // 把当前node的状态设置为CANCELLED
	node.waitStatus = Node.CANCELLED;
  // 如果当前节点是尾节点，将从后往前的第一个非取消状态的节点设置为尾节点
  // 更新失败的话，则进入else，如果更新成功，将tail的后继节点设置为null
	if (node == tail && compareAndSetTail(node, pred)) {
		compareAndSetNext(pred, predNext, null);
	} else {
		int ws;
    // 如果当前节点不是head的后继节点，1:判断当前节点前驱节点的是否为SIGNAL，2:如果不是，则把前驱节点设置为SINGAL看是否成功
    // 如果1和2中有一个为true，再判断当前节点的线程是否为null
    // 如果上述条件都满足，把当前节点的前驱节点的后继指针指向当前节点的后继节点
		if (pred != head && ((ws = pred.waitStatus) == Node.SIGNAL || (ws <= 0 && compareAndSetWaitStatus(pred, ws, Node.SIGNAL))) && pred.thread != null) {
			Node next = node.next;
			if (next != null && next.waitStatus <= 0)
				compareAndSetNext(pred, predNext, next);
		} else {
      // 如果当前节点是head的后继节点，或者上述条件不满足，那就唤醒当前节点的后继节点
			unparkSuccessor(node);
		}
		node.next = node; // help GC
	}
}
```




### 释放锁
释放同步状态
1. 当线程A调用执行`unlock()`方法将state=0后，A会唤醒头节点的后继节点(`LockSupport.unpark(B)`)
2. 即B线程从`LockSupport.park()`方法返回，此时B发现state已经为0，所以B线程可以顺利获取锁，B获取锁后B的Node节点随之出队

```
public void unlock() {
    //调用AQS中的release()方法
    sync.release(1);
}
//这是AQS框架定义的release()方法
public final boolean release(int arg) {
    //当前锁是不是没有被线程持有,返回true表示该锁没有被任何线程持有
    if (tryRelease(arg)) {
        //获取头结点h
        Node h = head;
        //判断头结点是否为null并且waitStatus不是初始化节点状态，解除线程挂起状态
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}
```
关键在于tryRelease()，这就不需要分公平锁和非公平锁的情况，只需要考虑可重入的逻辑。
```
protected final boolean tryRelease(int releases) {
    //减少可重入的次数
    int c = getState() - releases;
    //如果当前线程不是持有锁的线程，抛出异常
    if (Thread.currentThread() != getExclusiveOwnerThread())
        throw new IllegalMonitorStateException();
    boolean free = false;
    // 如果持有线程全部释放，将当前独占锁所有线程设置为null，并更新state
    if (c == 0) {
        //状态为0，表示持有线程被全部释放，设置为true
        free = true;
        setExclusiveOwnerThread(null);
    }
    setState(c);
    return free;
}
```


### 可重入
```
// java.util.concurrent.locks.ReentrantLock.FairSync#tryAcquire

if (c == 0) {
	if (!hasQueuedPredecessors() && compareAndSetState(0, acquires)) { // if (compareAndSetState(0, acquires)){} is unfair
		setExclusiveOwnerThread(current);
		return true;
	}
}
else if (current == getExclusiveOwnerThread()) {
	int nextc = c + acquires;
	if (nextc < 0)
		throw new Error("Maximum lock count exceeded");
	setState(nextc);
	return true;
}
```

------
## 共享模式
> 共享资源可以被多个线程同时占有，直到共享资源被占用完毕

CounCountDownLatch
1. 会将任务分成N个子线程去执行,state的初始值也是N(state与子线程数量一致).
2. N个子线程是并行执行的，每个子线程执行完成后countDown()一次，state会通过CAS方式减1。
2. N个子线程是并行执行的，每个子线程执行完成后countDown()一次，state会通过CAS方式减1。
3. 直到所有子线程执行完成后(state=0),会通过`unpark()`方法唤醒主线程，然后主线程就会从await()方法返回，继续后续操作



1. 求锁
```
public final void acquireShared(int arg) {
    if (tryAcquireShared(arg) < 0) // 当返回值大于等于0的时候，说明获得成功获取锁,tryAcquireShared()方法是一个模板方法由子类去重写
        doAcquireShared(arg); //基本上跟独占式的逻辑差不多,不同的地方在于入队的Node是标志为SHARED共享式的，获取同步资源的方式是tryAcquireShared()方法
```

2.出队列，入队的Node是标志为SHARED共享式
```
private void doAcquireShared(int arg) {
    //调用addWaiter()方法，把当前线程包装成Node，标志为共享式，插入到队列中
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            //获取当前节点node的前驱节点
            final Node p = node.predecessor();
            //前驱节点是否是头结点
            if (p == head) {
                //如果前驱节点是头结点，则调用tryAcquireShared()获取同步资源
                int r = tryAcquireShared(arg);
                //r>=0表示获取同步资源成功，只有获取成功，才会执行到return退出for循环
                if (r >= 0) {
                    //设置node为头结点
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    if (interrupted)
                        selfInterrupt();
                    failed = false;
                    return;
                }
            }
            //判断是否可以被park，跟独占式的逻辑一样返回true，则进行park操作，阻塞线程
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```


----


