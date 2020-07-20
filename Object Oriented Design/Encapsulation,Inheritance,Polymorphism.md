# 封装
* 好处
1. 代码使用者无需考虑实现细节就能直接使用它，同时不用担心不可预料的副作用，别人不能随便修改内部结构
2. 在外部接口保持不变的情况下，自己可以修改内部的实现
##访问控制关键词
* 类
  1. `public`:任何类都可以访问
  2. `private`:只能用于修饰内部静态类
* 成员变量和方法
  1. public:其他任何类都可以访问该成员
  2. protected:只有继承自己的子类才能访问该成员
  3. private:除自己外其他任何类都不能访问该成员
  4. 默认情况，如果没有任何访问控制修饰符，则表示相同包内的类可以访问该成员
  
# 继承
* 继承是一种类和类之间的关系
* 继承编码的是一个 is-a的关系
* 一般情况下把共性的结构和行为放到父类中，子类可以通过继承复用父类中的代码，并且根据自己的需要进行扩展
* 在Java中，使用extends关键字表示继承关系
## 构造函数
* 生成子类对象或者实例时，Java默认地首先调用父类的不带参数的构造方法，接下来再调用子类的构造方法，生成子类对象
* `super`表示对父类对象的引用
* 在子类的构造函数中，一般第一条语句是super();，表示调用父类构造函数
* 也可以调用父类有参数的构造函数，比如super(name)
###方法覆盖
* 如果子类中有和父类中非private的同名方法，且返回类型和参数表也完全相同，就会覆盖从父类继承来的方法
* 当两个方法形成重写关系时，可以在子类中通过`super`关键字调用父类被重写的方法

# `final`关键字
> 一个变量可以声明为final，这样做的目的是阻止它的内容被修改
* 这意味着在声明`final`变量的时候，必须初始化它
##`final`修饰方法
* 被final修饰的方法可以被子类继承，不能被子类的方法覆盖
* `final`不能修饰构造方法
* 所有有`private`限制的成员方法默认也是`final`的

## 修饰类
由final修饰的类是不能继承的

# 抽象类
>除了不能实例化对象之外，类的其它功能依然存在
* 抽象类不能实例化对象，所以抽象类必须被继承，才能被使用
## 抽象方法
* 抽象方法只包含一个方法名，而没有方法体
* 抽象方法没有定义，方法名后面直接跟一个分号，而不是花括号。
* 如果一个类包含抽象方法，那么该类必须是抽象类。
* 任何子类必须重写父类的抽象方法，否则就必须声明自身为抽象类

# 接口
> 一组抽象方法的集合
* 接口使用interface关键字进行定义
* 接口中定义的方法没有方法体，它们以分号结束
* 接口也和抽象类一样，无法被实例化，但是可以被实现
* 一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类
* 接口访问权限: public权限和默认权限
  1.如果接口的访问权限是public的话，所有的方法和变量都是public。
  2. 默认权限则同一个包内的类可以访问。
* 一个接口能继承另一个接口，和类之间的继承方式比较相似。接口的继承使用extends关键字
## 接口实现
* 在类声明中，`implements`关键字放在`class`声明后面实现接口
* 接口支持多重继承，即一个类可以同时实现多个接口
* 可以使用接口类型来声明一个变量，那么这个变量可以引用到一个实现该接口的对象

# 异常
## `try/catch`
* 可能出现异常的代码通过`try/catch`代码进行了处理，当异常发生时，系统能够继续运行，而没有意外终止
* 当try代码块中的语句发生了异常，程序就会跳转到catch代码块中执行，执行完catch代码块中的程序代码后，系统会继续执行catch代码块之后的代码
* 每个try语句必须有一个或多个catch语句对应
## `throws`
* 需要调用别人提供的方法时，我们方法参数列表后面增加一个throws关键字，增加这个方法可能抛出的异常，这种情况下调用者就必须使用try/catch进行处理了
* 可以在程序代码所在的函数（方法）声明后用throws声明该函数要抛出异常，将该异常抛出到该函数的调用函数中。
## `Exception`
* Exception类是所有异常类的父类
* 自定义异常类只需继承Exception类即可
  1. 创建自定义异常类
  2. 在方法中通过throw关键字抛出异常对象
  3. 如果在当前抛出异常的方法中处理异常，可以使用try-catch语句捕获并处理；否则在方法的声明处通过throws关键字指明要抛出给方法调用者的异常，继续进行下一步操作
  4. 在出现异常方法的调用者中捕获并处理异常
* `finally`语句中的代码块不管异常是否被捕获总是要被执行的
* `finally`中的代码块不能被执行的唯一情况是：在被保护代码块中执行了`System.exit(0)`

# Java IO
> 一套Java用来读写数据（输入和输出）的API
* 从外部（包括磁盘文件、键盘、网络套接字）读入到内存中的数据序列称为输入流
* 从内存写入到外部设备的数据序列称为输出流。
* 流的分类
  1. 字节流：数据流中最小的数据单元是字节，多用于读取或书写二进制数据
  2. 字符流：数据流中最小的数据单元是字符， Java中的字符是Unicode编码，一个字符占用两个字节
* 在最底层，所有的输入/输出都是字节形式的。基于字符的流只为处理字符提供方便有效的方法。
## 字节流
* 字节流的最顶层是两个抽象类：`InputStream`和`OutputStream`，其他关于处理字节的类都是它们的子类
* 字节流的每次操作都是一个数据单位——字节
* `in.read()`每次从输入流中读取一个字节，如果达到文件末尾就返回-1
* 用完输入输出流，一定调用close()方法将其关闭
## 字符流
* 字符流用于处理16位 unicode 的输入和输出
* 字符流的两个顶层抽象类是`Reader`和`Writer`
* 字符流操作的最小单位是一个字符16 bits，字节流是一个字节8 bits
* 从控制台读入单个字符我们用了read()方法，读入字符串则可以使用readLine()方法
## 文件的输入输出
* 两个流是`FileInputStream`和`FileOutputStream`
```
InputStream f = new FileInputStream("D:/java");
File f = new File("D:/java");
InputStream f = new FileInputStream(f);
```

# 反射
> 动态获取信息以及动态调用对象方法的机制
## 功能
* 在运行时判断任意一个对象所属的类；
* 在运行时构造任意一个类的对象；
* 在运行时判断任意一个类所具有的成员变量和方法；
* 在运行时调用任意一个对象的方法；
* 生成动态代理。
## 获取
* 任何一个类都有`getClass()`方法，调用该方法即可获得一个表示类信息的对象Class。
* 获取类的三种方式
  1. `class1 = Class.forName("com.tianmaying.ReflectionTest") # Class.forName()中传入的参数就是类的全限定名`
  2. `class2 = new ReflectionTest().getClass()`
  3. `class2 = new ReflectionTest().getClass()`
* 通过反射可以获取一个类的父类以及它实现的接口
```
Class<?> parentClass = clazz.getSuperclass();
Class<?> intes[] = clazz.getInterfaces();
```
###  实例化类对象
* 调用Class的`newInstance()`方法，这会调用类的默认构造方法 
```
User user = (User) class1.newInstance();
```
* 先取得全部的构造函数，然后使用构造函数创建对象
```
 Constructor<?> cons[] = class1.getConstructors();
```
### 获取类的信息
* 获取类的全部属性
```
Field[] field = clazz.getDeclaredFields();
int mo = field[i].getModifiers();#  返回int类型值表示该字段的修饰符
String priv = Modifier.toString(mo);
Class<?> type = field[i].getType();#获取属性的类型
```
* 获取类的public属性
```
Field[] filed1 = clazz.getFields();
```
* 通过Field对象，
  1. 可以改变对应属性的可见性，
  2. 可以通过set()方法进行属性的赋值
* 获取类的方法
```
Method method[] = clazz.getMethods();# 获取类的所有方法
Class<?> returnType = method[i].getReturnType();#获取方法返回类型
Class<?> para[] = method[i].getParameterTypes();#获取方法参数列表
int temp = method[i].getModifiers();#获取方法修饰符
Class<?> exce[] = method[i].getExceptionTypes();#获取方法抛出的异常列表
```
* 调用类的方法
```
method = clazz.getMethod("reflect", int.class, String.class);
method.invoke(clazz.newInstance(), 20, "张三");
```

# 日前处理
* `java.time`包中有一组全新的时间日期API
* `Clock`类提供了访问当前日期和时间的方法
  * `Clock`是时区敏感的，可以用来取代`System.currentTimeMillis()`
* `ZoneId`表示时区
  * 时区可以很方便的使用静态方法`of`来获取到。
* `LocalTime`定义了一个没有市区信息的时间
* `LocalDate`表示了一个确切的时间
* `LocalDateTime`同时表示了时间和日期
* `LocalDateTime`与`Date`之间的互相转换
```
Date date = new Date();
Instant instant = Instant.ofEpochMilli(date.getTime());
LocalDateTime ldt = LocalDateTime.ofInstant(instant, ZoneId.systemDefault());

LocalDateTime ldt = LocalDateTime.now();
ZonedDateTime zdt = ldt.atZone(ZoneId.systemDefault());
Date date = Date.from(zdt.toInstant());
```
