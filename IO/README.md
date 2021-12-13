# I/O

从计算机结构的视角来看的话， I/O 描述了计算机系统与外部设备之间通信的过程。

* 为了保证操作系统的稳定性和安全性，一个进程的地址空间划分为 用户空间（User space） 和 内核空间（Kernel space ） 。
* 像我们平常运行的应用程序都是运行在用户空间，只有内核空间才能进行系统态级别的资源有关的操作，比如文件管理、进程通信、内存管理等等。
  * 我们想要进行 IO 操作，一定是要依赖内核空间的能力
  * 用户空间的程序不能直接访问内核空间
* 当想要执行 IO 操作时，由于没有执行这些操作的权限，只能发起系统调用请求操作系统帮忙完成。
    * 因此，用户进程想要执行 IO 操作的话，必须通过 系统调用 来间接访问内核空间
    * 操作系统负责的内核执行具体的 IO 操作
    * 我们的应用程序实际上只是发起了 IO 操作的调用而已，具体 IO 的执行是由操作系统的内核来完成的


当应用程序发起 I/O 调用后，会经历两个步骤：
* 内核等待 I/O 设备准备好数据
* 内核将数据从内核空间拷贝到用户空间。

接触最多的就是 磁盘 IO（读写文件） 和 网络 IO（网络请求和响应）


# Java 中 3 种常见 IO 模型

## BIO (Blocking I/O)
> 属于同步阻塞 IO 模型 

用程序发起 read 调用后，会一直阻塞，直到内核把数据拷贝到用户空间

<img width="305" alt="Screen Shot 2021-12-13 at 3 49 42 PM" src="https://user-images.githubusercontent.com/27160394/145772604-3d19fd8e-1026-47e1-b894-6b7bf861857a.png">


## NIO (Non-blocking/New I/O)
> Java 中的 NIO 可以看作是 I/O 多路复用模型。也有很多人认为，Java 中的 NIO 属于同步非阻塞 IO 模型

应用程序会一直发起read调用，等待数据从内核空间拷贝到用户空间的这段时间里，线程依然是阻塞的，直到在内核把数据拷贝到用户空间

<img width="475" alt="Screen Shot 2021-12-13 at 3 51 13 PM" src="https://user-images.githubusercontent.com/27160394/145772772-4403a338-3352-4695-9582-098057d5a7d7.png">

### 多路复用
> 应用程序不断进行 I/O 系统调用轮询数据是否已经准备好的过程是十分消耗 CPU 资源的。

<img width="474" alt="Screen Shot 2021-12-13 at 3 52 14 PM" src="https://user-images.githubusercontent.com/27160394/145772887-3f2b13e2-075d-4174-aa90-8b3914e5d880.png">

IO 多路复用模型中，线程首先发起 select 调用，询问内核数据是否准备就绪，等内核把数据准备好了，用户线程再发起 read 调用。read 调用的过程（数据从内核空间->用户空间）还是阻塞的
* IO 多路复用模型，通过减少无效的系统调用，减少了对 CPU 资源的消耗


```
目前支持 IO 多路复用的系统调用，有 select，epoll 等等。select 系统调用
* select 调用 ：内核提供的系统调用，它支持一次查询多个系统调用的可用状态。几乎所有的操作系统都支持。 
* epoll 调用 ：linux 2.6 内核，属于 select 调用的增强版本，优化了 IO 的执行效率。

```

Java 中的 NIO ，有一个非常重要的选择器 ( Selector ) 的概念，也可以被称为 多路复用器。通过它，只需要一个线程便可以管理多个客户端连接。当客户端数据到了之后，才会为其服务

<img width="441" alt="Screen Shot 2021-12-13 at 3 55 23 PM" src="https://user-images.githubusercontent.com/27160394/145773306-e07261a3-553d-4e7e-b4e1-9ef835c95047.png">

## AIO (Asynchronous I/O)
> Java 7 中引入了 NIO 的改进版 NIO 2,它是异步 IO 模型

异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作
