* Java的关键字

## 说下static吧，什么情况下用static
* static变量: 如果是相同数据，且对象不需要做修改，只需要使用，且不需要存储在对象中， : Every variable has a different value for different object. 
```
Use instance variables when : Every variable has a different value for different object. E.g. name of student, roll number etc..
use static variables when : The value of the variable is independent of the objects (not unique for each object). E.g. number of students.
```
* static方法 
  * 如果一个函数不需要访问非静态成员变量, 一般在进行初始化操作时，比如读取配置文件信息，获取当前服务器参数等
  * 静态方法不需要访问对象状态， 其所需参数都是通过显式参数提供(例如: Math.pow ) 0 
  * 一个方法只需要访问类的静态域(例如: Employee.getNextldh
  * 静态方法还有另外一种常见的用途。 类似 LocalDate 和 NumberFormat 的类使用静态工 厂方法 来构造对象。
* static代码块
* static内部类
* static包内导入

* static variable A 可以访问 instance variable B吗，反之呢
```
静态方法只能访问静态成员，实例方法可以访问静态和实例成员.
是因为实例成员变量是属于某个对象的，而静态方法在执行时，并不一定存在对象。
而非static的成员是在创建对象，即new 操作的时候才初始化的；类加载的时候初始化static的成员，此时static 已经分配内存空间，所以可以访问；非static的成员还没有通过new创建对象而进行初始化，所以必然不可以访问。
在一个类的静态成员中去访问非静态成员之所以会出错是因为在类的非静态成员不存在的时候静态成员就已经存在了，访问一个内存中不存在的东西当然会出错。

```
* static修饰的变量在java内存结构中是怎样的
```
JVM内存总体一共分为了 
4个部分(stack segment、heap segment、code segment、data segment) 
当我们在程序中，申明一个局部变量的时候，此变量就存放在了 stack segment(栈)当中； 
当new 一个对象的时候，此对象放在了heap segment(堆)当中； 
而static 的变量或者字符串常量 则存在在 data segment(数据区)中； 
那么类中方法的话，是存在在 code segment(代码区)中了。

```
* 自动拆箱装箱
```
自动装箱就是Java自动将原始类型值转换成对应的对象
自动装箱时编译器调用valueOf将原始类型值转换成对象，同时自动拆箱时，编译器通过调用类似intValue(),doubleValue()这类的方法将对象转换成原始类型值。
The autoboxing specification requires that boolean, byte, char <= 127, short, and int between -128 and 127 are wrapped into fixed objects.
包装类提供了对象的缓存，具体的实现方式为在类中预先创建频繁使用的包装类对象，当需要使用某个包装类的对象时，如果该对象封装的值在缓存的范围内，就返回缓存的对象，否则创建新的对象并返回。
```
* String类，字符串常量池的相关知识
```

```
* 类、抽象类、接口的区别
```
```
* equals和== hashcode和equals关系（为什么重写）
```

```
* Integer 包装类底层是怎么实现的?
```
Java会通过自动装箱机制将int类型值转为Integer类型。自动装箱是指在编译时期编译器自动调用包装类型中的valueOf()方法，进行自动的转换。
```

* 深拷贝和浅拷贝？
```
引用拷贝 : 创建一个指向对象的引用变量的拷贝,地址值是相同的，指向了一个相同的对象.
对象拷贝: 创建对象本身的一个副本,地址是不同的，也就是说创建了新的对象
深拷贝和浅拷贝都是对象拷贝
浅拷贝: 被复制对象的所有变量都含有与原来的对象相同的值, 所有的对其他对象的引用仍然指向原来的对象,即对象的浅拷贝会对“主”对象进行拷贝，但不会复制主对象里面的对象,里面的对象“会在原来的对象和它的副本之间共享。
浅拷贝仅仅复制所考虑的对象，而不复制它所引用的对象

深拷贝：深拷贝是一个整个独立的对象拷贝，深拷贝会拷贝所有的属性,并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝
利用序列化实现深拷贝
继续利用 clone() 方法，既然 clone() 方法，是我们来重写的，实际上我们可以对其内的引用类型的变量，再进行一次 clone()。

浅拷贝：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝，此为浅拷
深拷贝：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷贝。
```
* 类的加载过程？
* 类加载器有哪些？双亲委派模型？有什么作用？
