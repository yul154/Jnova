![image](https://user-images.githubusercontent.com/27160394/144948077-7515c93b-c900-4445-9723-b30fc72c8e8d.png)


# Java IO - 分类(传输，操作)

## Few Terms

*  流的方向 从外部到程序，称为输入流；从程序到外部，称为输出流
*  流的数据单位 程序以字节作为最小读写数据单元，称为字节流，以字符作为最小读写数据单元，称为字符流
*  节点流: 从/向一个特定的IO设备（如磁盘，网络）或者存储对象(如内存数组)读/写数据的流
*  处理流(或称为过滤流) : 对一个已有流进行连接和封装，通过封装后的流来实现数据的读/写功能


## IO理解分类 - 从传输方式上

从数据传输方式或者说是运输方式角度看
* 字节流: 计算机看的
  * 字节流读取单个字节，字符流读取单个字符(一个字符根据编码的不同，对应的字节也不同，如 UTF-8 编码是 3 个字节，中文编码是 2 个字节。) 字节流用来处理二进制文件(图片、MP3、视频文件)， 
* 字符流: 人看的
  * 字符流用来处理文本文件(可以看做是特殊的二进制文件，使用了某种编码，人可以阅读)。  

<img width="327" alt="Screen Shot 2021-12-07 at 9 20 51 AM" src="https://user-images.githubusercontent.com/27160394/144948463-5569a44f-e98a-4a58-b67b-995f8d0f395c.png">

<img width="285" alt="Screen Shot 2021-12-07 at 9 21 48 AM" src="https://user-images.githubusercontent.com/27160394/144948545-7c73cf9c-ea3d-4df1-acfc-1a3dad21582f.png">


### 字节流和字符流的区别
*  面向字节流的InputStream和OutputStream 面向字符的Reader和Writer
*  字节流读取单个字节，字符流读取单个字符(一个字符根据编码的不同，对应的字节也不同，如 UTF-8 编码是 3 个字节，中文编码是 2 个字节。) 
*  字节流用来处理二进制文件(图片、MP3、视频文件)，读出来的是bit， 字符流用来处理文本文件(可以看做是特殊的二进制文件，使用了某种编码，人可以阅读)。处理的是char
*  编码就是把字符转换为字节，而解码是把字节重新组合成字符



## 从数据来源或者说是操作对象角度看，IO 类可以分为
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

**四个基本抽象流**

* InputStream
* OutputStream
* Reader
* Writer

**节点流**
* File文件
* Piped 进程内线程通信管道
* ByteArray / CharArray (字节数组 / 字符数组)
* StringBuffer / String (字符串缓冲区 / 字符串)

<img width="248" alt="Screen Shot 2021-12-18 at 9 11 28 PM" src="https://user-images.githubusercontent.com/27160394/146642283-c6eb7e13-f81e-4586-94e7-84da4c708636.png">

节点流的创建通常是在构造函数传入数据源
```
FileWriter writer = new FileWriter(new File("file.txt"));
```
**处理流**
> 处理流的应用了适配器/装饰模式
<img width="333" alt="Screen Shot 2021-12-18 at 9 14 00 PM" src="https://user-images.githubusercontent.com/27160394/146642337-11f8eab8-4928-4078-9822-76100c0e3c0a.png">

处理流I/O类名由对已有流封装的功能 + 抽象流类型组成
* 缓冲：对节点流读写的数据提供了缓冲的功能，数据可以基于缓冲批量读写，提高效率。常见有BufferedInputStream、BufferedOutputStream
* 字节流转换为字符流：由InputStreamReader、OutputStreamWriter实现
* 字节流与基本类型数据相互转换：这里基本数据类型数据如int、long、short，由DataInputStream、DataOutputStream实现
* 字节流与对象实例相互转换：用于实现对象序列化，由ObjectInputStream、ObjectOutputStream实现

```
BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(fileOutputStream);
```
---

# JAVA I/O 设计模式

## 装饰者模式
```
* 装饰者(Decorator)和具体组件(ConcreteComponent)都继承自组件(Component)，
* 具体组件的方法实现不需要依赖于其它对象，而装饰者组合了一个组件，这样它可以装饰其它装饰者或者具体组件。
* 所谓装饰，就是把这个装饰者套在被装饰者之上，从而动态扩展被装饰者的功能。装饰者的方法有一部分是自己的，这属于它的功能，然后调用被装饰者的方法实现，从而也保留了被装饰者的功能。
* 可以看到，具体组件应当是装饰层次的最低层，因为只有具体组件的方法实现不需要依赖于其它对象。
````

* 抽象构件（Component）角色：由InputStream扮演。这是一个抽象类，为各种子类型流处理器提供统一的接口。
* 具体构件（ConcreteComponent）角色：由ByteArrayInputStream、FileInputStream、PipedInputStream以及StringBufferInputStream等原始流处理器扮演。他们实现了抽象构件角色所规定的接口，可以被链接流处理器所装饰。
* 抽象装饰（Decorator）角色：由FilterInputStream扮演。它实现了InputStream所规定的接口。
* 具体装饰（ConcreteDecorator）角色：由几个类扮演，分别是DataInputStream、BufferInputStream以及两个不常用的类LineNumberInputStream和PushBackInputStream


> 装饰模式和适配器模式的对比

* 装饰模式和适配器模式，都是通过封装其他对象达到设计目的的
* 理想的装饰模式在对被装饰对象进行功能增强时,要求具体构件角色,装饰角色的接口与抽象构件角色的接口完全一致；而适配器模式则不然，一般而言，适配器模式并不要求对源对象的功能进行增强，只是利用源对象的功能而已，但是会改变源对象的接口，以便和目标接口相符合。
* 装饰模式有透明和半透明两种，区别就在于接口是否完全一致。
  * 关于装饰模式的重要的事实是，很难找到理想的装饰模式。一般而言，对一个对象进行功能增强的同时，都会导致加入新的行为，因此，装饰角色的接口比抽象构件角色的接口宽是很难避免的，这种现象存在于Java I/O库中多有的类型的链接流处理器中。一个装饰类提供的新的方法越多，它离纯装饰模式的距离就越远，离适配器模式的距离也就越近。


### 适配器模式
> 将某个类的接口转换成我们期望的另一个接口表示，目的是消除由于接口不匹配所造成的类的兼容性问题

* 字符流与字节流间互相适配
* ByteArrayInputStream是一个适配器类 。ByteArrayInputStream继承了InputStream的接口，而封装了一个byte数组
* 它将一个byte数组的接口适配成了InputStream流处理器的接口


---

## Sockets
> TCP用主机的IP地址加上主机上的端口号作为TCP连接的端点，这种端点就叫做套接字（socket）或插口

**Java 中的网络支持:**
* InetAddress: 用于表示网络上的硬件资源，即 IP 地址； 
* URL: 统一资源定位符；
*  Sockets: 使用 TCP 协议实现网络通信； 
*  Datagram: 使用 UDP 协议实现网络通信

```
InetAddress.getByName(String host); // 没有公有的构造函数，只能通过静态方法来创建实例。
URL url = new URL("http://www.baidu.com");//可以直接从 URL 中读取字节流数据。
```

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


