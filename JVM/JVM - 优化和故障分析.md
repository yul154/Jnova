调试排错 
- jvm参数
- 垃圾回收
- Java OOM 分析
- Java线程Dump分析
- Java问题排查：Linux命令
- Java问题排查：工具单 

----
# jvm参数

* `-Xms`： 堆最小值
* `-Xmx`: 堆最大堆值:
* `-Xmn`:新生代大小
* `-Xss`:每个线程池的堆栈大小
* `-XX:NewRatio`:设置新生代与老年代比值
* `-XX:PermSize`:设置持久代初始值，默认是物理内存的六十四分之一
* `-XX:MaxPermSize`:设置持久代最大值，默认是物理内存的四分之一
* `-XX:MaxTenuringThreshold`:新生代中对象存活次数，
* `-XX:SurvivorRatio`:Eden区与Subrvivor区大小的比值
* `-XX:PretenureSizeThreshold`:对象超过多大值时直接在老年代中分配

参数建议
* 当`Xms=Xmx`，可以使得堆相对稳定，避免不停震荡, 通常这两个配置参数相等，避免每次空间不足，动态扩容带来的影响。
* `Xmn`用于设置新生代的大小。过小会增加Minor GC频率，过大会减小老年代的大小。一般设为整个堆空间的1/4或1/3.
* `XX:TargetSurvivorRatio`表示，当经历Minor GC后，survivor空间占有量(百分比)超过它的时候，就会压缩进入老年代(当然，如果survivor空间不够，则直接进入老年代)。默认值为50%。
* 为了性能考虑，一开始尽量将新生代对象留在新生代，避免新生的大对象直接进入老年代
* 一般来说，`MaxPermSize`设为64MB可以满足绝大多数的应用了。若依然出现方法区溢出，则可以设为128MB。若128MB还不能满足需求，那么就应该考虑程序优化了

----
# 垃圾回收

GC考虑的指标
* 吞吐量: 应用耗时和实际耗时的比值；
* 停顿时间: 垃圾回收的时候，由于Stop the World，应用程序的所有线程会挂起，造成应用停顿。
```
吞吐量和停顿时间是互斥的。
对于后端服务(比如后台计算任务)，吞吐量优先考虑(并行垃圾回收)；
对于前端应用，RT响应时间优先考虑，减少垃圾收集时的停顿时间，适用场景是Web系统(并发垃圾回收)
```

## 回收器的JVM参数
* `-XX:+UseSerialGC` : 串行垃圾回收，现在基本很少使用。

* `-XX:+UseParNewGC`: 新生代使用并行，老年代使用串行；

* `-XX:+UseConcMarkSweepGC`:新生代使用并行，老年代使用CMS(一般都是使用这种方式)，CMS是Concurrent Mark Sweep的缩写，并发标记清除，一看就是老年代的算法

*  `-XX:ParallelGCThreads` 指定并行的垃圾回收线程的数量，最好等于CPU数量 

*  ` -XX:+DisableExplicitGC` 禁用System.gc()，因为它会触发Full GC，这是很浪费性能的，JVM会在需要GC的时候自己触发GC。

*  `-XX:CMSFullGCsBeforeCompaction` : 在多少次GC后进行内存压缩，这个是因为并行收集器不对内存空间进行压缩的，所以运行一段时间后会产生很多碎片，使得运行效率降低。
---
# OOM 分析
Java OOM 分析
* Java 堆内存溢出
* MetaSpace (元数据) 内存溢出

## Java 堆内存溢出
```
public static void main(String[] args) {
        List<String> list = new ArrayList<>(10) ;
        while (true){
            list.add("1") ;
        }
    }
```

使用 `-XX:+HeapDumpOutofMemoryErorr`当发生 OOM 时会自动 dump 堆栈到文件中。

```
java.lang.OutOfMemoryError: Java heap space表示堆内存溢出。///表示堆内存溢出。
```

原因分析
Javaheap space 错误产生的常见原因可以分为以下几类：
1. 请求创建一个超大对象，通常是一个大数组。
2. 超出预期的访问量/数据量，通常是上游系统请求流量飙升，常见于各类促销/秒杀活动，可以结合业务流量指标排查是否有尖状峰值。
3. 内存泄漏（Memory Leak），大量对象引用没有释放，JVM 无法对其自动回收，常见于使用了 File 等资源没有回收。

* 当出现 OOM 时可以通过工具来分析 GC-Roots 引用链  (opens new window) ，查看对象和 GC-Roots 是如何进行关联的，
* 是否存在对象的生命周期过长，或者是这些对象确实改存在的，那就要考虑将堆内存调大了


解决方案
* 通常只需要通过 -Xmx 参数调高 JVM 堆内存空间即可
* 修改代码



## GC overhead limit exceeded
> 应用程序已经基本耗尽了所有可用内存， GC 也无法回收。

当 Java 进程花费 98% 以上的时间执行 GC，但只恢复了不到 2% 的内存，且该动作连续重复了 5 次，就会抛出 java.lang.OutOfMemoryError:GC overhead limit exceeded



## MetaSpace (元数据) 内存溢出
> JDK8 中将永久代移除，使用 MetaSpace 来保存类加载之后的类信息，字符串常量池也被移动到 Java 堆。

JDK 8 中将类信息移到到了本地堆内存(Native Heap)中，将原有的永久代移动到了本地堆中成为 MetaSpace ,如果不指定该区域的大小，JVM 将会动态的调整。
* `-XX:MaxMetaspaceSize=10M` 来限制最大元数据。
```
java.lang.OutOfMemoryError: Metaspace 也就是元数据溢出。
```

> 原因分析
永久代存储对象主要包括以下几类：

1、加载/缓存到内存中的 class 定义，包括类的名称，字段，方法和字节码；

2、常量池；

3、对象数组/类型数组所关联的 class；

4、JIT 编译器优化后的 class 信息。

PermGen 的使用量与加载到内存的 class 的数量/大小正相关。

> 解决方案

1. 程序启动报错，修改 -XX:MaxPermSize 启动参数，调大永久代空间。

2. 应用重新部署时报错，很可能是没有应用没有重启，导致加载了多份 class 信息，只需重启 JVM 即可解决。

3. 运行时报错，应用程序可能会动态创建大量 class，而这些 class 的生命周期很短暂，但是 JVM 默认不会卸载 class，可以设置 -XX:+CMSClassUnloadingEnabled 和 -XX:+UseConcMarkSweepGC这两个参数允许 JVM 卸载 class。

4. 如果上述方法无法解决，可以通过 jmap 命令 dump 内存对象 jmap-dump:format=b,file=dump.hprof<process-id> ，然后利用 Eclipse MAT

----
# Dump

在故障定位(尤其是out of memory)和性能分析的时候，经常会用到一些文件来帮助我们排除代码问题。
* 这些文件记录了JVM运行期间的内存占用、线程执行等情况，这就是我们常说的dump文件。
  * heap dump: heap dump记录内存信息的
  * thread 是记录CPU信息的。

heap dump文件是一个二进制文件，它保存了某一时刻JVM堆中对象使用情况
* HeapDump文件是指定时刻的Java堆栈的快照，是一种镜像文件。
* Heap Analyzer工具通过分析HeapDump文件，哪些对象占用了太多的堆栈空间，来发现导致内存泄露或者可能引起内存泄露的对象
* 在Overview选项中，以饼状图的形式列举出了程序内存消耗的一些基本信息，其中每一种不同颜色的饼块都代表了不同比例的内存消耗情况。
* 如果说需要定位内存泄露的代码点，我们可以通过Dominator Tree菜单选项来进行排查


  

Thread Dump是诊断Java应用问题的工具
* thread dump文件主要保存的是java应用中各线程在某一时刻的运行的位置，即执行到哪一个类的哪一个方法哪一个行上
* 以stacktrace的方式显示。通过对thread dump的分析可以得到应用是否“卡”在某一点上，即在某一点运行的时间太长，如数据库查询，长期得不到响应，最终导致系统崩溃

Thread Dump抓取
* 一般当服务器挂起，崩溃或者性能低下时，就需要抓取服务器的线程堆栈（Thread Dump）用于后续的分析
* 需要接连多次做thread dump，每次间隔10-20s，建议至少产生三次 dump信息，如果每次 dump都指向同一个问题，我们才确定问题的典型性

* 操作系统命令获取ThreadDump
```
ps –ef | grep java
kill -3 <pid>
```
* JVM 自带的工具获取线程堆栈
```
jps 或 ps –ef | grep java （获取PID）
jstack [-l ] <pid> | tee -a jstack.log（获取ThreadDump）
```

## Thread Dump信息
```

1. "Timer-0" daemon prio=10 tid=0xac190c00 nid=0xaef in Object.wait() [0xae77d000] 

# 线程名称：Timer-0；线程类型：daemon；优先级: 10，默认是5；
# JVM线程id：tid=0xac190c00，JVM内部线程的唯一标识（通过java.lang.Thread.getId()获取，通常用自增方式实现）。
# 对应系统线程id（NativeThread ID）：nid=0xaef，和top命令查看的线程pid对应，不过一个是10进制，一个是16进制。（通过命令：top -H -p pid，可以查看该进程的所有线程信息）
# 线程状态：in Object.wait()；
# 起始栈地址：[0xae77d000]，对象的内存地址，通过JVM内存查看工具，能够看出线程是在哪儿个对象上等待；

2.  java.lang.Thread.State: TIMED_WAITING (on object monitor)
3.  at java.lang.Object.wait(Native Method)
4.  -waiting on <0xb3885f60> (a java.util.TaskQueue)     # ，然后释放该对象锁，进入waiting状态
5.  at java.util.TimerThread.mainLoop(Timer.java:509)
6.  -locked <0xb3885f60> (a java.util.TaskQueue)         # 对象先上锁，锁住对象0xb3885f60
7.  at java.util.TimerThread.run(Timer.java:462)
Java thread statck trace：是上面2-7行的信息。到目前为止这是最重要的数据，Java stack trace提供了大部分信息来精确定位问题根源
```

## 问题场景
* CPU飙高，load高，响应很慢
  1. 一个请求过程中多次dump；
  2. 对比多次dump文件的runnable线程，如果执行的方法有比较大变化，说明比较正常。如果在执行同一个方法，就有一些问题了

* 查找占用CPU最多的线程
  *  使用命令：top -H -p pid（pid为被测系统的进程号），找到导致CPU高的线程ID，对应thread dump信息中线程的nid，只不过一个是十进制，一个是十六进制；
  *  在thread dump中，根据top命令查找的线程id，查找对应的线程堆栈信息；

* CPU使用率不高但是响应很慢
  * 进行dump，查看是否有很多thread struck在了i/o、数据库等地方，定位瓶颈原因
* 请求无法响应
  * 多次dump，对比是否所有的runnable线程都一直在执行相同的方法，如果是的，恭喜你，锁住了

* 死锁: 线程 Dump中可以直接报告出 Java级别的死锁
* 热锁: 由于多个线程对临界区，或者锁的竞争,结合操作系统的各种工具观察系统资源使用状况，以及收集Java线程的DUMP信息，看线程都阻塞在什么方法上

---
# Linux命令
* 文本操作
```

grep yoursearchkeyword f.txt     #文件查找
grep 'KeyWord otherKeyWord' f.txt cpf.txt #多文件查找, 含空格加引号
grep 'KeyWord' /home/admin -r -n #目录下查找所有符合关键字的文件
grep 'keyword' /home/admin -r -n -i # -i 忽略大小写
grep 'KeyWord' /home/admin -r -n --include *.{vm,java} #指定文件后缀
grep 'KeyWord' /home/admin -r -n --exclude *.{vm,java} #反匹配

# cat + grep
cat f.txt | grep -i keyword # 查找所有keyword且不分大小写  
cat f.txt | grep -c 'KeyWord' # 统计Keyword次数

# seq + grep
seq 10 | grep 5 -A 3    #上匹配
seq 10 | grep 5 -B 3    #下匹配
seq 10 | grep 5 -C 3    #上下匹配，平时用这个就妥了
```

* 文本分析 - awk
```
awk '{print $4,$6}' f.txt
awk '{print NR,$0}' f.txt cpf.txt    
awk '{print FNR,$0}' f.txt cpf.txt
awk '{print FNR,FILENAME,$0}' f.txt cpf.txt
awk '{print FILENAME,"NR="NR,"FNR="FNR,"$"NF"="$NF}' f.txt cpf.txt
echo 1:2:3:4 | awk -F: '{print $1,$2,$3,$4}'

# 匹配
awk '/ldb/ {print}' f.txt   #匹配ldb
awk '!/ldb/ {print}' f.txt  #不匹配ldb
awk '/ldb/ && /LISTEN/ {print}' f.txt   #匹配ldb和LISTEN
awk '$5 ~ /ldb/ {print}' f.txt #第五列匹配ldb
```

* 文本处理 - sed
```
sed -n '3p' xxx.log #只打印第三行
sed -n '$p' xxx.log #只打印最后一行
sed -n '3,9p' xxx.log #只查看文件的第3行到第9行
sed -n -e '3,9p' -e '=' xxx.log #打印3-9行，并显示行号
sed -n '/root/p' xxx.log #显示包含root的行
sed -n '/hhh/,/omc/p' xxx.log # 显示包含"hhh"的行到包含"omc"的行之间的行

# 文本替换
sed -i 's/root/world/g' xxx.log # 用world 替换xxx.log文件中的root; s==search  查找并替换, g==global  全部替换, -i: implace

# 文本插入
sed '1,4i hahaha' xxx.log # 在文件第一行和第四行的每行下面添加hahaha
sed -e '1i happy' -e '$a new year' xxx.log  #【界面显示】在文件第一行添加happy,文件结尾添加new year
sed -i -e '1i happy' -e '$a new year' xxx.log #【真实写入文件】在文件第一行添加happy,文件结尾添加new year

# 文本删除
sed  '3,9d' xxx.log # 删除第3到第9行,只是不显示而已
sed '/hhh/,/omc/d' xxx.log # 删除包含"hhh"的行到包含"omc"的行之间的行
sed '/omc/,10d' xxx.log # 删除包含"omc"的行到第十行的内容

# 与find结合
find . -name  "*.txt" |xargs   sed -i 's/hhhh/\hHHh/g'
find . -name  "*.txt" |xargs   sed -i 's#hhhh#hHHh#g'
find . -name  "*.txt" -exec sed -i 's/hhhh/\hHHh/g' {} \;
find . -name  "*.txt" |xargs cat
```

# 文件操作
* 文件监听 - tail
```
最常用的tail -f filename
tail -f xxx.log # 循环监听文件
tail -300f xxx.log #倒数300行并追踪文件
tail +20 xxx.log #从第 20 行至文件末尾显示文件内容

# tailf使用
tailf xxx.log #等同于tail -f -n 10 打印最后10行，然后追踪文件

```
* 文件查找 - find
```
sudo -u admin find /home/admin /tmp /usr -name \*.log(多个目录去找)
find . -iname \*.txt(大小写都匹配)
find . -type d(当前目录下的所有子目录)
find /usr -type l(当前目录下所有的符号链接)
find /usr -type l -name "z*" -ls(符号链接的详细信息 eg:inode,目录)
find /home/admin -size +250000k(超过250000k的文件，当然+改成-就是小于了)
find /home/admin f -perm 777 -exec ls -l {} \; (按照权限查询文件)
find /home/admin -atime -1  1天内访问过的文件
find /home/admin -ctime -1  1天内状态改变过的文件    
find /home/admin -mtime -1  1天内修改过的文件
find /home/admin -amin -1  1分钟内访问过的文件
find /home/admin -cmin -1  1分钟内状态改变过的文件    
find /home/admin -mmin -1  1分钟内修改过的文件
```
* 批量查询vm-shopbase满足条件的日志 - pgm
```
pgm -A -f vm-shopbase 'cat /home/admin/shopbase/logs/shopbase.log.2017-01-17|grep 2069861630'

```
## 查看网络和进程

```
[root@pdai.tech ~]# ifconfig // 查看所有网络接口的属性
[root@pdai.tech ~]# iptables -L // 查看防火墙设置
[root@pdai.tech ~]# route -n
[root@pdai.tech ~]# netstat -lntp //netstat
[root@pdai.tech ~]# netstat -antp // 查看所有已经建立的连接
[root@pdai.tech ~]# netstat -s //查看网络统计信息进程
root@pdai.tech ~]# ps -ef | grep java // 查看所有进程
```
----
# JDK 命令行工具
* Java 调试入门工具 
  * jps 
  * jstack 
  * jinfo 
  * jmap 
  * jstat 
  * jdb 
  * CHLSDB 
* Java 调试进阶工具 
  * btrace 
  * Greys 
  * Arthas 
  * javOSize 
  * JProfiler 
* 其它工具
  * dmesg

* 标准终端类：jps、jinfo、jstat、jstack、jmap
* 功能整合类：jcmd、vjtools、arthas、greys

#  JDK 安装目录下的 bin 目录

* `jps (JVM Process Status）`: 类似 UNIX 的 ps 命令。用于查看所有 Java 进程的启动类、传入参数和 Java 虚拟机参数等信息；
* `jstat（JVM Statistics Monitoring Tool）`: 用于收集 HotSpot 虚拟机各方面的运行数据;
* `jinfo (Configuration Info for Java) `: Configuration Info for Java,显示虚拟机配置信息;
* `jmap (Memory Map for Java)` : 生成堆转储快照;
* `jhat (JVM Heap Dump Browser)` : 用于分析 heapdump 文件，它会建立一个 HTTP/HTML 服务器，让用户可以在浏览器上查看分析结果;
* `jstack (Stack Trace for Java)` : 生成虚拟机当前时刻的线程快照，线程快照就是当前虚拟机内每一条线程正在执行的方法堆栈的集合。
* 
## Jps
> jps是jdk提供的一个查看当前java进程的小工具， 可以看做是JavaVirtual Machine Process Status Tool的缩写。

查看所有 Java 进程

```
C:\Users\SnailClimb>jps
7360 NettyClient2
17396
7972 Launcher
16504 Jps
17340 NettyServer


jps # 显示进程的ID 和 类的名称
jps –l # 输出输出完全的包名，应用主类名，jar的完全路径名 
jps –v # 输出jvm参数
jps –q # 显示java进程号
jps -m # main 方法
jps -l xxx.xxx.xx.xx # 远程查看 
```

## jstack
> jstack是jdk自带的线程堆栈分析工具，使用该命令可以查看或导出 Java 应用程序中线程堆栈信息。

生成虚拟机当前时刻的线程快照
* 目的主要是定位线程长时间出现停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等都是导致线程长时间停顿的原因

```
jstack 2815

# java和native c/c++框架的所有栈信息
jstack -m 2815

# 额外的锁信息列表，查看是否死锁
jstack -l 2815


C:\Users\SnailClimb>jps
13792 KotlinCompileDaemon
7360 NettyClient2
17396
7972 Launcher
8932 Launcher
9256 DeadLockDemo
10764 Jps
17340 NettyServer

C:\Users\SnailClimb>jstack 9256

```

## jinfo
> 实时地查看和调整虚拟机各项参数

```
# 输出当前 jvm 进程的全部参数和系统属性 (第一部分是系统的属性，第二部分是 JVM 的参数)
jinfo vmid :

# 输出当前 jvm 进程的全部参数和系统属性
jinfo 2815

# 输出所有的参数
jinfo -flags 2815

# 查看指定的 jvm 参数的值
jinfo -flag PrintGC 2815

# 开启/关闭指定的JVM参数
jinfo -flag +PrintGC 2815

# 设置flag的参数
jinfo -flag name=value 2815

# 输出当前 jvm 进行的全部的系统属性
jinfo -sysprops 2815
```

## jmap
> 生成 java 程序的 dump 文件， 也可以查看堆内对象示例的统计信息、查看 ClassLoader 的信息以及 finalizer 队列

```
# 查看堆的情况
jmap -heap 2815

# dump
jmap -dump:live,format=b,file=/tmp/heap2.bin 2815
jmap -dump:format=b,file=/tmp/heap3.bin 2815

# 查看堆的占用
jmap -histo 2815 | head -10
```
## jhat
> 分析 heapdump 文件

jhat 用于分析 heapdump 文件，它会建立一个 HTTP/HTML 服务器，让用户可以在浏览器上查看分析结果
```
C:\Users\SnailClimb>jhat C:\Users\SnailClimb\Desktop\heap.hprof
Reading from C:\Users\SnailClimb\Desktop\heap.hprof...
Dump file created Sat May 04 12:30:31 CST 2019
Snapshot read, resolving...
Resolving 131419 objects...
Chasing references, expect 26 dots..........................
Eliminating duplicate references..........................
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.
```

## jstat
>  监视虚拟机各种运行状态信息

用于监视虚拟机各种运行状态信息的命令行工具。 它可以显示本地或者远程（需要远程主机提供 RMI 支持）虚拟机进程中的类信息、内存、垃圾收集、JIT 编译等运行数据

``
jstat -gc -h3 31736 1000 10 // 分析进程 id 为 31736 的 gc 情况，每隔 1000ms 打印一次记录，打印 10 次停止，每 3 行后打印指标头部。

``

## btrace
> 真是生产环境&预发的排查问题大杀器

## Greys
* `sc -df xxx`: 输出当前类的详情,包括源码位置和classloader结构

* `trace class method`: 打印出当前方法调用的耗时情况，细分到每个方法, 对排查方法性能时很有帮助。
