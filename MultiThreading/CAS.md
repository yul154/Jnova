## CAS
> CAS的作用
阻塞/唤醒这些操作，是非常消耗时间的

```
public static void increment() {
    do{
        int k = i;
        int j = k + 1;
    }while (compareAndSet(i, k, j))
}
```

1.线程从内存中读取 i 的值，假如此时 i 的值为 0，我们把这个值称为 k 吧，即此时 k = 0。
2. 令 j = k + 1。
3. 用 k 的值与内存中i的值相比，如果相等，这意味着没有其他线程修改过 i 的值，我们就把 j（此时为1） 的值写入内存；
4. 如果不相等（意味着i的值被其他线程修改过），我们就不把j的值写入内存，而是重新跳回步骤 1，继续这三个操作。

采用 CAS 这种机制的写法也是线程安全的，通过这种方式，可以说是不存在锁的竞争，也不存在阻塞等事情的发生，可以让程序执行的更好

### 能否保证 increment 是线程安全的呢
> 采用 CAS 这种机制的写法也是线程安全的，通过这种方式，可以说是不存在锁的竞争，也不存在阻塞等事情的发生，可以让程序执行的更好

这个`compareAndSet`操作，他其实只对应操作系统的一条硬件操作指令，尽管看似有很多操作在里面，但操作系统能够保证他是原子执行的

> CAS的底层实现

* CAS的实现就调用了`UnSafe`方法中的`compareAndSwap***`的系列native方法。
* 这些nativie方法其实在底层都是通过CPU指令实现的，因此CAS操作本身是很快的，这也是为什么CAS自旋的方式想必加锁能够提高性能的原因


### CAS的实现

在 Java 中，也是提供了这种 CAS 的原子类，例如：
```
AtomicBoolean
AtomicInteger
AtomicLong
AtomicReference
```



### ABA 问题
引入版本控制，
```
例如，每次有线程修改了引用的值，就会进行版本的更新，
虽然两个线程持有相同的引用，但他们的版本不同，
这样，我们就可以预防 ABA 问题了
```

Java 中提供了 AtomicStampedReference 这个类，就可以进行版本控制了。


### Java8 对 CAS 的优化。
> 由于线程太密集了，太多人想要修改 i 的值了，进而大部分人都会修改不成功，白白着在那里循环消耗资源

尝试使用分段CAS以及自动分段迁移的方式

Java 8推出了一个新的类，LongAdder
* Java 8推出了一个新的类，LongAdder
```
transient volatile long base;
transient volatile Cell[] cells; //初始值为 null，只有在对 base 执行 CAS 更新失败时（说明竞争激烈）才会用到 cells 这个数组
```


```
如果有 100 个线程要对 i 进行自增操作的话， 这个时候，冲突就会大大增加，
系统就会把这些线程分配到不同的 cell 数组元素去，
假如 cell[10] 有 10 个元素吧，且元素的初始化值为 0，
那么系统就会把 100 个线程分成 10 组，每一组对 cell 数组其中的一个元素做自增操作，
这样到最后，cell 数组 10 个元素的值都为 10，系统在把这 10 个元素的值进行汇总，进而得到 100，二这，就等价于 100 个线程对 i 进行了 100 次自增操作
```