# Basic Java

## 标识符
* Java程序的各个组成部分都需要名字。类名、变量名、方法名、方法参数名等都被称为标识符
* 都应该以字母（A-Z或者a-z）,美元符（$）、或者下划线（_）开始
* 可以是字母（A-Z或者a-z）,美元符（$）、下划线（_）和数字的组合
* 自带的关键字不能用作标识符
* 大小写敏感的

## 注释
* 单行注释：在注释内容前加两个斜线//，则Java编译器会忽略掉//的信息
* 多行注释：在要注释的内容前面添加/*，在注释的内容后添加*/
* 文档注释：在要注释的内容前面添加/**，在注释的内容后添加*/，注释中的内容可以用以生成程序的文档

## 面向对象
* 对象有状态(变量)和行为(方法)
  * 局部变量与成员变量不同，它不属于某个对象，是一个临时变量，当方法执行结束，变量就不再起作用了。一个方法中声明的变量都属于局部变量
  * 在`main`函数里定义的变量都是局部变量，和类的成员变量不一样
* 类：描述一类对象的状态和行为的模板
```
public class Post {# public:表示外部可以访问这个类
    String title; // 成员变量
    String content; // 成员变量
    
    // 成员方法
    void print() {# void:没有返回值和参数的方法
        System.out.println(title);
        System.out.println(content);
    }
}
```
* 创建的实例，使用实例都放在 main函数里
```
public class HelloWorld {  
    
    public static void main(String[] args) {  
        Post post = new Post(); #声明对象的类型为Post，并进行命名，代码中命名为post
                                # post变量是函数内的局部变量
    }
}
```

* 创建对象不一定放在类本身，可以放在别的类里
* package: 组织类的一种方式

# 变量与数据类型
* Java要求在使用一个变量前，必须声明它的类型（向计算机申请内存）
* 可以一次声明一个或多个同一类变量,并在声明同事，进行初始化赋值
```
int a=1,b,c=2:
```
## 两类数据类型
### 基本数据类型
* 八种基本类型 六种数字类型（四种整数型，两种浮点型），一种字符类型，还有一种布尔型。
1.`byte`: 八位，有符号的整数(正负)
 * 最值 -128（-2^7）
 * 默认值 0
 * 主要替代整数，占用空间是`int`的四分之一
2.`short`：16位，有符号整数
 * 最值 -32768（-2^15）
 * 默认值 0
 * 主要替代整数，占用空间是`int`的二分之一
3.`int`：32位，有符号整数
 * 最值 -2,147,483,648（-2^31）
 * 默认值 0
 * 整型变量默认为`int`
4. `long`： l是64位、有符号整数
 *  最小值是-9,223,372,036,854,775,808(-2^63)
 *  最大值是9,223,372,036,854,775,807(2^63 -1)
 *  默认值是0L
 *  这种类型主要使用在需要比较大整数的系统上
5. `float`：单精度，32位的浮点数
 *  float在储存大型浮点数组的时候可节省内存空间
 *  默认值是0.0f
 *  浮点数不能用来表示精确的值，如货币
6 `double`:双精度，64位的浮点数
 * 浮点数的默认类型为double类型
 * 默认值是0.0d
 * double类型同样不能表示精确的值，如货币
7. `boolean`:表示一位信息
 * 取值：`True` 和`False`
 * 默认值：`False`
8. `char`:一个单一的16位Unicod字符
 * 最小值是'\u0000'（即为0）
 * 最大值是'\uffff'（即为65,535）
 * 默认值是'\u0000'（即为0）
 * char数据类型可以储存任何字符
### 类型转换
* 一个浮点数字默认是`double`，如果定义`float`则要在字面最后添加f或F
```
double a=1.23;
float b=12.3F;
float c=1.23# wrong
```
* 整数默认是`int`,如果定义长整型，则要在字面后面加l or L
```
int a=100;
long b =10000L;
long c= 10000# it's not wrong,低级到高级的自动类型转换
```
* 低级可以自动转高级，高级需要强行转换成低级
```
byte b
int i =b
long l=b
int i=l # wrong
i=(int)b#强转
```
### 数组
* 描述一系列同类型的数据，一旦建立，长度固定
```
int[] anArray;
anArray = new int[10];#初始化长度
int[] anArray = { 100, 200, 300, 400, 500, 600, 700, 800, 900, 1000}
anArray.length
```
### String
* 一个字符的序列,不是基本数据类型，是一个类
```
String str="abc";
```
* 字符串和基本数据类型也能通过+进行拼接操作
```
int a=100；
String str ="a="+a
```
* String是个不可变的对象
### 控制台输入
* `Scanner` 对象
```
Scanner scanner=new Scanner(System.in)
```
* `Scanner`可以读取用户在命令行输入的各种数据类型
 1. `nextInt()`方式读取整数
 2. `nextFloat()`读取浮点型
 3. `next()`读取一个字符串
 4. `nextLine()`读取完整的一行（回车之前的所有）
 
 
# 运算符
* `=`:对对应的引用进行拷贝
```
Blog blog=new Blog
Blog new_blog=blog# 指向同一个对象
```
* `byte`,`short`,`char`进行数学运算，会自动转换成`int`
* 不同类型的数据进行运算，会自动转换为“更高级别”的类型
* `==`和`!=`作为关系运算符，只用于对象的引用（存放地址），如果判断对象实际内容是否相同，需要调用`equals()`方法
## 逻辑运算符
 1 `&&`,`||`,`!`:与或非
## 位运算符：二进制的位，可应用于整数类型(int)，长整型(long)，短整型(short)，字符型(char)，和字节型(byte)等类型，运算时会将其对应Bit位（0或者是1）进行布尔代数运算或者移动操作。
<img width="1010" alt="Screen Shot 2019-05-03 at 3 23 23 PM" src="https://user-images.githubusercontent.com/27160394/57160661-702dc000-6db7-11e9-986e-d045a935dc53.png">
* `>>` 和`>>>`区别：右移位运算符>>，若操作的值为正，则在高位插入0；若值为负，则在高位插入1；右移补零操作符>>>，无论正负，都在高位插入0。
## 三元运算符
```
# booleanExpression ? valueWhenTrue : valueWhenFalse
if (x>=0){
   y=x;
} else {
  y=-x;
}
y= x>=0? x:-x;
```
## 类型转换符号
```
int a=10;
long b=(long)a;
long c=(long)100;
int d =int(10.9)#10
```
## 运算优先级
<img width="557" alt="Screen Shot 2019-05-03 at 3 30 24 PM" src="https://user-images.githubusercontent.com/27160394/57160981-65bff600-6db8-11e9-9c06-ce30b6933822.png">

# 程序的控制流
1. 顺序控制
2. 循环控制(while, do/while,for)
3. 选择控制(if,switch)
## 循环控制
### do/while
> 即使不满足条件，也要至少执行一次
```
do{
  //loop
}while(condition);
```
### for loop
```
#for (initial value, condition, update){
# }
int sum=0;
for (int i=0,i<10,i++){\
    sum+=i}
    
String[] sentences = {"hello", "thank u", "thank u very much"};
for (String sentence : sentences) {
    System.out.println(sentence);
}
```
## 控制流
### if 
1. only if
```
if (i==1){
   i++}
```
2. if else
```
if(i==1){
  i++;
}else{
  i--;
}
```
3. else if 
```
if(i==1){
  i++;
}else if (i==2){
  i--
}else{
  i=0
}
```
### swith
* 可以判断一个变量和一系列值中某个值是否相等，
```
int sum = 0;
for(int i = 0; i <= 100; i++) {
    int result = i % 2;
    switch (result) {
        case 0:
          sum += i;
          break;
        case 1:
          break;
    }
}
```
* `swith`中变量只能为String、byte、short、int或者char
* switch语句可以拥有多个case语句，每个case后面跟一个要比较的值和冒号
* case语句中的值的数据类型必须与变量的数据类型相同，而且只能是常量或者字面常量。
* 直到break语句出现才会跳出switch语句。如果没有break语句出现，程序会继续执行下一条case语句，直到出现break语句。
* switch语句可以包含一个default分支，该分支必须是switch语句的最后一个分支。default在没有case语句的值和变量值相等的时候执行。default分支不需要break语句。

# 定义类
* 通常一个类一个.java文件，如果一个.java文件有多各类，只可能有一个public，而且文件名和public类一样
* 如果文件的所有类都不是public，那么文件名和任意一个类相同即可
## 成员变量
* private：只有该类自身可以访问
* protected：表示只有该类自身及其子类，以及与该类同一个包的类，可以访问该成员变量
* public：表示任何类都可以直接访问该成员变量
* 没有修饰：表示同一个包的类可以访问该成员变量
* 成员变量的声明
```
private String title
private Engine eng#引用变量
```
## 方法
* 关键词和变量一样
* 方法可以有输入和输出(必须有return)
 ```
 class Calculator {
    public int add (int a, int b) {
        return a + b;
    }
}
 ```
 ### overriding,overloading
* overloading:方法名相同，但参数不同，调用方法时通过传递给它们的不同参数个数和参数类型来决定具体使用哪个方法
* 两个方法仅仅是返回类型不一样不能构成方法重载，事实上还会引发编译错误。假设Car有两个run方法
* overriding: 如果在子类中定义某方法与其父类有相同的名称和参数，我们说该方法被重写 

### 构造器
>用于创造对象，和Python中的__init__差不多
* 与普通方法的区别
1. 方法名称必须和类名一样
2. 不允许定义返回类型
* 构造器重载：可以定义构造器的参数
* 如果一个类的方法或者构造器中的参数与自己的成员变量重名的时候，就可以使用this来进行区别。其它大部分情况下，实际上并不需要使用this
* 当一个类有多个构造器时，一个构造器调用另外一个构造器，可以使用this。

## 类
### 栈和堆
* 在方法中定义的基本类型变量和引用类型变量，其内存分配在栈上，变量出了作用域（即定义变量的代码块）就会自动释放
* 堆内存主要作用是存放运行时通过new操作创建的对象
```
 Car myCar（作为变量，存在栈中） = new（作为对象，把对象本身存在了堆中） Car();
```
* 一个对象的成员变量，如果是引用类型的变量的话，则该成员变量可以引用到堆中的其它对象
* 堆中的对象如果没有任何变量引用它们时，Java就会适时地通过垃圾回收机制释放这些对象占据的内存
* 基本类型变量的值就是存储在栈中，作用域结束（比如main方法执行结束）则这些变量占据的栈内存会自动释放
### 访问对象
#### 访问属性
* 在类的内部可以访问自身的属性，即在类的内部可以通过this来访问自身的属性。
* 在外部（即其它类中）也可以访问一个类的非private属性，通过对象名.属性名的方式进行访问。
#### 访问方法
* 方法可以在一个类内部进行调用
* 在外部（即其它类中）也可以访问一个类的非private方法，通过对象名.方法名的方式进行访问。
* 具有某个返回类型的方法，其返回结果可以用赋值操作赋给该类型的变量。

#### 参数
* 形参是方法定义中出现的参数，用于接收外部传入的变量
```
public int add(int a, int b)
```
* 实参即方法调用时传入的实际参数
```
int c = calculator.add(1, 3)
```
* 传参即是实参的值赋给形参。对于基本类型的形参，在方法内部对形参的修改只会局限在方法内部，不会影响实参。
* 引用类型的实参传入方法中时，是将对象的引用传入，而非对象本身
* 在方法结束时，形参占据的内存虽然会被释放，但是通过形参对对象进行的修改则不会丢失，因为对象依然保存在堆中。
* 虽然实参指向的对象可以在方法调用时被修改，但是实参本身的值（你可以认为是实参引用的对象地址）不会发生改变

### 成员变量初始化
* 可以通过调用final修饰的方法来进行赋值
* 通过初始化构造块来，编译器会将初始化构造块的代码会自动插入到在每个构造器中

# String
* 定义方式
 ```
 String str1;#str1的值为null，表示没有指向任何字符串对象
 String str2="";# 已经指向了一个字符串对象，这个对象的字符序列内容为空
 String str3= new String();# 调用构造函数创建字符串对象，该对象的字符序列内容为空，与第二行代码是基本一样的。
 ```
* `String` 和 `char` 区别
 1. char表示字符，定义时用单引号，只能存储一个字符，如char c=’x’;String表示字符串，定义时用双引号，可以存储一个或多个字符，如String name=”tom”;
 2. char是基本数据类型，而String 是一个类，具有面向对象的特征，可以调用方法，如name.length()获取字符串的长度
 ## Format 方法
 ```
String name = "David";
int age = 18;
String hobby = "篮球";
# regular way
String description = "我的名字是" + name;
System.out.println(description+ "，我今年" + age + "岁"+"，我的爱好是" + "篮球");

# format way
String formatString = "我的名字是%s，我今年%d岁，我的爱好是%s";
String output = String.format(formatString, name, age, hobby);
System.out.println(output);
 ```
 <img width="1013" alt="Screen Shot 2019-05-03 at 6 46 45 PM" src="https://user-images.githubusercontent.com/27160394/57169298-d9233100-6dd3-11e9-90a6-60f4219acd26.png">
 
## 常规操作
### equals()与==的区别
* 如果两个字符串变量指向的字符序列内容完全一样，equals()返回true；
* 如果两个字符串变量指向同一个对象，==返回true。 
### String的查找
<img width="1038" alt="Screen Shot 2019-05-03 at 7 06 43 PM" src="https://user-images.githubusercontent.com/27160394/57169751-a464a900-6dd6-11e9-8dcb-01a3787141b4.png">
### String 替换
* 注意每一个修改String对象的操作，都会创建一个新的String对象，最初的String对象则未发生任何变化。
* `String replace(char oldChar，char newChar)`
*  `String substring(int beginIndex，int endIndex)`: 返回一个新的字符串，它从下标beginIndex开始到endIndex-1的子串
*  `String trim()`: 把字符串前后的空格都删除，返回新串

### `StringBuffer`
> 表示可变长的和可修改的字符序列
* 我们可以`StringBuffer`进行插入或者追加字符序列、翻转字符序列等操作。
* 构建
  1.`StringBuffer()`：默认的构造方法预留16个字符的空间
  2. `StringBuffer(int size)`：第二种形式接收一个整数参数，显示的设置缓冲区的大小
  3. `StringBuffer(String str)`：第三种形式接收一个String参数，设置StringBuffer对象的初始内容，同时多预留16个字符的空间
  
# 静态变量和静态方法
* 希望定义一个类成员，使其作为该类的公共成员，所有实例都共享该成员变量
* 需要使用static关键字，可以修饰变量和方法
* 与“静态”对应的就是“实例”，因为“实例“都是程序在运行时动态生成的
## static 修饰变量
* 对于普通成员变量，每创建一个该类的实例就会创建该成员变量的一个拷贝，分配一次内存。由于成员变量是和类的实例绑定的，所以需要通过对象名进行访问，而不能直接通过类名对它进行访问。
* 对于静态变量在内存中只有一份，Java虚拟机（JVM）只为静态变量分配一次内存
* 与类的实例无关，因而可以直接通过类名访问这类变量
* 当修改static变量的值时，所有实例的count值都会改变
## 修饰方法
* 仅能调用其他的static方法
* 只能访问static数据
* 不能以任何方式引用this或super
* 这里我们是通过类名.方法的方式访问静态方法
## 修饰代码块
* 每个代码块只能执行一次
* 按照它们在类中出现的顺序依次执行它们
* 我们可以利用静态代码块可以对一些static变量进行赋值

# 泛类
> 我们可以把类型作为参数传入到泛型类中
```
public class Point<T> {
    private T x;
    private T y;

    public T getX() {
        return x;
    }

    public void setX(T x) {
        this.x = x;
    }

    public T getY() {
        return y;
    }

    public void setY(T y) {
        this.y = y;
    }
}
```
* 传入的类型参数不能是原生类型，必须是引用类型
```
Point<Integer> point1 = new Point<Integer>();
```
* 泛型类支持多个类型参数
```
public class Triple<A, B, C> {
    private A a;
    private B b;
    private C c;

    public A getA() {
        return a;
    }
   }
```
* 与泛型类不同的是泛型方法需要在方法返回值前用尖括号声明泛型类型名，这样才能在方法中使用这个标记作为返回值类型或参数类型。
```
public static <T> void printArray(T[] objects) {
        if (objects != null) {
            for(T element : objects){  
                System.out.printf("%s",element);  
            }  
        }
```
# Java Container
> 组织和管理一组对象
<img width="376" alt="Screen Shot 2019-05-03 at 8 57 30 PM" src="https://user-images.githubusercontent.com/27160394/57171810-27413000-6de6-11e9-83e4-3ddafb303535.png">

## ArrayList
```
private static ArrayList<Post> posts = new ArrayList<Post>();
```
* `ArrayList`就是Java提供给我们使用的一个集合类，用以保存一个元素序列，并且可以进行元素的访问、插入和删除等操作
* 可以使用迭代器Iterator类来完成遍历。Iterator主要有两个方法，基于这两个方法就能进行遍历操作
```
  public static void remove(long id) {
    Iterator<Post> iterator = posts.iterator();
    while (iterator.hasNext()) {
        Post post = iterator.next();
        if (post.getId() == id) {
            posts.remove(post); 
            return;
        }
    }
}
```
## Map
```
Map<Long, Post> newMap = new HashMap<Long, Post>();
```
* Map和List一样是一种接口
* `map`的实现HashMap类，是我们最常使用的一种容器
* Map具有两个泛型参数，第一个是键的类型，第二个是值的类型。类型不能是原生类型，必须是引用类型
* 我们可以获取键、值或键值对的集合，分别使用`keySet`, `values`以及`entrySet`
* `getAll`方法就通过`postsMap.values()`获取所有的值
* `addAll`方法，这个方法是所有容器都有的一个方法，可以把另外一个容器中的元素加入其中
