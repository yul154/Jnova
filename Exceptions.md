异常机制
 - 异常的层次结构
 - 异常基础
 - 异常实践

------
# 异常的层次结构

<img width="570" alt="Screen Shot 2021-12-19 at 10 39 24 AM" src="https://user-images.githubusercontent.com/27160394/146661656-08dbf66e-be41-4b3d-b6c3-bed1e4e9af54.png">


## Throwable

Throwable 是 Java 语言中所有错误与异常的超类。 

Throwable 包含两个子类：Error（错误）和 Exception（异常），它们通常用于指示发生了异常情况。

Throwable 包含了其线程创建时线程执行堆栈的快照，它提供了`printStackTrace()`等接口用于获取堆栈跟踪数据等信息

## Error(错误)

Error 类及其子类：程序中无法处理的错误，表示运行应用程序中出现了严重的错误

* 此类错误一般表示代码运行时 JVM 出现问题。
* 通常有`Virtual MachineError`(虚拟机运行错误)`NoClassDefFoundError`(类定义错误)等
* `OutOfMemoryError`：内存不足错误；`StackOverflowError`：栈溢出错误。此类错误发生时，JVM 将终止线程


## Exception(异常)
> 程序本身可以捕获并且可以处理的异常

可以通过 catch 来进行捕获。

Exception 这种异常又分为两类:运行时异常和编译时异常。

### 运行时异常

RuntimeException类及其子类异常，如NullPointerException(空指针异常)、IndexOutOfBoundsException(下标越界异常)等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。

这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。

### 非运行时异常(编译异常)

是RuntimeException以外的异常，类型上都属于Exception类及其子类。

从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。
* 如IOException、SQLException等以及用户自定义的Exception异常，一般情况下不自定义检查异常


可查的异常（checked exceptions）和不可查的异常（unchecked exceptions）

* 可查异常(编译器要求必须处置的异常):正确的程序在运行中，很容易出现的,情理可容的异常状况.可查异常虽然是异常状况，但在一定程度上它的发生是可以预计的，而且一旦发生这种异常状况，就必须采取某种方式进行处理
  * 除了RuntimeException及其子类以外，其他的Exception类及其子类都属于可查异常
  * 这种异常的特点是Java编译器会检查它，也就是说，当程序中可能出现这类异常，要么用try-catch语句捕获它，要么用throws子句声明抛出它，否则编译不会通过。

* 不可查异常(编译器不要求强制处置的异常)
  *  包括运行时异常（RuntimeException与其子类）和错误（Error）

----
# 异常语句

## Few Terms

* `try` – 用于监听。将要被监听的代码(可能抛出异常的代码)放在try语句块之内，当try语句块内发生异常时，异常就被抛出。 
* `catch` – 用于捕获异常。catch用来捕获try语句块中发生的异常。 
* `finally` – finally语句块总是会被执行。它主要用于回收在try块里打开的物力资源(如数据库连接、网络连接和磁盘文件)。
    * 只有finally块，执行完成之后，才会回来执行try或者catch块中的return或者throw语句，
    * 如果finally中使用了return或者throw等终止方法的语句，则就不会跳回执行，直接停止。

* `throw` – 用于抛出异常。
* `throws` – 用在方法签名中，用于声明该方法可能抛出的异常


## 异常的申明(throws)

若方法中存在检查异常，如果不对其捕获，那必须在方法头中显式声明该异常，以便于告知方法调用者此方法有异常，需要进行处理
```
public static void method() throws IOException, FileNotFoundException{
    //something statements
}
```

> 若是父类的方法没有声明异常，则子类继承方法后，也不能声明异常。

> 通常，应该捕获那些知道如何处理的异常，将不知道如何处理的异常继续传递下去。

> 传递异常可以在方法签名处使用 throws 关键字声明可能会抛出的异常。

**Throws抛出异常的规则：**
* 如果是不可查异常(unchecked exception)，即Error、RuntimeException或它们的子类，那么可以不使用throws关键字来声明要抛出的异常，编译仍能顺利通过，但在运行时会被系统抛出。
* 必须声明方法可抛出的任何可查异常(checked exception)。即如果一个方法可能出现受可查异常，要么用try-catch语句捕获，要么用throws子句声明将它抛出，否则会导致编译错误
* 仅当抛出了异常，该方法的调用者才必须处理或者重新抛出该异常。当方法的调用者无力处理该异常的时候，应该继续抛出，而不是囫囵吞枣
* 调用方法必须遵循任何可查异常的处理和声明规则。若覆盖一个方法，则不能声明与覆盖方法不同的异常。声明的任何异常必须是被覆盖方法所声明异常的同类或子类


## 异常的抛出(throw)
> 如果代码可能会引发某种错误，可以创建一个合适的异常类实例并抛出它，这就是抛出异常

```
public static double method(int value) {
    if(value == 0) {
        throw new ArithmeticException("参数不能为0"); //抛出一个运行时异常
    }
    return 5.0 / value;
}
```

有时我们会从 catch 中抛出一个异常，目的是为了改变异常的类型。多用于在多系统集成时，当某个子系统故障，异常类型可能有多种，可以用统一的异常类型向外暴露，不需暴露太多内部异常细节
```
catch (IOException e) {
        MyException ex = new MyException("read file failed.");
        ex.initCause(e);
        throw ex;
```

## 异常的自定义

定义一个异常类应包含两个构造函数
* 一个无参构造函数
* 一个带有详细描述信息的构造函数（Throwable 的 toString 方法会打印这些详细信息，调试时很有用）

```
public class MyException extends Exception {
    public MyException(){ }
    public MyException(String msg){
        super(msg);
    }
    // ...
}
```

## 异常的捕获

* try-catch
* try-catch-finally
* try-finally
* try-with-resource

**try-catch**
```
try {
        // code
    } catch (FileNotFoundException e) {
        // handle FileNotFoundException
    } catch (IOException e){
        // handle IOException
    }
```

**try-catch-finally**
```
 try {                        
    //执行程序代码，可能会出现异常                 
} catch(Exception e) {   
    //捕获异常并处理   
} finally {
    //必执行的代码
}
```
执行的顺序
* 当try没有捕获到异常时：try语句块中的语句逐一被执行，程序将跳过catch语句块，执行finally语句块和其后的语句
* 当try捕获到异常，catch语句块里没有处理此异常的情
    * 况当try语句块里的某条语句出现异常时，而没有处理此异常的catch语句块时，此异常将会抛给JVM处理，finally语句块里的语句还是会被执行，但finally语句块后的语句不会被执行；
* 当try捕获到异常，catch语句块里有处理此异常的情况：
    * 在try语句块中是按照顺序来执行的，当执行到某一条语句出现异常时，程序将跳到catch语句块，并与catch语句块逐一匹配，
    * 找到与之对应的处理程序，其他的catch语句块将不会被执行，
    * 而try语句块中，出现异常之后的语句也不会被执行，
    * catch语句块执行完后，执行finally语句块里的语句，最后执行finally语句块后的语句


**try-finally**

try块中引起异常，异常代码之后的语句不再执行，直接执行finally语句。 try块没有引发异常，则执行完try块就执行finally语句

* try-finally可用在不需要捕获异常的代码，可以保证资源在使用后被关闭
  * IO流中执行完相应操作后，关闭相应资源；
  * 使用Lock对象保证线程同步，通过finally可以保证锁会被释放；
  * 数据库连接代码时，关闭连接操作等等

finally遇见如下情况不会执行
* 在前面的代码中用了System.exit()退出程序。
* finally语句块中发生了异常。
* 程序所在的线程死亡。
* 关闭CPU。

finally语句块中的代码一定会被执行，常用于回收资源 。


**try-with-resource**
> 更优雅的方式来实现资源的自动释放，自动释放的资源需要是实现了 AutoCloseable 接口的类

try 代码块退出时，会自动调用 scanner.close 方法


## 常用的异常

**`RuntimeException`**
* `java.lang.ArrayIndexOutOfBoundsException` 数组索引越界异常。当对数组的索引值为负数或大于等于数组大小时抛出。 
* `java.lang.ArithmeticException` 算术条件异常。譬如：整数除零等。 
* `java.lang.NullPointerException` 空指针异常。当应用试图在要求使用对象的地方使用了null时，抛出该异常。譬如：调用null对象的实例方法、访问null对象的属性、计算null对象的长度、使用throw语句抛出null等等 
* `java.lang.ClassNotFoundException` 找不到类异常。当应用试图根据字符串形式的类名构造类，而在遍历CLASSPAH之后找不到对应名称的class文件时，抛出该异常。 
* `java.lang.NegativeArraySizeException`  数组长度为负异常 
* `java.lang.ArrayStoreException` 数组中包含不兼容的值抛出的异常 
* `java.lang.SecurityExceptio`n 安全性异常 
* `java.lang.IllegalArgumentException` 非法参数异常

**IOException**
* `IOException`：操作输入流和输出流时可能出现的异常。
* `EOFExceptio`n 文件已结束异常
* `FileNotFoundException` 文件未找到异常
----
# 异常实践

* 只针对不正常的情况才使用异常: Java 类库中定义的可以通过预检查方式规避的RuntimeException异常不应该通过catch 的方式来处理
* 在 finally 块中清理资源或者使用 try-with-resource 语句: 
* 尽量使用标准的异常: 
  * IllegalArgumentException 参数的值不合适 
  * IllegalStateException 参数的状态不合适
  *  NullPointerException 在null被禁止的情况下参数值为null
  *  IndexOutOfBoundsException	下标越界
  *  ConcurrentModificationException	在禁止并发修改的情况下，对象检测到并发修改
* 优先捕获最具体的异常 : 总是优先捕获最具体的异常类，并将不太具体的 catch 块添加到列表的末尾
* 不要捕获 Throwable 类: 如果在 catch 子句中使用 Throwable ，它不仅会捕获所有异常，也将捕获所有的错误
* 不要在finally块中使用return : try块中的return语句执行成功后，并不马上返回，而是继续执行finally块中的语句，如果此处存在return语句，则在此直接返回，无情丢弃掉try块中的返回点

----
# JVM处理异常的机制？

javap,一个用来拆解class文件的工具，和javac一样由JDK提供。
```
 Exception table:
       from    to  target type
           0     3    14   Class java/lang/Exception
           0     3    30   any
          14    19    30   any
```
异常表中包含了一个或多个异常处理者(Exception Handler)的信息，这些信息包含如下
* from 可能发生异常的起始点
* to 可能发生异常的结束点
* target 上述from和to之前发生异常后的异常处理者的位置
* type 异常处理者处理的异常的类信息


当一个异常发生时
1. JVM会在当前出现异常的方法中，查找异常表，是否有合适的处理者来处理
2. 如果当前方法异常表不为空，并且异常符合处理者的from和to节点，并且type也匹配，则JVM调用位于target的调用者来处理。
3. 如果上一条未找到合理的处理者，则继续查找异常表中的剩余条目
4. 如果当前方法的异常表无法处理，则向上查找（弹栈处理）刚刚调用该方法的调用处，并重复上面的操作。
5. 如果所有的栈帧被弹出，仍然没有处理，则抛给当前的Thread，Thread则会终止。
6. 如果当前Thread为最后一个非守护线程，且未处理异常，则会导致JVM终止运行。


> 异常是否耗时？为什么会耗时？

* 建立一个异常对象，是建立一个普通Object耗时的约20倍（实际上差距会比这个数字更大一些，因为循环也占用了时间，追求精确的读者可以再测一下空循环的耗时然后在对比前减掉这部分），
* 而抛出、接住一个异常对象，所花费时间大约是建立异常对象的4倍


