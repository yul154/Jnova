# Catalog
1. Introduction to Object-Oriented Programming 
2. Using Predefined Classes
3. Defining Your Own Classes
4. Static Fields and Methods
5. Method Parameters 
6. Object Construction 
7. Packages
8. JAR Files
9. Documentation Comments 4.10 Class Design Hints
----
## 4.1 Introduction to Object-Oriented Programming
* OOP reverses the order: puts the data first, then looks at the algorithms to operate on the data.
* 
### 4.1.1 Classes
> A class is the template or blueprint from which objects are made
* As you have seen, all code that you write in Java is inside a class. 
* Constructing an **object** by creating an **instance** of the class.

**Encapsulation** 
> Encapsulation is simply combining data and behavior in one package and hiding the implementation details from the users of the object

* *instance fields* : The bits of data in an object.
* *methods* : the procedures that operate on the data.

The key of Encapsulation is to have methods never directly access instance fields in a class other than their own. Interact with object data only through the object’s methods.

### 4.1.2 Objects
Key characteristics of objects
* The object’s behavior—what can you do with this object, or what methods can you apply to it?
* The object’s state—how does the object react when you invoke those methods?
* The object’s identity—how is the object distinguished from others that may have the same behavior and state?

### 4.1.4 Relationships between Classes
The most common relationships between classes are

**Dependence (“uses–a”)**
> a class depends on another class if its methods use or manipulate objects of that class.

if a class A is unaware of the existence of a class B, it is also unconcerned about any changes to B. (And this means that changes to B do not introduce bugs into A

* Try to minimize the number of classes that depend on each other(minimize the coupling between classes.)

**Aggregation (“has–a”)** 

**Inheritance (“is–a”)**
> expresses a relationship between a more special and a more general class
----

## 4.2 Using Predefined Classes
### 4.2.1 Objects and Object Variables
To work with objects
1. First construct them: *constructors* to construct new instances.
    * Constructors always have the same name as the class name.
    * Store the object in a variable in order to keep using it.
    * An object variable doesn’t actually contain anobject. It only refers to an object.
    * The value of any object variable is a reference to an object that is stored elsewhere. 
    *  set an object variable to null to indicate that it currently refers to no object.
```
new Date() //  construct a Date object
System.out.println(new Date()); //pass the objectto a method
String s = new Date().toString(); // apply a method to the object
Date birthDay = new Date() 
/* store an object in a variable, 
The expression new Date() makes an object of type Date,
its value is a reference to that newly created object
reference is then stored in the deadline variable.*/

Date deadline; // it's just a variable, but not refer to any object
deadline = new Date(); // must first initialize the deadline variable.
deadline = birthday; //  refer to an existing object:
```
3. Specify their initial state. 
4. Apply methods to the objects.

### 4.2.2 The LocalDate Class of the Java Library
* the Date class is not very useful for manipulating the kind of calendar dates.
* Separating time measurement (Date()) from calendars(localDate()) is good object-oriented design.
* Date() represent a point in time, localDate() expresses days in calendar notation.

### 4.2.3 Mutator and Accessor Methods

*mutator method* : After invoking it, the state of the object has changed
*accessor* : Only access objects without modifying them 

* Be careful not to write accessor methods that return references to mutable objects
```
Employee harry = . . .;
Date d = harry.getHireDay(); // Date is mutable object.
double tenYearsInMilliSeconds = 10 * 365.25 * 24 * 60 * 60 * 1000; d.setTime(d.getTime() - (long) tenYearsInMilliSeconds);
// let's give Harry ten years of added seniority
// Applying mutator methods to d automatically changes the private state of the Employee object!
```

![Screen Shot 2021-09-30 at 7 36 25 PM](https://user-images.githubusercontent.com/27160394/135448151-1d77d19f-998b-48c2-9f51-b5a6caa683bb.png)

> Applying mutator methods to d automatically changes the private state of the Employee object!

### 4.3 Defining Your Own Classes
### 4.3.1 Building Class
```
package work;

import java.time.LocalDate;

public class Employee { // The keyword public means that any method in any class can call the method. 
	// instance fields;
	private String name;  // The private makes sure that the only methods that can access these instance fields are the methods of the Employee class itself
	private double salary; //  primitive type can never be null.
	private LocalDate hireDay; // guaranteed to be non-null because it is initialized with a new LocalDate object.
	public Employee(String n, double s, int year,int month,int day) {//constructor
		name = n; //not use variable names that equal the names of instance fields.
		salary = s;
		hireDay = LocalDate.of(year,month,day);
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public double getSalary() {
		return salary;
	}
	public void setSalary(double salary) {
		this.salary = salary;
	}
	public LocalDate getHireDay() { // field accessors.
		return hireDay;
	}
	public void setHireDay(LocalDate hireDay) {
		this.hireDay = hireDay;
	}
	public void raiseSalary(double byPercent){
		double raise = salary * byPercent / 100;
		salary += raise;
	}
}

Employee harry = new Employee("Harry Hacker", 50000, 1989, 10, 1);
var harry = new Employee("Harry Hacker", 50000, 1989, 10, 1); // declare local variables with the var keyword instead of specifying their type

```
### Working with null References

```
LocalDate birthday = null;
String s = birthday.toString(); // NullPointerException
```
Apply a method to a null value, a NullPointerException occurs.

There are two  approach is to turn a null argument into an appropriate non-null value
1. turn a null argument into an appropriate non-null value
```
if (n == null) name = "unknown"; else name = n;
name = Objects.requireNonNullElse(n, "unknown");
```
2.reject a null argument
```
Objects.requireNonNull(n, "The name cannot be null"); 
name = n; //constructs an Employee object with a null name, then a NullPointerException occurs.
```
----
### 4.3.7 Implicit and Explicit Parameters

*implicit* parameter : the object that appears before the method name ,the keyword *this* refers to the implicit parameter.,
*Explicit* parameter : the number inside the parentheses after the method name.
```
public void raiseSalary(double byPercent){
		double raise = this.salary * byPercent / 100;
		this.salary += raise;
	}
```
### 4.3.9 Class-Based Access Privileges
>  method can access the private data of all objects of its class.
```
class Employee
   {
...
      public boolean equals(Employee other)
      {
return name.equals(other.name); }
}

if (harry.equals(boss)) 
```
### 4.3.10 Private Methods
Wish to break up the code for a computation into separate helper methods. Typically, these helper methods should not be part of the public interface,Such methods are best implemented as private.

### 4.3.11 Final Instance Fields
* must guarantee that the field value has been set after the end of every constructor. Afterwards, the field may not be modified again.
* 
```
class Employee
{
private final String name; ...
}
```
----
## 4.4 Static Fields and Methods
### 4.1 Static Fields
> There is only one such field per class
* there is only one field that is shared among all instances of the class.
* It belongs to the class, not to any individual object.

### 4.4.3 Static Methods 
>  Static methods as methods that don’t have a this paramete.
* A static method can access a static field. 

Use static methods in two situations:
1. When a method doesn’t need to access the object state because all needed parameters are supplied as explicit parameters (example: Math.pow).
2. When a method only needs to access static fields of the class (example: Employee.getNextId).

### 4.4.4 Factory Methods
> Use static factory methods that construct object.
```
NumberFormate currentFor = NumberFormate.getCurrencyInstance();
NumberFormat percentFormatter = NumberFormat.getPercentInstance();
```
* The constructor name is always the same as the class name. But we want two different names to get the currency instance and the percent instance.
* When you use a constructor, you can’t vary the type of the constructed object. But the factory methods actually return objects of the class.
----
## 4.5 Method Parameters
*call by value* :  method gets just the value that the caller provides.

*call by reference* : the method gets the location of the variable that the caller provides. 

The Java programming language always uses *call by value*.  
* That means that the method gets a copy of all parameter values
* In particular, the method cannot modify the contents of any parameter variables passed to it.

```
double percent = 10; 
harry.raiseSalary(percent); // the percent is still 10
```

There are, however, two kinds of method parameters: 
1.Primitive types (numbers, boolean values) : no way fo a method to change.
2. Object references
```
public static void main(String[] args) {
	Employee harry = new Employee("Carl Cracker", 75000, 1987, 12, 15);
	tripleSalary(staffs);
	System.out.println(staffs.getSalary()); //225000

}
	
public static void tripleSalary(Employee x) {
	x.raiseSalary(200);
}
```
1. x is initialized wih a copy of the values of `staffs` (object reference)
2. The `raiseSalary` methodis applied to that object reference.The Employee object to which both `x` and` harry refer` gets its salary raised by 200 pe
3. The method ends,and the parameter variable `x` is no longer in use.Of course, the object variable `harry` continues to refer to the object whose salary was tripled.
![Screen Shot 2021-09-30 at 9 35 37 PM](https://user-images.githubusercontent.com/27160394/135465373-ce15a565-d7b1-4856-8ea6-f2f57cf9ce2c.png)

* The method gets a copy of the object reference, and both the original and the copy refer to the same object.

```
public static void swap(Employee x, Employee y) // doesn't work
   {
	Employee temp = x; x = y;
	y = temp;
   }
var a = new Employee("Alice", . . .); 
var b = new Employee("Bob", . . .); 
swap(a, b); // The original variables a and b still refer to the same objects as they did before the method call 
```
**Summary**
* A method cannot modify a parameter of a primitive type (that is,numbers or boolean values).
* A method can change the state of an object parameter.
* A method cannot make an object parameter refer to a new object.
----
## 4.6 Object Construction
### 4.6.1 Overloading
> Overloading occurs if several methods have the same name but different parameters.
1. Construct an empty `StringBuilder` object
```
var messages = new StringBuilder();
var todoList = new StringBuilder("To do:\n");
```
* It picks the correct method by matching the parameter types in the headers of the various methods with the types of the values used in the specific method call. 

### 4.6.2 Default Field Initialization
If you don’t set a field explicitly in a constructor, it is automatically set to a default value: 
* numbers to 0
* boolean values to false
* object references to null.

### 4.6.3 The Constructor with No Arguments
> If you write a class with no constructors whatsoever, then a no-argument constructor is provided for you.
* The default constructor sets all the instance fields to their default values.
* If a class supplies at least one constructor but does not supply a no-argument constructor, it is illegal to construct objects without supplying arguments

### 4.6.4 Explicit Field Initialization
> make sure that, regardless of the constructor call, every instance field is set to something meaningful.
```
class Employee
{
	private static int nextId; 
	private int id = assignId(); ...
	private static int 
	assignId()
	{
	int r = nextId; 
	nextId++; 
	return r;
        }
}
```

### 4.6.5 Parameter Names
* Some programmers prefix each parameter with an “a”:
```
public Employee(String aName, double aSalary) {
	name = aName; 
	salary = aSalary;
}
```
* Parameter variables shadow instance fields with the same name
```
public Employee(String name, double salary) {
	this.name = name;
	this.salary = salary; 
}
```
### 4.6.6 Calling Another Constructor
If the first statement of a constructor has the form this(. . .), then the constructor calls another constructor of the same class
```
public Employee(double s) {
	this("Employee #" + nextId, s);// calls Employee(String, double) 
	nextId++; 
}
```
### 4.6.7 Initialization Blocks


1. All data field  are initialized to their default values.
2. All field initializers  and initialization blocks are executed,in the order in which they occur in the class declaration.
3. The body of the constructor is executed.

If the static fields of your class require complex initialization code, use a static initialization block.

### 4.6.8 Object Destruction and the finalize Method
* If a resource needs to be closed as soon as you have finished using it, supply a close method that does the necessary cleanup. You can call the close method when you are done with the objec

* `finalize`  is now deprecated.
---
## 4.7 Packages
> group classes in a collection called a package.
### 4.7.1 Package Names
*  Use an Internet domain name (which is known to be unique, written in reverse
### 4.7.2 Class Importation
---
## 4.8 JAR Files
> When you package your application, you want to give your users a single file, not a directory structure filled with class files.

### 4.8.2 The manifest
> A manifest file that describes special features of the archive.

### 4.8.3 Executable JAR Files
---
## 4.9 Documentation Comments
*javadoc* generates HTML documentation from your source files.

start with the special delimiter `/**` to your source code, you too can easily produce professional-looking documentation.
### 4.9.1 Comment Insertion
The `javadoc` utility extracts information for the following items:
1. Modules
2. Packages
3. Public classes and interfaces
4. Public and protected fields
5. Public and protected constructors and methods

* Comment contains free-form text followed by tags. A tag starts with an @, such as @since or @param.
* The first sentence of the free-form text should be a summary statement

### 4.9.2 Class Comments
The class comment must be placed after any import statements, directly before the class definition.
### 4.9.3 Method Comments
general-purpose tags
| tag name | descrption|
|----------|-----------|
|`@param` variable | This tag adds an entry to the “parameters” section of the current method. The description can span multiple lines and can use HTML tags. All `@param` tags for one method must be kept together.|
|`@return ` |This tag adds a “returns” section to the current method. The description can span multiple lines and can use HTML tags.|
|`@throws` class |This tag adds a note that this method may throw an exception|

```
/**
  * Raises the salary of an employee.
   * @param byPercent the percentage by which to raise the salary (e.g., 10 me
   * @return the amount of the raise
*/
```
----
## 4.10 Class Design Hints
1. *Always keep data private*.
2.* Always initialize data* : initialize all variables explicitly.
3. *Don’t use too many basic types in a class* : to replace multiple related uses of basic types with other classes
4. *Not all fields need individual field accessors and mutators.* 
5. *Break up classes that have to omany responsibilities* 
6. *Make the names of your classes and methods reflect their responsibilities*.
>a class name should be a noun (Order), or a noun preceded by an adjective (RushOrder) or a gerund (an “-ing” word, as in BillingAddress).
7. *Prefer immutable classes*:  it is safe to share their objects among multiple threads.
8. 

