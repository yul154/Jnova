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
