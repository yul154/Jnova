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
## 12.1 What Are Threads?
Individual programs will appear to do multiple tasks at the same time. Each task is executed in a thread, 

what is the difference between multiple processes and multiple threads? 
* The essential difference is that while each process has a complete set of its own variables, threads share the same data.

Interruption is used to request that a thread terminates. Accordingly, our run method exits when an `InterruptedException` occurs.

**Create a thread program**
1.Since `Runnable` is a functional interface, you can make an instance with a lambda expression:
```
public interface Runnable
       {
void run(); }

Runnable r = () -> { 
    try
       {
              for (int i = 0; i < STEPS; i++) {
              double amount = MAX_AMOUNT * Math.random(); 
              bank.transfer(0, 1, amount);
              Thread.sleep((int) (DELAY * Math.random()));
       } 
    }
    catch (InterruptedException e)
      {
      } 
 };
```
2. Construct a `thread` from Runnable
```
var t = new Thread(r);
```
3. Startthethread:
```
t.start();
```

*  Do not call the `run` method of the Thread class or the Runnable object, Calling the `run` method directly merely executes the task in the same thread, no new thread is started. 
*  Instead, call the `Thread.start` method. It creates a new thread that executes the run method.
---

## 12.2 Thread States
Threads can be in one of six states: 
1. New
2. Runnable 
3. Blocked 
4. Waiting 
5. Timed waiting 
6. Terminated

* To determine the current state of a thread, simply call the `getState` method.

### 12.2.1 New Threads
When you create a thread with the `new` operator. the thread is not yet running. This means that it is in the new state.
### 12.2.2 Runnable Threads
Once you invoke the `start` method, the thread is in the runnable state.
* A runnable thread may or may not actually be running. It is up to the operating system to give the thread time to run. 
* Once a thread is running, it doesn’t necessarily keep running. In fact, it is desirable that running threads occasionally pause so that other threads have a chance to run
*  `yield()`: causes the currently executing thread to yield to another thread. Note that this is a static method.
### 12.2.3 Blocked and Waiting Threads
> When a thread is blocked or waiting, it is temporarily inactive. It doesn’t execute any code and consumes minimal resources. 

|Status of thread | unlock it|
|-----------------|----------|
|When the thread tries to acquire an intrinsic object lock (not`java.util.concurrent`) that is currently held by another thread, it becomes blocked|The thread becomes unblocked when all other threads have relinquished the lock and the thread scheduler has allowed this thread to hold it.|
|When the thread waits for another thread to notify the scheduler of a condition, it enters the *waiting* state.|This happens by calling the `Object.wait` or `Thread.join` method, or by waiting for a Lock or Condition in the `java.util.concurrent` library|
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
*  When the `interrupt` method is called on a thread, the interrupted status of the thread is set for object.
```
while (!Thread.currentThread().isInterrupted() && more work to do) { //Thread.currentThread method to get the current thread

       do more work
}

```
* If a thread is blocked, it cannot check the interrupted status. This is where the `InterruptedException` comes in
* When the interrupt method is called on a thread that blocks on a call such as `sleep`or `wait`. the blocking call is terminated by an `InterruptedException.`
* If you call the `sleep` method when the interrupted status is set, it doesn’t sleep. Instead, it clears the status (!) and throws an `InterruptedException`

`interrupted` and `isInterrupted.`
* The `interrupted` method is a static method that checks whether the current thread has been interrupted. 
       * Furthermore, calling the `interrupted` method clears the interrupted status of the thread. 
* The `isInterrupted` method is an instance method that you can use to check whether any thread has been interrupted.

### 12.3.2 Daemon Threads
> `t.setDaemon(true)` :  turn a thread into a `daemon` thread by calling
A daemon is simply a thread that mainly serve User thread。
* This method must be called before the thread is started.
* daemon threads are low-priority threads whose only role is to provide services to user thread

### 12.3.4 Handlers for Uncaught Exceptions
> The `run` method of a thread cannot throw any checked exceptions, but it can be terminated by an unchecked exception. 

`UncaughtExceptionHandler`
* before the thread dies, the exception is passed to a handler (`Thread.UncaughtExceptionHandler` interface)for uncaught exceptions.
* You can install a handler into any thread with the `setUncaughtExceptionHandler` method. 
* Install a default handler for all threads with the static method `setDefaultUncaughtExceptionHandler` of the Thread clas

### 12.3.5 Thread Priorities
> Every thread has a priority
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
This problem occurs when two threads are simultaneously trying to update a variable.

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
* Protect blocks of code that thread safety before another thread can use the same object

fair lock
*  are a lot slower than regular locks
*  Only enable fair locking if you truly know what you are doing and have a specific reason to consider fairness essential for your program
*  Have no guarantee that the thread scheduler is fair.

### 12.4.4 Condition Objects
> Use a `condition` object to manage threads that have acquired a lock but cannot do useful work.

```
We wait until some other thread has added funds. But this thread has just gained exclusive access to the bankLock, so no other thread has a chance to make a deposit. This is where condition objects come in.
````

Thread that has called `await`
1. `thread.await()` -> enter a wait set for that condition
2. Stay deactivated until another thread  call `signalAll` method on the same condition. 
     * The thread is not made runnable when the lock is available(thread that is waiting to acquire a lock will do)
     * it has no way of reactivating itself. It puts its faith in the other threads (difference between a thread that is waiting to acquire a lock)
3. `signalAll` call reactivates all threads waiting for the condition
4. When the threads are removed from the wait set, They are again runnable and the scheduler will eventually activate them again
5. No guarantee that the condition is now fulfilled. the `signalAll` method just ask waiting thread attempt to check for the condition again.

**dead lock**
1. When a thread calls await, it has no way of reactivating itself. 
2. It puts its faith in the other threads. If none of them bother to reactivate the waiting thread, 
3. it will never run again. This can lead to unpleasant deadlock situations. 
4. If all other threads are blocked and the last active thread calls await without unblocking one of the others, it also blocks

*  to call `signalAll` whenever the state of an object changes in a way that might be advantageous to waiting threads.
*  `signalAll` does not immediately activate a waiting thread. It only unblocks the waiting threads so that they can compete for entry into the object after the current thread has relinquished the lock.
*  `signal`, unblocks only a single thread from the wait set, chosen at random
```
public void transfer(int from, int to, int amount)
   {
       bankLock.lock(); 
       try
       {
              while (accounts[from] < amount) 
                     sufficientFunds.await(); // sufficientFunds = bankLock.newCondition();
                     // transfer funds
              ... 
              sufficientFunds.signalAll();
       } 
       finally {
              bankLock.unlock(); 
       }
}
```

### 12.4.5 The synchronized Keyword
> a lock mechanism that is built into the Java language.

> If you write a variable which may next be read by another thread, or you read a variable which may have last been written by another thread, you must use synchronization.


Every object in Java has an intrinsic lock., If a method declared with  `synchronized`keyword, the object’s `lock` protects the entire method == a thread must acquire the intrinsic object lock.
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
*
A Java object differs from a monitor in three important way
 * Fields are not required to be `private.`
 * Methods are not required to be `synchronized.`
 * The intrinsic lock is available to clients.

### 12.4.8 Volatile Fields
> The `volatile` keyword offers a lock-free mechanism for synchronizing access to an instance field.  
 
**Variable Visibility Problems**
* Computers with multiple processors can temporarily hold memory values in registers or local memory caches. As a consequence, threads running in different processors may see different values for the same memory location!
* The problem with threads not seeing the latest value of a variable because it has not yet been written back to main memory by another thread, is called a *visibility" problem*. The updates of one thread are not visible to other threads.
* Compilers can reorder instructions for maximum throughput. Compilers won’t choose an ordering that changes the meaning of the code, but they make the assumption that memory values are only changed when there are explicit instructions in the code. However, a memory value can be changed by another thread!

If declared field as `volatile`: 
* `volatile` variable will be written back to main memory immediately.
*  Also, all reads of the counter variable will be read directly from main memory.
```
private volatile boolean done;
public boolean isDone() { return done; } 
public void setDone() { done = true; }
```
Before inserting `volatile`: The isDone and setDone methods can block if another thread has locked `done`.
After: The compiler  ensure that a change to the `done` variable in one thread is *visible* from any other thread that reads the variable.

But, Volatile variables do not provide any atomicity.
*  Need to use a `synchronized` to guarantee that the reading and writing of the variable is atomic
*  Also use one of the many atomic data types found in the `java.util.concurrent package`.

### 12.4.9 Final Variables
`final`:  one other situation in which it is safe to access a shared field

### 12.4.10 Atomics
There are a number of classes in the `java.util.concurrent.atomic` package that use efficient machine-level instructions to guarantee atomicity of other operations without using locks. 
* `AtomicInteger` class has methods `incrementAndGet` and `decrementAndGet`
* The `LongAdder` and `LongAccumulator` can solve the problem that a very large number of threads accessing the same atomic values

### 12.4.11 Deadlocks
> A deadlock is when two or more threads are blocked forever, waiting for each other. 

Deadlock can occur 
* It is possible that all threads get blocked because each is waiting for impossible situation.
* It is possible that in ith thread, the method is not making progress.
* Call to `signal`. It only unblocks one thread, and it may not pick the thread that is essential to make progress

### 12.4.12 Thread-Local Variables
>  Avoid sharing by giving each thread its own instance, using the `ThreadLocal` helper class
```
public static final ThreadLocal<SimpleDateFormat> dateFormat = ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-
dd"));

String dateStamp = dateFormat.get().format(new Date());

```
* the `get` method returns the instance belonging to the current thread. Avoid shared instane can be corrupted by concurrent access.
*  the ThreadLocal helper also give each thread a separate instance which is thread-safe. 

### 12.4.13 Why the `stop` and `suspend` Methods Are Deprecated
* `stop` method that simply terminates a thread, 
* `suspend` method that blocks a thread until another thread calls resume.
Both attempt to control the behavior of a given thread without the thread’s cooperation.

* `stop` This can leave objects in an inconsistent state.
* if you suspend a thread that owns a lock, then the lock is unavailable until the thread is resumed. If the thread that calls the suspend method tries to acquire the same lock. the program deadlocks:

---
## 12.5 Thread-Safe Collections

### 12.5.1 Blocking Queues
> `java.util.concurrent Interface BlockingQueue<E>`
> 
A blocking queue causes a thread to block when you try to add an element when the queue is currently full or to remove an element when the queue is empty.

* The `poll` and `peek` methods return null to indicate failure. Therefore, it is illegal to insert null values into these queues.
* The `put` method blocks if the queue is full, and the `take` method blocks if the queue is empty

`DelayQueue()` : Only elements whose delay has expired can be removed from the queue.

### 12.5.2 Efficient Maps, Sets, and Queues
> `ConcurrentHashMap`, `ConcurrentSkipListMap`, `ConcurrentSkipListSet`, and `ConcurrentLinkedQueue`.
* The concurrent hash map can efficiently support a large number of readers and a fixed number of writers.
* The collections return weakly consistent iterator That means that the iterators may or may not reflect all modifications that are made after they were constructed

### 12.5.3 Atomic Update of Map Entries

This is not Atomic Update
```
Long oldValue = map.get(word);
Long newValue = oldValue == null ? 1 : oldValue + 1; 
map.put(word, newValue); // ERROR--might not replace oldValue
```
1. In old versions of Java, it was necessary to use the replace method,
```
do {
   oldValue = map.get(word);
   newValue = oldValue == null ? 1 : oldValue + 1; 
 }
while (!map.replace(word, oldValue, newValue));
```
2. Use a `ConcurrentHashMap<String,AtomicLong>`,You cannot have null values in a ConcurrentHashMap.
```
map.putIfAbsent(word, new AtomicLong()); 
map.get(word).incrementAndGet();

```
3. `compute` method: receives the key and the associated value, or null if there is none, and it computes the new value. 
```
map.compute(word, (k, v) -> v == null ? 1 : v + 1);
map.computeIfAbsent(word, k -> new LongAdder()).increment(); //. A map of LongAdder counters can be updated with
```
4. `merge` method :It has a parameter for the initial value that is used when the key is not yet present
```
map.merge(word, 1L, (existingValue, newValue) -> existingValue + newValue);
map.merge(word, 1L, Long::sum);
```
Difference between `merge` and `compute`
* `compute`: attempts to compute a mapping for the specified key and its current mapped value (or null if there is no current mapping)
* `merge`: If the specified key is not already associated with a value or is associated with null, associates it with the given non-null value. Otherwise, replaces the associated value with the results of the given remapping function, 
###  12.5.4 Bulk Operations on Concurrent Hash Maps
There are three kinds of operations:
* `search` applies a function to each key and/or value, until the function yields a non-null result. Then the search terminates and the function’s result is returned.
* `reduce` combines all keys and/or values, using a provided accumulation function.
* `forEach` applies a function to all keys and/or values.

With each of the operations, you need to specify a parallelism threshold. If the map contains more elements than the threshold, the bulk operation is parallelized

### 12.5.5 Concurrent Set Views
The static `newKeySet` method yields a `Set<K>` that is actually a wrapper around a `ConcurrentHashMap<K, Boolean>`.
```
Set<String> words = ConcurrentHashMap.<String>newKeySet();

Set<String> words = map.keySet(1L); //with a default value
words.add("Java");
```
### 12.5.6 Copy on Write Arrays

The `CopyOnWriteArrayList` and `CopyOnWriteArraySet` are thread-safe collections in which all mutators make a copy of the underlying array.

### 12.5.7 Parallel Array Algorithms
* `Arrays.parallelSort` method can sort an array of primitive values or objects.
* The `parallelSetAll` method fills an array with values that are computed from a function. 
* `parallelPrefix` method that replaces each array element with the accumulation of the prefix for a given associative operation.

---
## 12.6 Tasks and Thread Pools

A thread pool contains a number of short-lived threads that are ready to run.
 * give a `Runnable` to the pool, and one of the threads calls the `run` method.
 * When the `run` method exits, the thread doesn’t die but stays around to serve the next request.

### 12.6.1 Callables and Futures

A `Runnable` encapsulates a task that runs asynchronously; 
* it as an asynchronous method with no parameters and no return value

A `Callable` is similar to a Runnable,but have return a value
```
public interface Callable<V> //a parameterized type
   {
      V call() throws Exception;
   }
```
A `Future` holds the result of an asynchronous computation.
 * You start a computation, give someone the `Future` object, and forget about it. 
 * The owner of the Future object can obtain the result when it is ready.

```
V get()
V get(long timeout, TimeUnit unit)
void cancel(boolean mayInterrupt)
boolean isCancelled()
boolean isDone()
```

One way to execute a `Callable` is to use a `FutureTask` which implements both the Future and Runnable interfaces,
```
Callable<Integer> task = . . .;
var futureTask = new FutureTask<Integer>(task);
var t = new Thread(futureTask); // it's a Runnable t.start();
...
Integer result = task.get(); // it's a Future

```
* `FutureTask` constructs an object that is both a Future<V> and a Runnable.
### 12.6.2 Executors
The  `Executors` class has a number of static factory methods for constructing thread pools
<img width="300" alt="Screen Shot 2021-10-11 at 2 36 48 PM" src="https://user-images.githubusercontent.com/27160394/136743598-240d9280-be4b-4a49-8cf5-bba050a8e18c.png">
* Use a cached thread pool when you have threads that are short-lived or spend a lot of time blocking.
* use a fixed thread pool: the number of concurrent threads is the number of processor cores.
* The single-thread executor is useful for performance analysis

`submit` ： The pool will run the submitted task at its earliest convenience. When you call submit, you get back a Future object that you can use to get the result or cancel the task.

Summary of thread pool
1. Call the static new `CachedThreadPool` or `newFixedThreadPool` method of the Executors class.
2. Call `submit` to  submit `Callable` or `Runnable` objects.
3. Hang  on to the returned `Future` objects so that you can get the results or cancel the tasks.
4. Call `shutdown` when you no longer want to submit any tasks.

### 12.6.3 Controlling Groups of Tasks
> an executor is used for a more tactical reason—simply to control a group of related tasks. 
* you can cancel all tasks in an executor with the `shutdownNow` method.
* The `invokeAny` method submits all objects in a collection of `Callable` objects and blocks until all of them complete, and returns a list of Future objects that represent the solutions to all task.
* `ExecutorCompletionService.`:obtaining the results in the order in which they are available.
       
```
var service = new ExecutorCompletionService<T>(executor); //a more efficient organization for the preceding computation
for (Callable<T> task : tasks) service.submit(task);
for (int i = 0; i < tasks.size(); i++)
     processFurther(service.take().get());
```
                                 
### 12.6.4 The Fork-Join Framework
> Decomposes into subtasks,
```
protected Integer compute()
{
    if (to - from < THRESHOLD) {
             solve problem directly
    } else {
       int mid = (from + to) / 2;
       var first = new Counter(values, from, mid, filter); 
       var second = new Counter(values, mid, to, filter); 
       invokeAll(first, second); //the invokeAll method receives a number of tasks and blocks until all of them have completed
       return first.join() + second.join(); //The join method yields the result
} }
```
* we apply `join` to each subtask and return the sum.
* Fork-join pools are optimized for nonblocking workloads. implement the `ForkJoinPool.ManagedBlocker` interface might be a good idea to handle that add many blocking tasks into a fork-join pool.
---
## 12.7 Asynchronous Computations
### 12.7.1 Completable Futures
The `CompletableFuture` class implements the `Future` interface : obtaining the result by register a `callback` that will be invoked (in some thread) with the result once it is available.

`CompletableFuture.supplyAsync` : submit to to an executor service instead of submit it directly  
                              
`CompletableFuture` can complete in two ways: 
1. either with a result, 
2. an uncaught exception.
                              
`whenComplete` method can handle both case:
1. The supplied function is called with the result (or null if none) 
2. The exception (or null if none).
                             
* The `isDone` method tells you whether a Future object has been completed (normally or with an exception)
* `CompletableFuture` is not interrupted when you invoke its `cancel` method. Canceling simply sets the Future object to be completed exceptionally with a `CancellationException`

### 12.7.2 Composing Completable Futures
Registers a `callback` for the action that should occur after a task completes.

The `CompletableFuture` class providing a mechanism for composing asynchronous tasks into a processing pipeline

---
## 12.8 Processes
> The `Process` us ab executing  program.
The `ProcessBuilder` class lets you configure a `Process` object.
### 12.8.1 Building a Process
```
var builder = new ProcessBuilder("gcc", "myapp.c");
```
Each process has a working directory, By default, a process has the same working directory as the virtual machine, which is typically the directory from which you launched the java program. 
```
builder = builder.directory(path.toFile());
```
Each of the methods for configuring a `ProcessBuilder` returns itself
```
Process p = new ProcessBuilder(command).directory(file).... start();
```
Specify what should happen to the standard input, output, and error streams of the process.
 ```
 OutputStream processIn = p.getOutputStream(); 
 InputStream processOut = p.getInputStream(); 
 InputStream processErr = p.getErrorStream();
 ```
* Note that the input stream of the process is an output stream in the JVM! 

`builder.redirect`: Represents a source of subprocess input or a destination of subprocess output.
* `ProcessBuilder.Redirect.INHERIT` :subprocess I/O source or destination will be the same as those of the current process.

`builder.environment()`:modify the environment variables of the process.

`startPipeline` method : Pass a list of process builders and read the result from the last process
      
### 12.8.2 Running a Process
>  invoke its `start` method to start the process

To `wait` for the process to finish, call
```
int result = process.waitFor(); // returns the exit value of the process
if (process.waitfor(delay, TimeUnit.SECONDS)) { //The second call returns true if the process didn’t time out.
       int result = process.exitValue(); ...
} 
```
To kill the process, call `destroy` or `destroyForcible`

Finally, you can receive an asynchronous notification when the process has completed. The call `process.onExit()` yields a `CompletableFuture<Process`

### 12.8.3 Process Handles
> `ProcessHandle` interface to get more information about a process that your program started, or any other process that is currently running on your machine

Obtain a ProcessHandle in four ways:
1. Given a `Process` object p, `p.toHandle()` yields its Process Handle.
2. Given a `long` operating system process ID, `ProcessHandle.of(id)` yields the handle of that process.
3. `Process.current()` 
4. `ProcessHandle.allProcesses()` yields a Stream<`ProcessHandle`> of all operating system processes that are visible to the current process.
