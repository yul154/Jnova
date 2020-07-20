<img width="446" alt="Screen Shot 2019-10-02 at 11 27 19 AM" src="https://user-images.githubusercontent.com/27160394/66058094-c38f7000-e507-11e9-9e6c-a1bb4985c98e.png">.

# programming language?
* There are two ways to translate a program; 
  * interpreting and compiling.
  * An interpreter is a program that To run a program in a high-level language by translating it one line at a time.
  * A compiler: To translate a program in a high-level language into a low-level language, all at once, in preparation for later execution.
* A program is a sequence of instructions(statements) that specifies how to perform a com- putation

## Error
* P rogramming errors are called bugs
* Syntax errors:   static error that can be detected by the compiler, cannot be executed. 
* Run-time errors: the error does not appear until you run the program.
   * run-time errors are called exceptions
* logic or semantic error:it will compile and run without generating error messages, but it will not do the right thing

##  Java Glossary
* Java programs are made up of class definitions, which have the form
* method: A named collection of statements.
* library: A collection of class and method definitions.
*  syntax: The structure of a program.
* semantics: The meaning of a program.


# variables and types

## Variables
> A variable is a named location that stores a value.
* declaration: A statement that creates a new variable and determines its
  * When you declare a variable, you create a named storage location.
```
String bob;
```
* assignment:A statement that assigns a value to a variable.
```
bob="hello";
```
* Keywords: certain words that are reserved in Java
<img width="592" alt="Screen Shot 2019-10-02 at 11 51 13 AM" src="https://user-images.githubusercontent.com/27160394/66060007-f424d900-e50a-11e9-8ef5-e42629011cdf.png">.

* expression: A combination of variables, operators and values that repre- sents a single value.
### Operators
> symbols used to represent computations like addition and multiplication
* Java is performing integer division.
* operand: things operators operate on
* Order of operations: precedence
* String: the + operator does work with Strings
* Composition: left side of an assignment has to be a variable name, not an expressio

# Methods
## Void methods
*  double: the floating-point type
* initialization: combined declaration and assignment
```
int x=1;
double y= 1.0/3.0;
```
*  Java converts ints to doubles automatically
* typecast:take a value that belongs to one type and “cast” it into another type
```
double pi = 3.14159;
int x = (int) pi;
```
* round:  rounds a floating- point value off to the nearest integer and returns an int.
```
int x = Math.round(Math.PI * 20.0);
```
* Composition:  use one expression as part of another
```
double x = Math.cos(angle + Math.PI/2);
```
## Addiong new Methods
*  Java methods start with a lower case letter and use “camel caps,”
*  Static method in Java: a method which belongs to the class and not to the object.
* Execution always begins at the first statement of main
* arguments: values that you provide when you invoke the method
* parameter: A piece of information a method requires before it can run
* It is not necessary to include the type when you pass them as arguments.


# Conditionals and recursion
## Conditional statements
* The two sides of a condition operator have to be the same type
* The operators == and != work with Strings

# Value methods
## Return values
```
 public static double area(double radius) {
    return Math.PI * radius * radius;
}
```

*  Static method in Java: a method which belongs to the class and not to the object.
* Overloading: two or more methods in one class have the same method name but different parameters. 
* Overriding : having two methods with the same method name and parameters,. One of the methods is in the parent class and the other is in the child class.
* Boolean expressions
```
 boolean evenFlag = (n%2 == 0);
```
* Logical operators: AND, OR and NOT, which are denoted by the symbols &&, || and !.
* Boolean methods
```
public static boolean isSingleDigit(int x) {
    if (x >= 0 && x < 10) {
      return true;
    } else {
      return false;
    }
}
```
# Iteration and loops
* Multiple assignment
```
int a =5;
int b=3;
a=3;
```
* `while`

* Encapsulation means taking a piece of code and wrapping it up in a method,
## Local variables
* Variables declared inside a method definition are called local variables

# Strings
* Strings are objects
* char is the variable type that contain only a single letter or symbol
* `indexOf` is the inverse of `charAt`
* String are immutable, incomparable
* String  don't use new to create one.
# Mutabl objects
## Packages
* To use a class defined in another package, you have to import it
