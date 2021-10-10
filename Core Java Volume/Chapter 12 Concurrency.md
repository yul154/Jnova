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
|Calling timeout method causes the thread to enter the `timed waiting` state.| This state persists either until the timeout expires or the appropriate notification |
