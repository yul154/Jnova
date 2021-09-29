# CATALOG
1. A Simple Java Program 
2. Comments
3. Data Types
4. Variables and Constants
5. Operators
6. Strings
7. Input and Output
8. Control Flow
9. Big Numbers
10. Arrays

----
## 3.1 A Simple Java Program 
```
public class FirstSample
{
  public static void main(String[] args)
  {
         System.out.println("We will not use 'Hello, World'");
  }
}
```
*  The keyword public is called an access modifier; these modifiers control the level of access other parts of a program have to this code. 
*  A class as a container for the program logic that defines the behavior of an application
*  The rules for class must begin with a letter,class names are nouns that start with an uppercase letter

----

## 3.2 Comments

Java has three ways of marking comments

1. Mark each line with a //, 
2. Use the /* and */ comment delimiters that let you block off a longer comment.
3. Generate documentation automatically. This comment uses a /** to start and a */ to end

----

## 3.3 Data Type

* Java is a strongly typed language. This means that every variable must have a declared type
* There are eight primitive types in Java. 
  * Four of them are integer types; 
  * two are floating-point number types; 
  * one is the character type char, used for code units in the Unicode encoding scheme
  * one is a boolean type for truth values.
 
### 3.3.1 Integer Types
![Screen Shot 2021-09-29 at 11 02 45 AM](https://user-images.githubusercontent.com/27160394/135195965-f86999e9-ef0b-46cc-9383-199f5501d71f.png)

### 3.3.2 Floating-Point Types
![Screen Shot 2021-09-29 at 11 06 24 AM](https://user-images.githubusercontent.com/27160394/135196303-349d4448-2627-4b28-baff-1d31dcf72f6a.png)
* The name double refers to the fact that these numbers have twice the precision of the float type
* Numbers of type float have a suffix F or f (for example, 3.14F). Floating-point numbers without an F suffix (such as 3.14) are always considered to be of type double. 
* Floating-point numbers are not suitable for financial calculations in which roundoff errors cannot be tolerated。
*  If you need precise numerical computations without roundoff errors, use the **BigDecimal** class。

### 3.3.3 The char Type

* Literal values of type char are enclosed in single quotes.
> 'A' is a character constant with value 65. It is different from "A", a string containing a single character.


![Screen Shot 2021-09-29 at 11 15 59 AM](https://user-images.githubusercontent.com/27160394/135197131-1ab77376-1e73-4ca6-b084-fb679429124f.png)

### 3.3.4 Unicode
> Unicode was invented to overcome the limitations of traditional character encoding schemes
* In Java, the char type describes a code unit in the UTF-16 encoding.

### 3.3.5 The boolean Type
* You cannot convert between integers and boolean values.

## 3.4 Variables and Constants
> variables are used to store values. Constants are variables whose values don’t change

### 3.4.1 Declaring Variables
* every variable has a type. You declare a variable by placing the type first, followed by the name of the variable
```
double salary;
int vacationDays;
```
* You can declare multiple variables on a single line:
```
int i, j; // both are integers
```

### 3.4.2 Initializing Variables
```
int vacationDays;
System.out.println(vacationDays); // ERROR--variable not initialized
```

### 3.4.3 Constants
> In Java, you use the keyword final to denote a constant.

* The keyword final indicates that you can assign to the variable once, and then its value is set once and for all. 
* It is customary to name constants in all uppercase.
```
final double EXCHANGE_RATE = 0.65;
```
* class constants: It is probably more common in Java to create a constant so it’s available to multiple methods inside a single class. 
    * Set up a class constant with the keywords static final
```
public class Constants
{
   public static final double EXCHANGE_RATE = 0.65;
}
```

### 3.4.4 Enumerated Types
> A variable should only hold a restricted set of values

* An enumerated type has a finite number of named values.
* 
```
enum Size {LARGE, MEDIUM, SMALL};
Size size = Size.LARGE;
```
----

## 3.5 Operators
> Operators are used to combine values. 

### 3.5.1 Arithmetic Operators
* Note that integer division by 0 raises an exception, whereas floating-point division by 0 yields an infinite or NaN resul
* Division if both arguments are integers, and floating-point division otherwise.
* Methods tagged with the *strictfp* keyword must use strict floating-point operations that yield reproducible results.
```
public static strictfp void main(String[] args)
```
### 3.5.2 Mathematical Functions and Constants
> import static java.lang.Math.*'
* The *Math* class contains an assortment of mathematical functions
* Static method: *sqrt* method in the Math class does not operate on any object.

### 3.5.3 Conversions between Numeric Types

![Screen Shot 2021-09-29 at 11 49 44 AM](https://user-images.githubusercontent.com/27160394/135200226-fb3163be-5237-48d7-9dc4-6f10903a8635.png)
> * The six solid arrows denote conversions without information loss. 
> * The three dotted arrows denote conversions that may lose precision.

* When two values are combined with a binary operator, both operands are converted to a common type before the operation is carried out.
  * either of the operands is of type *double* , *float*, *long* be convert to its type
  * Otherwise, both operands will be converted to an *int*.

### 3.5.4 Casts
> Conversions in which loss of information is possible are done by means of casts.
```
double x = 9.997; 
int nx = (int) x;
```
* round a floating-point number to the nearest integer (which in most cases is a more useful operation), use the Math.round method:
```
double x =  9.997;
int nx = (int) Math.round(x);
```
### 3.5.5 Combining Assignment with Operators 3.5.6 Increment and Decrement Operators
```
int x = 4;
x + = 4;

/** 
postfix and prefix form of the operator;
The prefix form does the addition first; 
the postfix form evaluates to the old value of the variable
*/

int m =7;
int n = 7;
int a = 2* ++m;
int b = 2* n++; // a = 16,b=14, m=n=8

```

### 3.5.7 Relational and boolean Operators
 * The ternary :  *?:* 
 > condition ? expression1 : expression2
 ```
 x < y ? x : y;
 ```
### 3.5.8 Bitwise Operators
```
& (“and”) 
| (“or”)
^ (“xor”) 
~ (“not”)

public class operators {
    public static void main(String[] args)
    {
        // Initial values
        int a = 5;
        int b = 7;
 
        // bitwise and
        // 0101 & 0111=0101 = 5
 
        // bitwise or
        // 0101 | 0111=0111 = 7
 
        // bitwise xor
        // 0101 ^ 0111=0010 = 2
 
        // bitwise not
        // ~0101=1010
        // will give 2's complement of 1010 = -6
 
        // can also be combined with
        // assignment operator to provide shorthand
        // assignment
        // a=a&b
        a &= b;
        System.out.println("a= " + a);
    }
}
```

* Signed Right shift operator (>>) : Similar effect as of dividing the number with some power of two. 
* Left shift operator (<<) : Similar effect as of multiplying the number with some power of two. 
* Unsigned shift operator (>>>) ：The only difference is that the empty spaces in the left are filled with 0 irrespective of whether the number is positive or negative. Therefore, the result will always be a positive integer.
```  
int a = 12;
int leftShift = a << 2; //  00001100 -> 0110000 
int leftShift = 48 ;// 12*2**2;
```
```
-9 (base 10): 11111111111111111111111111110111 (base 2)
                    --------------------------------
-9 >>> 2 (base 10): 00111111111111111111111111111101 (base 2) = 1073741821 (base 10)
```

### 3.5.9 Parentheses and Operator Hierarchy
![Screen Shot 2021-09-29 at 12 11 05 PM](https://user-images.githubusercontent.com/27160394/135201950-bb95c945-948b-47c3-84e9-01797cc671a1.png)

---

## 3.6 Strings
>  Java strings are sequences of Unicode characters
* Each quoted string is an instance of the String class:

### 3.6.1 Substring

```
String greeting = "Hello";
String s = greeting.substring(0, 3);
```
### 3.6.2 Concatenation
* When you concatenate a string with a value that is not a string, the latter is converted to a string
```
String first = "1";
string second = "2";
String concat = first+second; //"12"
```
* multiple strings together, separated by a delimiter, use the static join method:
```
String all = String.join(" / ", "S", "M", "L", "XL"); // "S / M / L / XL"
String repeated = "Java".repeat(3); // repeated is "JavaJavaJava"
```

### 3.6.3 Strings Are Immutable
* Immutable strings have one great advantage: The compiler can arrange that strings are shared.(all uses of an interned string will share the same memory locations)

### 3.6.4 Testing Strings for Equality
* To test whether two strings are equal, use the *equals* method. 
* "==" :  It only determines whether or not the strings are stored in the same location
* When you use a string literal the string can be interned, but when you use new String("...") you get a new string object.

### 3.6.5 Empty and Null Strings
> An empty string is a Java object which holds the string length (namely, 0) and an empty contents. 
*  a String variable can also hold a special value, called null, that indicates that no object is currently associated with the variable
```
if (str != null && str.length() != 0) // to check if  string is neither null nor empty;
```
### Code Points and Code Units
* the *char* data type is a code unit for representing Unicode code points in the UTF-16 encoding.

The *length* method yields the number of code units required
``` 
String greeting = "Hello";
int n = greeting.length(); // is 5
To get the true length—that is, the number of code
```

To get the true length—that is, the number of code points—call
```
int cpCount = greeting.codePointCount(0, greeting.length());
```
The call s.charAt(n) returns the **code unit** at position n
```
Char first = greeting.charAt(0); // first is 'H'har first = greeting.charAt(0); // first is 'H'
```
To get at the ith code point, use the statements
```
int index = greeting.offsetByCodePoints(0, i);
int cp = greeting.codePointAt(index);
```

### 3.6.9 Building Strings
> `StringBuilder` class avoids this problem that every time you concatenate strings, a new String object is constructed.
```
StringBuilder builder = new StringBuilder();
builder.append('a');
builder.append("b");
String str = bubilder.toString(;)
```
* `StringBuffer`, is slightly less efficient than `StringBuilder` but it allows multiple threads to add or remove character

----
## 3.7 Input and Output
