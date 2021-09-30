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

