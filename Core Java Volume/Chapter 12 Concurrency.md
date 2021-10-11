# Catalog

1. What Are Threads?
2. Thread States
3. Thread Properties
4. Synchronization
5. Thread-Safe Collections 
6. Tasks and Thread Pools
7. Asynchronous Computations
8. Processes

> *Java Concurrency in Practice by Brian Goetz et al. (Addison-Wesley Professional, 2006).*
---
## 1. What Are Threads?
what is the difference between multiple processes and multiple threads? 
* The essential difference is that while each process has a complete set of its own variables, threads share the same data.

1. Place the code for the task into the run method of a class，Since `Runnable` is a functional interface, you can make an instance with a lambda expression:
```
public interface Runnable
       {
void run(); }

Runnable r = () -> { task code };
```
2. Construct a `thread` from Runnable
```
var t = new Thread(r);
```
3. Startthethread:
```
t.start();
```

> Do not call the `run` method of the Thread class or the Runnable object
> Calling the `run` method directly merely executes the task in the same thread, no new thread is started. 
> Instead, call the `Thread.start` method. It creates a new thread that executes the run method.
---
## 12.2 Thread States
Threads can be in one of six states: 
1. New
2. Runnable 
3. Blocked 
4. Waiting 
5. Timed waiting 
6. Terminated

### 12.2.1 New Threads
When you create a thread with the `new` operator. the thread is not yet running. This means that it is in the new state.
### 12.2.2 Runnable Threads
Once you invoke the `start` method, the thread is in the runnable state.
* A runnable thread may or may not actually be running. It is up to the operating system to give the thread time to run. 
* Once a thread is running, it doesn’t necessarily keep running. In fact, it is desirable that running threads occasionally pause so that other threads have a chance to run

### 12.2.3 Blocked and Waiting Threads
> When a thread is blocked or waiting, it is temporarily inactive. It doesn’t execute any code and consumes minimal resources. 

|Status of thread | unlock it|
|-----------------|----------|
|When the thread tries to acquire an intrinsic object lock (not`java.util.concurrent`) that is currently held by another thread, it becomes blocked|The thread becomes unblocked when all other threads have relinquished the lock and the thread scheduler has allowed this thread to hold it.|
|When the thread waits for another thread to notify the scheduler of a condition, it enters the waiting state.|This happens by calling the `Object.wait` or `Thread.join` method, or by waiting for a Lock or Condition in the `java.util.concurrent` library|
|Calling timeout method causes the thread to enter the `timed waiting` state.| This state persists either until the timeout expires or the appropriate notification 

<img width="440" alt="Screen Shot 2021-10-10 at 2 54 41 PM" src="https://user-images.githubusercontent.com/27160394/136685923-bf9669b3-78e7-4250-ac27-03799a5e0c7d.png">

Transitions from one state to another
* When a thread is blocked or waiting (or, of course, when it terminates), another thread will be scheduled to run. 
* When a thread is reactivated, the scheduler checks to see if it has a higher priority than the currently running threads

### 12.2.4 Terminated Threads
A thread is terminated for one of two reasons:
1. It dies a natural death because the run method exits normally.
2. It dies abruptly because an uncaught exception terminates the run method.
---
## 12.3 Thread Properties
### 12.3.1 Interrupting Threads
A thread terminates when its `run` method returns, or if an exception occurs that is not caught in the method.
*  there is no way to force a thread to terminate. However, the `interrupt` method can be used to request termination of a thread.
*  When the `interrupt` method is called on a thread, the interrupted status of the thread is set.
```
while (!Thread.currentThread().isInterrupted() && more work to do) {

       do more work

}

```
* This is where the `InterruptedException` comes in. When the interrupt method is called on a thread that blocks on a call such as `sleep`or `wait`.

`interrupted` and `isInterrupted.`

* The `interrupted` method is a static method that checks whether the current thread has been interrupted. 
       * Furthermore, calling the `interrupted` method clears the interrupted status of the thread. 
* The `isInterrupted` method is an instance method that you can use to check whether any thread has been interrupted.

### 12.3.2 Daemon Threads
` t.setDaemon(true)` :  turn a thread into a `daemon` thread by calling
* This method must be called before the thread is started.
* daemon threads are low-priority threads whose only role is to provide services to user thread

### 12.3.4 Handlers for Uncaught Exceptions
The `run` method of a thread cannot throw any checked exceptions, but it can be terminated by an unchecked exception. 
* before the thread dies, the exception is passed to a handler (`Thread.UncaughtExceptionHandler` interface)for uncaught exceptions.
* You can install a handler into any thread with the `setUncaughtExceptionHandler` method. 
### 12.3.5 Thread Priorities
You can increase or decrease the priority of any thread with the `setPriority` method. 
* You can set the priority to any value between MIN_PRIORITY (defined as 1 in the Thread class) 
* MAX_PRIORITY (defined as 10). 
* NORM_PRIORITY is defined as 5.

Thread priorities are highly system- dependent. 
* Windows has seven priority levels. Some of the Java priorities will map to the same operating system level. 
* In the Oracle JVM for Linux, thread priorities are ignored altogether—all threads have the same priority.
---
## 12.4 Synchronization

*Race condition* occurs when two or more threads attempt to update mutable shared data at the same time

### 12.4.2 The Race Condition Explained
To avoid corruption of shared data by multiple threads, you must learn how to *synchronize* the *access*
 *  Race condition occurs due to not atomic operations.
 *  If we could ensure that the method runs to completion before the thread loses control, the state of the object would never be corrupted.
 
### 12.4.3 Lock Objects
There are two mechanisms for protecting a code block from concurrent access. 
1. The Java language provides a `synchronized` keyword for this purpose, 
2. Java 5 introduced the `ReentrantLock` class.

```
public class Counter{

  private int count = 0;

  public int inc(){
    synchronized(this){
      return ++count;
    }
  }
}

public class Counter{

  private Lock lock = new Lock();
  private int count = 0;

  public int inc(){
    lock.lock();
    int newCount = ++count;
    lock.unlock();
    return newCount;
  }
}
```

**`synchronized`**
> Automatically provides a lock with condition, which become a better choice for explicit locking.

**`ReentrantLock`**
```
myLock.lock(); //This construct guarantees that only one thread at a time can enter the critical section.
try
{ critical section}
finally //unlock operation is enclosed in a finally clause. If the code in the critical section throws an exception, the lock must be unlocke
{  myLock.unlock(); }

```
When you use locks, you cannot use the `try-with-resources` statement. 
1. the unlock method isn’t called `close` method
2. when you use a lock, you want to keep using the same variable that is shared among threads.

`reentrance`  mechanism : 
* If a thread already holds the lock on a monitor object, it has access to all blocks synchronized on the same monitor object. 
* The thread can reenter any block of code for which it already holds the lock.
* Synchronized blocks in Java are reentrant. 


`reentrant` lock 
* A reentrant lock is one where a process can claim the lock multiple times without blocking on itself
* a thread can repeatedly acquire a lock that it already owns. 
* The lock has a hold count that keeps track of the nested calls to the lock method.
* The thread has to call unlock for every call to lock in order to relinquish the lock
* Code protected by a lock can call another method that uses the same locks.
*  protect blocks of code that thread safety before  another thread can use the same object

fair lock
*  are a lot slower than regular locks
*  Only enable fair locking if you truly know what you are doing and have a specific reason to consider fairness essential for your program
*  Have no guarantee that the thread scheduler is fair.

### 12.4.4 Condition Objects
> Use a `condition` object to manage threads that have acquired a lock but cannot do useful work.

```
We wait until some other thread has added funds. But this thread has just gained exclusive access to the bankLock, so no other thread has a chance to make a deposit. This is where condition objects come in.
````

There is an essential difference between a thread that is waiting to acquire a lock and a thread that has called `await`
* `thread.await()` -> a wait set for that condition and stay deactivated -> another thread  call `signalAll` method on the same condition.
* No guarantee that the condition is now fulfilled. the `signalAll` method just ask waiting thread attempt to check for the condition again.
* `signalAll` does not immediately activate a waiting thread. It only unblocks the waiting threads.
* When a thread calls `await`, it has no way of reactivating itself. It puts its faith in the other threads
*  `dead lock`: If none of them bother to reactivate the waiting thread, it will never run again.
*  to call `signalAll` whenever the state of an object changes in a way that might be advantageous to waiting threads.
*  `signal`, unblocks only a single thread from the wait set, chosen at random

### 12.4.5 The synchronized Keyword

A thread can only call `await`, `signalAll`, or `signal `on a condition if it owns the lock of the condition.

### 12.4.5 The synchronized Keyword
> a lock mechanism that is built into the Java language.

>If you write a variable which may next be read by another thread, or you read a variable which may have last been written by another thread, you must use synchronization.”

If a method  declared with  `synchronized`keyword, the object’s `lock` protects the entire method == a thread must acquire the intrinsic object lock.
```
public synchronized void method()

public void method() {
this.intrinsicLock.lock(); try
{
method body
}
finally { this.intrinsicLock.unlock(); } }
```
*intrinsic lock*

* Each object has an `intrinsic` lock,and that the lock has an intrinsic condition. 
* The lock manages the threads that try to enter a `synchronized` method. 
* The `condition` manages the threads that have called `wait`.
* The `wait` method adds a thread to the wait set, and the `notifyAll/notify` methods unblock waiting threads
*  A thread can acquire the object's lock by calling a `synchronized` method

The intrinsic locks and conditions have some limitation
* You cannot interrupt a thread that is trying to acquire a lock.
* You cannot specify a timeout when trying to acquire a lock. 
* Having a single condition per lock can be inefficient.

Note:
* It is best to use neither `Lock/Condition` nor the `synchronized` keyword. In many situations, you can use one of the mechanisms of the `java.util.concurrent` package that do all the locking for you.
* If the `synchronized` keyword works for your situation, by all means, use it
* Use `Lock/Condition` if you really need the additional power that these constructs give you.

### 12.4.6 Synchronized Blocks
> Locked pieces of code instead of whole method
```
synchronized (obj) // this is the syntax for a synchronized block
   {
      critical section
}
```
Synchronized blocks are compiled into a lengthy sequence of bytecodes to manage the intrinsic lock.

### 12.4.7 The Monitor Concept
> looked for ways to make multithreading safe without forcing think about explicit locks.

A monitor has these properties:
* A monitor is a class with only private fields.
* Each object of that class has an associated lock.
* All methods are locked by that lock (lock at beginning of the method call and relinquished when the method returns.)
* The lock can have any number of associated conditions.

Loosely adapted the monitor concept.
* Every object in Java has an intrinsic lock and an intrinsic condition. 
* If a method is declared with the `synchronized` keyword, it acts like a monitor method. 
* The condition variable is accessed by calling `wait/notifyAll/notify.`

 a Java object differs from a monitor in three important way
 * Fields are not required to be `private.`
 * Methods are not required to be `synchronized.`
 * The intrinsic lock is available to clients.

### 12.4.8 Volatile Fields
