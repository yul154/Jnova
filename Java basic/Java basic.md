# What is Java
## Java is compiled programming language,
1. Java source code is compiled down to bytecode by the Java compiler(`.class`), The bytecode is executed by a Java Virtual Machine (JVM)
2. The byte code is then compiled and/or interpreted(JVMS) to native instructions understood by hardware CPU on the fly at runtime.
## Java Identifiers
* Java is case sensitive
```
MyClassName
myclassName
```
* Camal Casing
  1.capitalize the very first letter of every word.(No spaces)
  2. a lowercase first letter and then the camel casing starts after the first word.
```
MyNewClass
myMethodName
```
* Class Name and File Name should be same in Java

# Basic java commands
<img width="1081" alt="Screen Shot 2019-06-14 at 11 45 06 AM" src="https://user-images.githubusercontent.com/27160394/59521305-ff8bc000-8e99-11e9-9572-93cbe749729b.png">
* The main method is the wrapper that tells your computer which lines of code to run

## Comments
* Block comments
```
/*
* MULTI LINE COMMNENTS
*/
```
* Line comments
```
System.out.println("hello, worlld");// this is a comments	
```
* JavaDoc comments
```
/** */
```
## Package
> A group of related classes 
### Naming Conventor
* All lowercase
* User reversed domain name
* Add furthe qualifiers to assur uniqueness with a company/group

### Determining type's package
* Don't need t be qualified
  1. Types in current package
  2. Tyeps in the java.lang package
### Access to package content

Modifier|Visibility|Usable on Types| Usable on Member
--------|----------|---------------|------------------
no access modifier| Only within its own package|Y|Y
public|everywhere|Y|Y
private|Only within its own class|N|Y
protected|Only within its own class and subclass|N|Y

### Archive Files
* put folder structure into an archive file(jar)


## Data Types
### 1. Primitives
  * Type: `int`,`float`,`char`,`boolean`
  * Define a char, enclose it in single quotes
  * Primitive Types are stored by value
  * PMDAS: Parentheses--> Mutiply,Divide,Mod,Add and Subtract
  * Divide: `10/3-->3`
  
<img width="920" alt="Screen Shot 2019-07-28 at 1 28 52 PM" src="https://user-images.githubusercontent.com/27160394/62010694-0dd92700-b13c-11e9-85e5-0dac9db90aa0.png">
<img width="930" alt="Screen Shot 2019-07-28 at 1 30 25 PM" src="https://user-images.githubusercontent.com/27160394/62010702-1fbaca00-b13c-11e9-8f6c-e94fe48e683f.png">
<img width="378" alt="Screen Shot 2019-07-28 at 1 39 42 PM" src="https://user-images.githubusercontent.com/27160394/62010792-2f86de00-b13d-11e9-9c2f-a57674c5328b.png">

#### Primitve wrapper class 
* All wrapper class instances are immutable
* Explicit convetsions
 1. Primitive,String to wrapper: `valueOf`
 2. Wrapper to primitive:`xxxValue()`
 3. String to primitive:`parseXxx`
 ```
 Interger d =Interger.valueOf(100);
 int e=d.intValue();
 String s="87.4"
 double s1=Double.parseDouble(s);value
 Double s2=Double.valueOf(s);reference
 ```
 * Null references: wrapper class can check it's not equal to null
 * Wrapper Class equality: Boxing conversions that always return the same wrapper class instance in given range
 
### 2. Objects
  * String are object
  * When `double` come up against each other, result is `double`
  * `string` comes up against anything else, the result is a `string`
  * Computer move from left to right
```
1+2+"3"+4+5
>>> "3345"
1 * 2 + "3" + 4 * 5 
>>>"2320"
```
### Type Conversion
* Implicit(Widening, automatic) type conversion: Conversions performed automatically by the compiler
* Explicit(Widening or narrowing ) type conversion: Conversions performed explicitly in code with cast operator
```
int Val=50;
long Vall=val; implicit

long Vall=50:
int Val=(int)Vall;Explicit
```
### Variable
> store a piece of information and then manipulate it later on
#### Declaring
* Assignment statement: creates the space in memory for a value
```
DataType variable;
variable=value;
DataType variableName= value;
```
* declar an arrays
```
int[] vars=int[2];
int[0]=1;
int[1]=2;

int[] vars={1,2}
```
#### Naming Variables
* The style often referred to as "Camel Case"
```
int bankAccountBalance;
```
#### Casting
> force variable to different type
```
(int)(10/4.0)
>>> 2
(int)10/4
>>> 2.5
```
#### Scope
> Variable only lives within a set of curly braces
* class constants:declaring variables whose value never changes 
* Java naming convention is to use all capital letters for class constants,separating words by underscores


## String Class
> Sequence of Unicode characters
* String objects are immutable, When you change the string, variable reference to another sTRING object

### Main methods
<img width="854" alt="Screen Shot 2019-07-29 at 1 38 10 PM" src="https://user-images.githubusercontent.com/27160394/62069372-1eee6a80-b206-11e9-8560-6b2552885128.png">.

```
String s="abcde"
for (int i = 0; i<s.length();i++){
    System.out.println(s.indexOf(s.charAt(i)))
System.out.println(.substring(0,4))
int val=100
String sval=String.valueOf(val);//"100"
```

### String Equality
* `equals()` in String comparsion is a character by character comparison
* `intern()`:ensure that all strings having the same contents share the same memory,it returns a canonical representation for the string object

```
public static void main(String[] args)  
    {  
        // S1 refers to Object in the Heap Area  
        String s1 = new String("GFG"); // Line-1  
  
        // S2 refers to Object in SCP Area 
        String s2 = s1.intern(); // Line-2  
          
        // Comparing memory locations 
        // s1 is in Heap 
        // s2 is in SCP 
        System.out.println(s1 == s2); 
          
        // Comparing only values 
        System.out.println(s1.equals(s2)); 
          
        // S3 refers to Object in the SCP Area  
        String s3 = "GFG"; // Line-3  
  
        System.out.println(s2 == s3);  
    }  
```
### Converting Non-string type to String
* Conversions often happen implicity
* Class conversions controlled by `toString` method
* `toString` use to get the string representation of a class

### StringBuilder
* provide mutabl string buffer
* pre-size buffer, will grow automatically if needed
* `toString`:returns a string representing the data contained by StringBuilder Object
----------

# Control Structures
## Comparisons
* Primitive data type: relations operators
* String: `string.equals(string2)`

## If- else Statement
```
if (condition){
    code
} else{
    code
}
```

```
if (condition){
    code
} else if (test){
    code
}
  else{
  code
}
```
* ternary operator
```
result=testcondintion ? trueValue : falseValue
```
### Logical Operators
Operator|Name|Example|Value
--------|----|-------|-----
`&&`|and|(2==3)&&(-1<5)|False
`II`|or|(2==3)&&(-1<5)|true
`!`|not|!(2==3)|true
`^`|exclusive|true^false|true

* CANNOT write `-2 <= x <= 4`
* Differ between `&&` and`&`: Only execute the right side if needed to determine the result


## Loops
### While loop
```
variable initialization;
while (boolean test is true){
     statements;
     test update
}
```
### Do-while loop
* Unlike while loop, Do-while check the conditio at the end of block
* Statement always executes at least once.
```
do
  statment;
while(condition);
```
### For loop
```
for (initialization;condition;update){
    statements
}
```
### Switch
* test against multiple possible matches
* Only char and integers support Switch
* Optionally include default to handle any unmatched values
```
int var=10;
swith(var%2){
      case 0:
        System.out.println(ival);
        System.out;println("is even");
	break;
      case 1:
        System.out.println(ival);
        System.out;println("is odds);
	break;
     default:
     	System.out.println("unvalidable");
	break;
```
----------
# Class 
> A template for creating an object
## Made up
### Fields
>store object state
#### Final field
> prevernt field from being changed once assigned
* final field must be set during creating of an object instance
* Can be set with field initializer, initialization block, or constructor.
* `static` field can not be set by an object instance, It's class variable
* Enumeration(`enum`) define a type that have a list of valid values
### Methods
> manipulates state and performs operations
* static methods: a named group of statements
* called by main to execute
<img width="435" alt="Screen Shot 2019-06-14 at 3 16 31 PM" src="https://user-images.githubusercontent.com/27160394/59532715-88fdbb00-8eb7-11e9-908c-e9386ac2626e.png">
* declaring methods should outside of the main method, but inside of the class

```
#Declare
public static void FristMethods(){
    statement1;
}

# Call
public static void main(String[] args){
    FirstMethod();
  }
```

* Main Method(`public static void main(String[] args)`):
  * it’s the method Java will use to execute your program
  * Since this is a special method, you cannot use the name "main" for any of your other methods.
  * Other methos should places after main method

* Method name: 
  * start with a verb, followed by whatever item that task is performed
  * start with loww case letter and use uppercase letters to start each following word with no spaces. 
### CLass initializers and Constructors
* Use `new` to create a class instance
* Class are reference types,Variables of object instance are not object self, it's reference
  * `this` is an implicit reference to the current object
  * `null` presents an uncreated object 
  
#### Initialization
##### Field Initial State
* specify a field's initial value as part of its declaration.
* Can be an equation
* Can reference other fields
* Can be a method call
##### Constructor
> creation object to set the initial state
* A class have at least one constructor
  * java provide one(no parameter) if no explicit constructor
  * A class can have mulitpl constructors, each with different parameter lists.
  * if user want create object without provide any parameters, need to explicit write new constructor.
* Chaining Constructors
  * one constructor can call other constructor, but must declare in first line
  * use access modifiers to control constructor visibility
##### Initialization Blocks
* Initialization blocks shared across all constructors
* Enclose statements in brackets outside of any metho or constructor
##### Order
1. Field Initialization
2. Initialization Block
3. Constructor

## Encapsulation
> internal representation of an object is generally hidden
* Java uses access modifiers to achieve encapsulation
* Use accessor/mutator(`setter`,`getter`) pattern to control field access
## Naming classes 
* followed "Pascal case"(start of each word is upper case)
* Should use simple, descriptive nouns
## Inheritance
* Use `extend`identify what class it inheriting from 
* Instance of subclass can be assigned to references of the base class type
```
animal[] zoo = new animal[2];
zoo[0]=new animal();
zoo[1]=new dog();
```
### Member hiding and overriding
* If subclass add a field that has the same name as a field in base class, it will acutally hiden the base class field
* if instance of subclass as a reference of base class type, 
  * the computer execute base class field.
  * if method in subclass override base class, computer will execute subclass method
* Java provide `@Override` intention, overrider a method from the base class.
### Object class
> root of the Java class hierarchy
* Object class methods
<img width="873" alt="Screen Shot 2019-07-29 at 12 33 37 PM" src="https://user-images.githubusercontent.com/27160394/62065501-1d6c7480-b1fd-11e9-9e4e-5f8da6773055.png">
#### Equality
* equals operator and `.equals()` only resolves to true with reference types if both points to same object
* If compare with two instance of given class, we can override `eqauls` method.

### `super`
* Similar to `this`,`super` reference to the current object, but it treats the object as if it is an instance of its base class
* `super.equal(instance)`: check two references point to same object
### `Final`
* prevent inheriting or overriding
* It can implement in class level, method level and variable level
### `abstract`
* require inheriting or overriding
* In method level, no brack required
* It's possible to mark a class as abstract class and have no abstract methods
### Constructor
* Constructor are not inherited
* A base class constructor must always be called,
* If don't an explicit call to a base class constructor, it will use the no-argument constructor
* Explicity call a special base class constructor using `super` followed by parameter list(Must be first line)	

## Interface
* Compare with class, interface don't provide any implementation
* Interface define a contract, class conforms to the contract
* All methods of an interface must be public
* A class can implement multiple interfaces
* Class implementing an interface must define all interface methods as public
```
public class Customer implements Comparable, Iterable<Person> {
	private int memberlevel;
	private memeberDays;
	public int compareTo(object o){
	 	Customer c=(Customer)0;
		if(memberlevel>c.memberlevel)
			return -1;
		else if (memberleve<c.memberlevel)
			reurn 1;
		else{
			if(memberDays>c.memberDays)
				return -1;
			else if (memberDays<c.memberDays)
				return 1
			else
				return 0
			}
		}
	public Iterator<Person> iterator(){
	
	
	}
	
	}
```
### Declaring an interface
* Interface can extend another interface
* 
* Generic interface: interface which require addition type information
```
public class Customer implements Comparable<Customer>
```
## `Staic`
* Static member is class member that's not associated with any individual instance,All instances access the same values
* Access them using class name
### Staic initialization blocks
* Execute before type's first use
* perform one-time type initialization, so next time the data already loaded
### Nested types
> A type declated within another type
* Class can be declared within classes and interfaces
* Interfaces can be declared within classes and interfaces
* In actual, nested types are member of the enclosing type
  * Private members of the enclosing type are visible to the nested type
  * Nested types support all member access modifiers(`public`,`package private`,`protected`,`private`)
#### Two purposes of Nested types
##### 1. Structure and scoping
> No relationship between instances of nested and enclosing type
* Static classes nested within classes
* All classes nested within interfaces
* All nested interfaces
##### 2. Inner classes
> Each instance of the nested class is associated with an instance of the enclosing class
* Non-Static classes nested within classes
* Can access any private instance variable of outer class

#### Anonymous Classes
* Anonymous class are inner classs
* Create as if you are constructing an instance of the interface or base class
* Enable you to declare and instantiate a class at the same time
* Use them if you need to use a local class only once

## Data Flow
### Parameters
```
public static void name(type name, type name){
      statement(s)
}
```
* Any changes you make to the value inside method are not visible outside of method
* Changes made to members of passed class instances are visible outside of method
* Method can be declared to accept a varying number of parameter value
	* Can only be the last parameter
	* Method reveives values as an array
```
addNumbers(new nums[]{1,2,3,4})
addNumbers(1,2,3,4)
```
#### Overloading
> Same method name with different paramteter(or in different orders)
* Each constructor and metho must have an unique signature(Name,Number of parameters,Type of each parameter)
* Signature determines which implementation of an overload get called
* parameter can automatically convert type if there is no match signature


### `Return`
* instead of 'void',put the data type of whatever you're returning.
```
public static TYPE name(parameters){
  return data
}
variable=name(parameter)
```

### Math
* Math
```
int x=Math.abs(-2);
double fSq=Math.pov(4,2);
int y= Math.round(0.66);# return 1
doulbe z=Math.ceil(0.4)# retur 1.0
double n=Math.random();#returns a randomly generated number between 0 and 1
```


### Recursive
> an alogrithm that is made up of small versions of its self
* Component
1. base case
2. recurive case
```
public static int factorial(n){
     if(n<=1){return 1;}#1
     return n*factorial(n-1)#2
}
```

# Collections

## Printlns
Special Character|Replacement
-----------------|------------
New line|`\n`
"|\"
Tab|\t
\ | \\

* print function

```
System.out.println();# hit Enter at the end of line
System.out.print();# no Enter at the end of line
```


## Scanner
>  A special Java API that user types into the console.
1. import library `Scanner`
2. create `scanner` object
3. create object which receive `scanner` content
  * When Java gets to`input.next()`,it pauses and waits for the user to type something into the console and hit "enter".
  * If the data type of user typed in is different with declaring type, will raise `java.util.InputMismatchException` 
  
  
```
import java.util.Scanner;#1
public class HelloWorld{
	public static void main(String [] args){
		System.out.println("There");
		Scanner s=new Scanner(System.in);#2
		String m = s.next();#3
		System.out.println(m);
		s.close();
	}
```

Method   |Data Type
-------------------|----------
`name.nextInt()`| int
`name.nextDouble()`| double
`name.next()`|String,no spaces
`name.nextLine()`|Everthing before "enter"

`next()`| `nextLine()`accpet
--------|-------------
filter all no-valid symbol before valid character| everything before "enter"
after valid symbol,space will treat as end| the only one end symbol is "enter"
only read the input only till the space|reads the whole line before "enter"
places the cursor in the same line|positions the cursor in the next line.

# Exceptions and Error Handle
## `try/catch/fnally` 
* provide structured way to handle exceptions
## Exception Class Hierarchy
![ExceptionClassHierarchy-1](https://user-images.githubusercontent.com/27160394/62088355-13656880-b233-11e9-8ab1-92cf88505848.png).

* Error generally represents problem directly inside the Java virtual machine
* Classes that inheirt from `RuntimeException` represent error in your program
* Checked exceptions: exceptions that are checked at compile time, complier will raised a error if you can't handle it
* Uncheckered exceptions: the occurrences of which are not checked by the compiler. 
* Any exception class that inherits from `RuntimeException` is uncheck exception
* Each exception tye can have a separate catch block, the order from leaf to root

## Exceptions in Methods
* Document that the exception might occur by using the `throws` clause
* The throws clause of an overridiing method must b compatibl with the throws clause o th overridden method
## Customize Exceptions types
