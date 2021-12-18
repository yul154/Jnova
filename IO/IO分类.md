![image](https://user-images.githubusercontent.com/27160394/144948077-7515c93b-c900-4445-9723-b30fc72c8e8d.png)


# Java IO - 分类(传输，操作)

**IO理解分类 - 从传输方式上**
从数据传输方式或者说是运输方式角度看
* 字节流: 计算机看的
  * 字节流读取单个字节，字符流读取单个字符(一个字符根据编码的不同，对应的字节也不同，如 UTF-8 编码是 3 个字节，中文编码是 2 个字节。) 字节流用来处理二进制文件(图片、MP3、视频文件)， 
* 字符流: 人看的
  * 字符流用来处理文本文件(可以看做是特殊的二进制文件，使用了某种编码，人可以阅读)。  

<img width="327" alt="Screen Shot 2021-12-07 at 9 20 51 AM" src="https://user-images.githubusercontent.com/27160394/144948463-5569a44f-e98a-4a58-b67b-995f8d0f395c.png">

<img width="285" alt="Screen Shot 2021-12-07 at 9 21 48 AM" src="https://user-images.githubusercontent.com/27160394/144948545-7c73cf9c-ea3d-4df1-acfc-1a3dad21582f.png">




**字节转字符**
> 如果编码和解码过程使用不同的编码方式那么就出现了乱码。

编码就是把字符转换为字节，而解码是把字节重新组合成字符



**从数据来源或者说是操作对象角度看，IO 类可以分为:**
* 文件(file)
  * FileInputStream、FileOutputStream、FileReader、FileWriter
* 数组([]) 字节数组(byte[]): 
  * ByteArrayInputStream、ByteArrayOutputStream 字符数组(char[]): CharArrayReader、CharArrayWriter
* 管道操作
  * PipedInputStream、PipedOutputStream、PipedReader、PipedWriter 
* 基本数据类型
  * DataInputStream、DataOutputStream
* 缓冲操作
  * BufferedInputStream、BufferedOutputStream、BufferedReader、BufferedWriter
* 打印
  * PrintStream、PrintWriter
* 对象序列化反序列化
  * ObjectInputStream、ObjectOutputStream
* 转换
  * InputStreamReader、OutputStreamWriter
  
----
# 常见类使用

* 磁盘操作: File
* 字节操作: InputStream 和 OutputStream
* 字符操作: Reader 和 Writer
* 对象操作: Serializable: 不会对静态变量进行序列化，因为序列化只是保存对象的状态
* 网络操作: Socket


**Java 中的网络支持:**
* InetAddress: 用于表示网络上的硬件资源，即 IP 地址； 
* URL: 统一资源定位符；
*  Sockets: 使用 TCP 协议实现网络通信； 
*  Datagram: 使用 UDP 协议实现网络通信

```
InetAddress.getByName(String host); // 没有公有的构造函数，只能通过静态方法来创建实例。
URL url = new URL("http://www.baidu.com");//可以直接从 URL 中读取字节流数据。
```

## Sockets
> TCP用主机的IP地址加上主机上的端口号作为TCP连接的端点，这种端点就叫做套接字（socket）或插口

套接字(Socket)，就是对网络中不同主机上的应用进程之间进行双向通信的端点的抽象

Socket是进程通讯的一种方式，即调用这个网络库的一些API函数实现分布在不同主机的相关进程之间的数据交换
*  `bind`: 将套接字绑定一个IP地址和端口号，因为这两个元素可以在网络环境中唯一地址表示一个进程
*  `listen`: 主动连接套接字变为被连接套接口，使得一个进程可以接受其它进程的请求
*  `connect`: 连接服务器
*  ` accept`: accept()接受一个客户端的连接请求，并返回一个新的套接字
*  `read/write函数`:

<img width="562" alt="Screen Shot 2021-12-07 at 10 46 44 AM" src="https://user-images.githubusercontent.com/27160394/144956896-fc9228f1-b8a4-4d7d-b5d0-950cfa0032de.png">

* ServerSocket: 服务器端类
* Socket: 客户端类
* 服务器和客户端通过 InputStream 和 OutputStream 进行输入输出。

```
服务器绑定端口：server = new ServerSocket(PORT)
服务器阻塞监听：socket = server.accept()
服务器开启线程：new Thread(Handle handle)
服务器读写数据：BufferedReader PrintWriter
客户端绑定IP和PORT：new Socket(IP_ADDRESS, PORT)
客户端传输接收数据：BufferedReader PrintWrite
```
----
# IO 模型
一个输入操作通常包括两个阶段:
* 等待数据准备好
* 从内核向进程复制数据

**五种 I/O 模型**
* 阻塞式 I/O
* 非阻塞式 I/O
* I/O 复用(select 和 poll)
* 信号驱动式 I/O(SIGIO)
* 异步 I/O(AIO)

### 阻塞式 I/O
应用进程被阻塞，直到数据复制到应用进程缓冲区中才返回。
* 在阻塞的过程中，其它程序还可以执行

### 非阻塞式 I/O
应用进程执行系统调用之后，内核返回一个错误码。应用进程可以继续执行，但是需要不断的执行系统调用来获知 I/O 是否完成，这种方式称为轮询(polling)

### I/O 复用
使用 select 或者 poll 等待数据，并且可以等待多个套接字中的任何一个变为可读，这一过程会被阻塞，当某一个套接字可读时返回。之后再使用 recvfrom 把数据从内核复制到进程中
* 以让单个进程具有处理多个 I/O 事件的能力。又被称为 Event Driven I/O，即事件驱动 I/O。

### 信号驱动 I/O
应用进程使用 sigaction 系统调用，内核立即返回，应用进程可以继续执行，也就是说等待数据阶段应用进程是非阻塞的。内核在数据到达时向应用进程发送 SIGIO 信号，应用进程收到之后在信号处理程序中调用 recvfrom 将数据从内核复制到应用进程中


### 异步 I/O
进行 aio_read 系统调用会立即返回，应用进程继续执行，不会被阻塞，内核会在所有操作完成之后向应用进程发送信号。 
* 异步 I/O 与信号驱动 I/O 的区别在于，异步 I/O 的信号是通知应用进程 I/O 完成，而信号驱动 I/O 的信号是通知应用进程可以开始 I/O。

<img width="728" alt="Screen Shot 2021-12-07 at 9 46 46 AM" src="https://user-images.githubusercontent.com/27160394/144950875-98cef34a-f60d-4e32-9454-c14453d113f8.png">


## IO多路复用
epoll 的描述符事件有两种触发模式: LT(level trigger)和 ET(edge trigger)。
* LT 模式: 当 epoll_wait() 检测到描述符事件到达时，将此事件通知进程，进程可以不立即处理该事件，下次调用 epoll_wait() 会再次通知进程。是默认的一种模式
* ET 模式: 通知之后进程必须立即处理事件，下次再调用 epoll_wait() 时不会再得到事件到达的通知。

select 应用场景
* select 的 timeout 参数精度为 1ns，而 poll 和 epoll 为 1ms，因此 select 更加适用于实时要求更高的场景

poll 应用场景
* poll 没有最大描述符数量的限制，如果平台支持并且对实时性要求不高，应该使用 poll 而不是 select。
* 需要同时监控小于 1000 个描述符，就没有必要使用 epoll
* 需要监控的描述符状态变化多，而且都是非常短暂的，也没有必要使用 epoll。因为 epoll 中的所有描述符都存储在内核中，造成每次需要对描述符的状态改变都需要通过 epoll_ctl() 进行系统调用，频繁系统调用降低效率。

epoll 应用场景
* 只需要运行在 Linux 平台上，并且有非常大量的描述符需要同时轮询，而且这些连接最好是长连接

----

# BIO,AIO, NIO

* 阻塞IO 和 非阻塞IO
  * 程序级别的
  * 程序请求操作系统IO操作后，如果IO资源没有准备好，那么程序该如何处理的问题
  * 阻塞是等待
  * 非阻塞继续执行，并且使用线程一直轮询，直到有IO资源准备好了

* 同步IO 和 非同步IO
  * 操作系统级别的
  * 操作系统在收到程序请求IO操作后，如果IO资源没有准备好，该如何响应程序的问题: 
  * 同步IO不响应，直到IO资源准备好以后；
  * 非同步IO返回一个标记(好让程序和自己知道以后的数据往哪里通知)，当IO资源准备好以后，再用事件机制返回给程序。


同步阻塞IO（BIO）：用户进程发起一个IO操作以后，必须等待IO操作的真正完成后，才能继续运行；

同步非阻塞IO（NIO）：用户进程发起一个IO操作以后，可做其它事情，但用户进程需要经常询问IO操作是否完成，这样造成不必要的CPU资源浪费；

异步非阻塞IO（AIO）：用户进程发起一个IO操作然后，立即返回，等IO操作真正的完成以后，应用程序会得到IO操作完成的通知。类比Future模式。


![image](https://user-images.githubusercontent.com/27160394/144961570-fcd22eae-a64a-42aa-b549-bb5782ff3c82.png)


## BIO通信方式
> Java BIO是传统的Java io编程，相关的类和接口在`java.io`包中

服务器通过一个 Acceptor 线程负责监听客户端请求和为每个客户端创建一个新的线程进行链路处理。典型的一请求一应答模式。
* 服务器提供IP地址和监听的端口，客户端通过TCP的三次握手与服务器连接，连接成功后，双放才能通过套接字(Stock)通信。
* 若客户端数量增多，频繁地创建和销毁线程会给服务器打开很大的压力。后改良为用线程池的方式代替新增线程，被称为伪异步IO

**伪异步I/O模型“**
* 为了改进这种一连接一线程的模型，我们可以使用线程池来管理这些线程,实现1个或多个线程处理N个客户端的模型（但是底层还是使用的同步阻塞I/O）
* 虽然在服务器端，请求的处理交给了一个独立线程进行，但是操作系统通知accept(它内部的实现是使用的操作系统级别的同步IO)的方式还是单个的。也就是，实际上是服务器接收到数据报文后的“业务处理过程”可以多线程

<img width="598" alt="Screen Shot 2021-12-07 at 11 01 49 AM" src="https://user-images.githubusercontent.com/27160394/144958433-5dffaac6-d498-42f0-ba75-5dc2009147e9.png">


> BIO模型中通过 Socket 和 ServerSocket 完成套接字通道的实现。阻塞，同步，建立连接耗时


## NIO
> 一种同步非阻塞的通信模式,相关类都被放在 java.nio 包及子包下

<img width="520" alt="Screen Shot 2021-12-07 at 11 54 47 AM" src="https://user-images.githubusercontent.com/27160394/144963351-a090071e-7b32-4382-b086-43f54fc68b92.png">


客户端和服务器之间通过Channel通信。
* NIO可以在Channel进行读写操作。这些Channel都会被注册在Selector多路复用器上。
* Selector通过一个线程不停的轮询这些Channel。找出已经准备就绪的Channel执行IO操作。 
* NIO 通过一个线程轮询，实现千万个客户端的请求，这就是非阻塞NIO的特点

> NIO模型中通过SocketChannel和ServerSocketChannel完成套接字通道的实现。非阻塞/阻塞，同步，避免TCP建立连接使用三次握手带来的开销

## AIO (NIO.2)
> 异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理

* AIO 并没有采用NIO的多路复用器，而是使用异步通道的概念。其read，write方法的返回类型都是Future对象。
  * Future模型是异步的，其核心思想是：去主函数等待时间
* AIO方式使用于连接数目多且连接比较长（重操作）的架构

> AIO模型中通过`AsynchronousSocketChannel`和`AsynchronousServerSocketChannel`完成套接字通道的实现。非阻塞，异步

**BIO，NIO，AIO区别**
* IO（同步阻塞）：客户端和服务器连接需要三次握手，使用简单，但吞吐量小
* NIO（同步非阻塞）：客户端与服务器通过Channel连接，采用多路复用器轮询注册的Channel。提高吞吐量和可靠性。
* AIO（异步非阻塞）：NIO的升级版，采用异步通道实现异步通信，其read和write方法均是异步方法。
----
