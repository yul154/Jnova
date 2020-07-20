# Multithreading

## Basic terms

Multitasking
* Multitasking can be achieved in two ways :Multiprocessing，Multithreading
* If you have to perform multiple tasks by multiple threads,you need multiple run() methods

Thread
> lightweight process

* A multithreaded program contains two or more parts that can run concurrently
* Each part of such a program is called a thread and each thread defines a separate path of the execution.

Java Thread status.

<img width="489" alt="Screen Shot 2019-10-13 at 6 27 18 PM" src="https://user-images.githubusercontent.com/27160394/66723442-3e4d6a80-ede7-11e9-8544-fdeb36fca008.png">

## Multithreading in java

* multithreading is built upon the `Thread` class and its companion interface, `Runnable`.
* To create a new thread, 
  1. your program will either extend Thread
  2. implement the Runnable interface.
  3. using executer service if you want your thread in thread pool
  
<img width="753" alt="Screen Shot 2019-10-13 at 8 55 38 PM" src="https://user-images.githubusercontent.com/27160394/66725100-dc4b3000-edfb-11e9-94c0-7f9c9e720f4e.png">

### thread sychronized
* `volatile`.
  1.prevent threads caching variabls when they're not changed from within tha thread
  2. another way making class thread safe
  3.The value of this variable will never be cached thread-locally: all reads and writes will go straight to "main memory";
* `join`
  1. join() is another mechanism of inter-thread synchronization.
  2. join() method suspends the execution of the calling thread until the object called finishes its execution.

### `Callable`
* represents a task that is intended to be executed concurrently by a separate thread.
* different from a Runnable in that
  1. the Runnable interface's run() method does not return a value
  2.it cannot throw checked exceptions (only RuntimeExceptions).

```
public interface Callable<V> {

    V call() throws Exception;

}
```

Main Java thread
* When a Java program starts up, one thread begins running immediately
*  it affects the other 'child' threads.
* It must be the last thread to finish execution

Thread Scheduler
* Thread scheduler in java is the part of the JVM that decides which thread should run.
* Difference between preemptive scheduling and time slicing： 
  Under preemptive scheduling, the highest priority task executes until it enters the waiting or dead states or a higher priority task comes into existence. Under time slicing, a task executes for a predefined slice of time and then reenters the pool of ready tasks. The scheduler then determines which task should execute next, based on priority and other factors

### Java Thread Pool
*  a group of worker threads that are waiting for the job and reuse many times.
* The `ThreadPoolExecutor` executes the given task (Callable or Runnable) using one of its internally pooled threads.
```
public class TestThreadPool {  
     public static void main(String[] args) {  
        ExecutorService executor = Executors.newFixedThreadPool(5);//creating a pool of 5 threads  
        for (int i = 0; i < 10; i++) {  
            Runnable worker = new WorkerThread("" + i);  
            executor.execute(worker);//calling execute method of ExecutorService  
          }  
        executor.shutdown();  
        while (!executor.isTerminated()) {   }  
  
        System.out.println("Finished all threads");  
    }  
 } 
```

### ThreadGroup
*  a convenient way to group multiple threads in a single object.
```
public class ThreadGroupDemo implements Runnable{  
    public void run() {  
          System.out.println(Thread.currentThread().getName());  
    }  
   public static void main(String[] args) {  
      ThreadGroupDemo runnable = new ThreadGroupDemo();  
          ThreadGroup tg1 = new ThreadGroup("Parent ThreadGroup");  
            
          Thread t1 = new Thread(tg1, runnable,"one");  
          t1.start();  
          Thread t2 = new Thread(tg1, runnable,"two");  
          t2.start();  
          Thread t3 = new Thread(tg1, runnable,"three");  
          t3.start();  
               
          System.out.println("Thread Group Name: "+tg1.getName());  
         tg1.list();  
  
    }  
   }  
```

# Synchronization
> capability to control the access of multiple threads to any shared resource.

* if two threads simultaneously accessing a shared variables
* Java Synchronization is better option where we want to allow only one thread to access the shared resource.
* one single thread can't expect other threads to modify status

Types of Synchronization

1.Process Synchronization.
2.Thread Synchronization.

Thread Synchronization

1.Synchronized method.
2.Synchronized block : Scope of synchronized block is smaller than the method.
3.Static synchronization.
4. Cooperation 



Lock
* Synchronization is built around an internal entity known as the lock or monitor.

Deadlock in java
*  Deadlock can occur in a situation when a thread is waiting for an object lock, that is acquired by another thread and second thread is waiting for an object lock that is acquired by first thread

Inter-thread communication in Java
> Co-operation is all about allowing synchronized threads to communicate with each other.
*  a thread is paused running in its critical section and another thread is allowed to enter (or lock) in the same critical section to be executed
* wait(): Causes current thread to release the lock and wait until either another thread invokes the notify() method or the notifyAll() method for this object, or a specified amount of time has elapsed.
* notify(): Wakes up a single thread that is waiting on this object's monitor.
* notifyAll(): Wakes up all threads that are waiting on this object's monitor
  

Interrupting a Thread:
* If the thread is not in the sleeping or waiting state, calling the interrupt() method performs normal behaviour and doesn't interrupt the thread but sets the interrupt flag to tru
* If any thread is in sleeping or waiting state (i.e. sleep() or wait() is invoked), calling the interrupt() method on the thread, breaks out the sleeping or waiting state throwing InterruptedException.





















# Summary

## Have you worked on multitrhead?  What senorial you used multithread?

1. Lets take an example of bank ,let’s suppose we have an online banking system, 
where people can log in and access their account information. 
Whenever someone logs in to their account online, 
they receive a separate and unique thread so that different bank account holders can access the central system simultaneously.

2. Joint Account holder having multiple ATM cards for same account and trying to perform operation same time . at time one operation is handled for account and then next one on updated data.

3. Online bus/anything ticket booking : here many users trying to book available ticket at same time (ex : tatkal booking ) , here application needs to handle different threads (diff users request to server ) , if tickets sold out/not available then rest users will get correct response as not available to book .

## Difference between Runnable vs Thread in Java

* Implementing Runnable makes your class more flexible. 
* When you extend Thread class, you can’t extend any other class which you require. (As you know, Java does not allow inheriting more than one class). 
* When you implement Runnable, you can save a space for your class to extend any other class in future or now.
* When you extends Thread class, each of your thread creates unique object and associate with it. When you implements Runnable,
it shares the same object to multiple threads.


## Why wait(), notify() and notifyAll() methods are defined in Object class not Thread class?

It is because they are related to lock and object has a lock.

## `run` instead `start`
* if i call `run()`, no new thread will be created and run() method will be executed as a normal method call on the current calling thread itself and no multi-threading will take place.
* if i call `start()`: a new thread is created and then the run() method is executed
