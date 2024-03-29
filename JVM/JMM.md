# JMM

内存模型其实就是一套规则，这套规则决定了一个线程对共享变量的写入何时对另一个线程可见

## 堆栈

* 线程堆栈还包含正在执行的每个方法的所有局部变量(调用堆栈上的所有方法).线程只能访问它自己的线程堆栈
* 基本类型的所有局部变量完全存储在线程堆栈中，因此对其他线程不可见。 一个线程可以将一个基本类型变量的副本传递给另一个线程，但它不能共享原始局部变量本身
* 局部变量可以是基本类型，在这种情况下，它完全保留在线程堆栈上。
* 局部变量也可以是对象的引用.在这种情况下，引用(局部变量)存储在线程堆栈中，但是对象本身存储在堆(Heap)上。
* 静态类变量也与类定义一起存储在堆上。
* 对象的成员变量与对象本身一起存储在堆上。 当成员变量是基本类型时，以及它是对象的引用时都是如此,对象本身有成员变量的引用


## JMM与硬件内存结构关系
> JMM模型跟CPU缓存模型结构类似，是基于CPU缓存模型建立起来的

<img width="405" alt="Screen Shot 2021-12-13 at 9 33 32 AM" src="https://user-images.githubusercontent.com/27160394/145739198-28668ac1-fbe0-4d84-8d58-881379f4f031.png">


Java 虚拟机规范中定义一种 Java 内存模型（Java Memory Model，JMM）来 屏蔽各个硬件平台和操作系统的内存（RAM）访问差异

在具有2个或更多CPU的现代计算机上，可以同时运行多个线程。 每个CPU都能够在任何给定时间运行一个线程。 这意味着如果您的Java应用程序是多线程的，线程真的在可能同时运行.

每个CPU基本上都包含一组在CPU内存中的寄存器。CPU可以在这些寄存器上执行的操作比在主存储器中对变量执行的操作快得多。 这是因为CPU可以比访问主存储器更快地访问这些寄存器。

大多数现代CPU都有一些大小的缓存存储层。 CPU可以比主存储器更快地访问其高速缓存存储器，但通常不会像访问其内部寄存器那样快。 因此，CPU高速缓存存储器介于内部寄存器和主存储器的速度之间

计算机还包含主存储区(RAM)。 所有CPU都可以访问主内存。 主存储区通常比CPU的高速缓存存储器大得多。同时访问速度也就较慢. 当CPU需要访问主存储器时，它会将部分主存储器读入其CPU缓存

当CPU需要将结果写回主存储器时，它会将值从其内部寄存器刷新到高速缓冲存储器，并在某些时候将值刷新回主存储器

## JMM与硬件内存连接 - 引入

在硬件上，线程堆栈和堆都位于主存储器中. 线程堆栈和堆的一部分有时可能存在于CPU高速缓存和内部CPU寄存器中

当对象和变量可以存储在计算机的各种不同存储区域中时，可能会出现某些问题。 两个主要问题是
* Visibility of thread updates (writes) to shared variables. 
* Race conditions when reading, checking and writing shared variables


## JMM 引入

Java 中的并发采用的就是共享内存模型，因为这样 Java 线程之间的整个通信过程对程序员来说是完全透明的。
1. 线程A 把本地内存A 中更新过的共享变量刷新到主内存中去
2. 线程B 到主内存中去读取线程A 之前已更新过的共享变量

Java 虚拟机规范中定义一种 Java 内存模型（Java Memory Model，JMM）来 屏蔽各个硬件平台和操作系统的内存（RAM）访问差异
* JMM 定义了线程和主内存之间的抽象关系：
    * 线程之间的共享变量存储在主内存（Main Memory）中，
    * 每个线程都有一个私有的本地内存（Local Memory），本地内存中存储了该线程以 读/写 共享变量的副本。


JMM 通过控制主内存与每个线程的本地内存之间的交互，来提供内存可见性保证。
* Java 内存模型只保证了基本读取和赋值是原子性操作


## 内存间交互操作
* read：把一个变量的值从主内存传输到工作内存中
* load：在 read 之后执行，把 read 得到的值放入工作内存的变量副本中
* use：把工作内存中一个变量的值传递给执行引擎
* assign：把一个从执行引擎接收到的值赋给工作内存的变量
* store：把工作内存的一个变量的值传送到主内存中
* write：在 store 之后执行，把 store 得到的值放入主内存的变量中
* lock：作用于主内存的变量，把一个变量标识为一条线程独占状态
* unlock：作用于主内存变量，把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定

Java内存模型还规定了在执行上述8种基本操作时，必须满足如下规则：
* 不允许 read 和 load、store 和 write 操作之一单独出现
* 不允许一个线程丢弃它的最近 assign 的操作，即变量在工作内存中改变了之后必须同步到主内存中
* 不允许一个线程无原因地（没有发生过任何assign操作）把数据从工作内存同步回主内存中
* 一个新的变量只能在主内存中诞生，不允许在工作内存中直接使用一个未被初始化（load 或 assign）的变量。即就是对一个变量实施 use 和 store 操作之前，必须先执行过了 assign 和 load 操作。
* 一个变量在同一时刻只允许一条线程对其进行lock操作，lock 和 unlock必须成对出现
* 如果对一个变量执行 lock 操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量前需要重新执行 load 或 assign 操作初始化变量的值
* 如果一个变量事先没有被 lock 操作锁定，则不允许对它执行 unlock 操作；也不允许去 unlock 一个被其他线程锁定的变量。
* 对一个变量执行 unlock 操作之前，必须先把此变量同步到主内存中（执行 store 和 write 操作）。



## JMM与硬件内存连接 - 对象共享后的可见性
> 如果两个或多个线程共享一个对象，而没有正确使用volatile声明或同步，则一个线程对共享对象的更新可能对其他线程不可见。

共享对象最初存储在主存储器中。 然后，在CPU上运行的线程将共享对象读入其CPU缓存中。 它在那里对共享对象进行了更改。 
* 只要CPU缓存尚未刷新回主内存，共享对象的更改版本对于在其他CPU上运行的线程是不可见的
* 每个线程最终都可能拥有自己的共享对象副本，每个副本都位于不同的CPU缓存中

<img width="417" alt="Screen Shot 2021-12-13 at 9 40 45 AM" src="https://user-images.githubusercontent.com/27160394/145739665-715c7b74-0ad7-4ba2-9a69-4dfd7d676c76.png">


## JMM与硬件内存连接 - 竞态条件
> 如果两个或多个线程共享一个对象，并且多个线程更新该共享对象中的变量，则可能会出现竞态


如果线程A将共享对象的变量计数读入其CPU缓存中。 想象一下，线程B也做同样的事情，但是进入不同的CPU缓存。 现在，线程A将一个添加到count，而线程B执行相同的操作。 现在var1已经增加了两次，每个CPU缓存一次

<img width="423" alt="Screen Shot 2021-12-13 at 9 43 43 AM" src="https://user-images.githubusercontent.com/27160394/145739835-5420527b-25d6-4c78-bd09-901ce41140d9.png">


要解决此问题,您可以使用Java synchronized块.同步块保证在任何给定时间只有一个线程可以进入代码的给定关键部分.同步块还保证在同步块内访问的所有变量都将从主存储器中读入,当线程退出同步块时，所有更新的变量将再次刷新回主存储器，无论变量是不是声明为volatile ¶

----
# Summary
> Java 内存模型试图屏蔽各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的内存访问效果

将变量从主内存拷贝的每个线程各自的工作内存空间，然后对变量进行操作,操作完成后再将变量写回主内存

处理器上的寄存器的读写的速度比内存快几个数量级，为了解决这种速度矛盾，在它们之间加入了高速缓存
* 加入高速缓存带来了一个新的问题：缓存一致性。如果多个缓存共享同一块主内存区域，那么多个缓存的数据可能会不一致
* 线程只能直接操作工作内存中的变量，不同线程之间的变量值传递需要通过主内存来完成
* Java 内存模型下，线程可以把变量保存本地内存（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成数据的不一致。


JMM 使用 happens-before 的概念来 定制两个操作之间的执行顺序。这两个操作可以在一个线程以内，也可以是不同的线程之间。因此，JMM 可以通过 happens-before 关系向程序员提供跨线程的内存可见性保证

