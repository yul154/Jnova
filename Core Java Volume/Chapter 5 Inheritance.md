In this chapter
1. Classes, Superclasses, and Subclasses 
2. Object: The Cosmic Superclass
3. Generic Array Lists
4. Object Wrappers and Autoboxing
5. Methods with a Variable Number of Parameters 5.6 Enumeration Classes
6. Reflection
7. Design Hints for Inheritance
----
## 5.1 Classes, Superclasses, and Subclasses
### 5.1.1 Defining Subclasses
The keyword `extends` indicates that you are making a new class that derives from an existing class. 

* Only need to indicate the differences between the subclass and the superclass. When designing classes

### 5.1.2 Overriding Methods
* Subclass can't directly access to the private fields of the superclass class.Need *super* class to access.
*  If there is same method in subclass. `super` indicate that we want to call the getSalary method of the Employee superclass, not the current class.
* Other than `this` which can assign the value o another obbject variable . `super` is a special keyword that directs the compiler to invoke the superclass method  

### 5.1.3 Subclass Constructors
```
public Manager(String name, double salary, int year, int month, int day) {
		super (name,salary,year, month, day);
		bonus = 0 ;
	}
```

|key word|meanings|
|--------|--------|
| `this`|to denote a reference to the implicit parameter and to call another constructor of the same clas|
|`super`|to invoke a superclass method and to invoke a superclass constructor.|

```
e.getSalary()
```
When e refers to an subclass object, the call e.getSalary() calls the subbclass method. However, when e refers to a superclass object, then the superclass method  is called instead.

* the declared type might be parent type, but the actual type of the object to which variable refers to.
* *polymorphismhe* : an object variable (such as the variable e) can refer to multiple actual types.
* *dynamic binding* : Automatically selecting the appropriate method at runtime .

### 5.1.5 Polymorphism
>  Object variables are polymorphic
* The “is–a” rule states that every object of the subclass is an object of the superclass is a simple rule to devide right design.
* *substitution principle* :  You can use a subclass object whenever the program expects a superclass object. Assign a subclass object to a superclass variable
```
Employee e;
e = new Employee(. . .); // Employee object expected
e = new Manager(. . .); // OK, Manager can be used as well
```

*  Cannot assign a superclass reference to a subclass variable
```
Manager boss = new Manager(. . .); 
Employee[] staff = new Employee[3];
staff[0] = boss;// staff[0] and boss refer to the same objectbut staff[0] is considered to be only Employee object 

Manager m = staff[i] // ERROR, assign a superclass reference to a subclass variable
```
*  Arrays of subclass references can be converted to arrays of superclass references without a cast,But all arrays remember the element type with which they were created.

### 5.1.6 Understanding Method Calls

Let's call `x.f(args)`, parameter x is declared to be ann object of Class C.

1. The compiler looks at the declared type of the object and method names
2. The compiler enumerates all methods called f in the class C and all accessible methods called f in the superclasses of C
3. Base on types of arguments, the compiler decide which method should be call --> overloading.
   *  The method’s signature: the name and parameter type list for a method
   *  If you define a method in a subclass that has the same signature as a superclass method, you override the superclass method.
   *  The return type is not part of the signature. However, when you override a method, you need to keep the return type compatible(subtype).

4. *static binding* : If the method is *private*, *static*, *final* or a constructor,then the compiler knows exactly which method to call. *Dynamic  binding*: the method to be called depends on the actual type of the implicit parameter. (overriding) 

5. When the program runs and uses dynamic binding to call a method ,
    1. The virtual machine precomputes for each class a method table that lists all method signatures and the actual methods to be called
    2. When a method is actually called, the virtual machine simply makes a table lookup (search current class at first)
 
Dynamic binding has a very important property: It makes programs extensible without the need for modifying existing code

* When you override a method, the subclass method must be at least as visible as the superclass method. 
      * if the superclass method is *public*, the subclass method must also be declared *public*. 

### 5.1.7 Preventing Inheritance: Final Classes and Methods
> Classes that cannot be extended are called final classes

* If a class is declared `final`, only the methods, not the fields, are automatically final.

### 5.1.8 Casting
> The process of forcing a conversion from one type to another 

There is only one reason why you would want to make a cast—to use an object in its full capacity after its actual type has been temporarily forgotten.

Syntax : Surround the target class name with parentheses and place it before the object reference you want to cast.

```
Manager boss = (Manager) staff[0];
```

* `instanceof` : check before casting from a superclass to a subclass.
* The only reason to make the cast is to use **a method that is unique** to managers

To sum up:
* You can cast only within an inheritance hierarchy.
* Use `instanceof` to check before casting from a superclass to a subclass.

### 5.1.9 Abstract Classes
> a class with one or more `abstract` methods must itself be declared `abstract`.

When you extend an abstract class,
* Abstract classes can have fields and concrete methods.
* can leave some or all of the abstract methods undefined; then you must tag the subclass as abstract as well. 
* you can define all methods, and the subclass is no longer abstract.

Abstract classes cannot be instantiated, but you can still create object variables of an abstract class,. but such a variable must refer to an object of a nonabstract subclass.
```
Person john = new Student("john");
```

### 5.1.10 Protected Access
> `protected` : Restrict a method to subclasses only or, less commonly, to allow subclass methods to access a superclass field. 
* A protected field is accessible by any class in the same package
* Accessble in the package and all sub classes(protected).
---

##5.2 `Object`: The Cosmic Superclass
> every class in Java extends `Object`

### 5.2.1 Variables of Type Object
it's able to use a variable of type Object to refer to objects of any type
```
Object obj = new Employee("harry",3500);
```
* Only primitive type are 

### 5.2.2 The equals Method
> The `equals` method in the `Object` class tests whether one object is considered equal to another.
```
public boolean equals(Object otherObject) {
		if (this == otherObject) return true;
		if (otherObject == null) return false;
		if (getClass() != otherObject.getClass()) return false;
		Employee other = (Employee) otherObject;
		return Objects.equals(name, other.name) && salary == other.getSalary() && Objects.equals(hireDay, other.hireDay);
	}
```
* `Objects.equals(a, b)` returns true if both arguments are null, false if only one is null,

### 5.2.3 Equality Testing and Inheritance

When we check if two objects from same class, we will use 
1. `getclass()`
2. `instanceOf()`: subclass can be `instanceOf` superClass

The `equals` method has the following properties
* *reflexive*:Foranynon-nullreferencex,`x.equals(x)`should return true.
* *symmetric*: For any references x and y , `x.equals(y)` should return true if and only if y.equals(x) returns true.
* *transitive* : if `x.equals(y)` returns true and `y.equals(z)` returns true, then `x.equals(z)` should return true.
* `consistent` x and y refer haven’t changed, then repeated calls to `x.equals(y)` return the same value.
* For any non-null reference x,`x.equals(null)` should return false. 

The way we see it, there are two distinct scenarios:
* If subclasses can have their own notion of equality, then the symmetry requirement forces you to use the getClass test.
* If the notion of equality is fixed in the superclass, then you can use the `instanceof` test and allow objects of different subclasses to be equal
to one another.

A perfect recipe for writing equals method
1. Name the explicit parameter other Object
2. Check same reference `if (this == otherObject) return true; `
3. Check null `if (otherObject == null) return false;`
4. Compare the classes ,If`equals` can change in subclasses, use the `getClass`,If the same semantics holds for all subclasses, you can use an `instanceof` test
5. Cast otherObject to a variable of your class type: `ClassName other = (ClassName) otherObject`
6. compare the fields

### 5.2.4 The hashCode Method
> A hash code is an integer that is derived from an object.
`equals` and `hashCode()` must be compatible. If  `x.equals(y)` is true. Then `x.hashCode()` must return the same value as `y.hashCode()`

If you redefine the `equals` method, you will also need to redefine the hashCode method
* If not,It will apply the default `hashCode` method in the `Objec`t class derives the hash code from the object’s memory address

### 5.2.5 The toString Method
> `toString` method that returns a string representing the value of this object.

* Whenever an object is concatenated with a string by the “+” operator, the Java compiler automatically invokes the `toString` method to obtain a string representation of the object. 

* the `println` method simply calls `x.toString()`, The Object class defines the `toString` method to print the class name and the hash code of the object
---
## 5.3 Generic Array Lists

> `ArrayList` is a *generic class* with a `type parameter`

```
ArrayList<Employee> = new ArrayList<>();
```
### 5.3.1 Declaring Array Lists

`ensureCapacity(100);` That call allocates an internal array of 100 objects. Then, the first 100 calls to add will not involve any costly reallocation.

Difference of Allocating an array list and allocating a new array.
```
new ArrayList<>(100) //at the beginning, even after its initial construction, an array list holds no elements at all.
new Employee[100] //If you allocate an array with 100 entries, then the array has 100 slots, ready for use.
```

`trimToSize`: trims the capacity of an ArrayList instance to be the list's current size.

### 5.3.2 Accessing Array List Elements
>  use the `get` and `set` methods instand of [] to access and change

* Do not call list.set(i, x) until the size (holding element)of the array list is larger than i. 
* `ArrayLis.toArray()` : copy the elements into an array
* `set` method replaces the element in the specified position with the new element. But in `add(position, element)` will add the element in the specified position and shifts the existing elements to right side of the array

### 5.3.3 Compatibility between Typed and Raw Array Lists
* Assign a raw ArrayList to a typed one, you get a warning.
* For compatibility, the compiler translates all typed array lists into raw ArrayList objects after checking that the type rules were not violated.
* All Lists are the same without type parameters in VM.
---

## 5.4 Object Wrappers and Autoboxing
>  Convert a primitive type like int to an object.
>  All primitive types have class counterparts(Wrappers)

* Wappers are immutable
* They are also `final`, so you cannot subclass them.
* type parameter inside of diamond can not be a primitive type

`int` --> `Integer` is boxing and opposite is unboxing.
```
var list = new ArrayList<Integer>();
list.add(3); //autoboxing
lis.add(Integer.valueOf(3)); //autoboxing.
int n = list.get(i).intValue(); // unboxing
```
Automatic boxing and unboxing even works with arithmetic expressions.
```
Integer n = 3; 
n++; // The compiler automatically inserts instructions to unbox the object, increment the resulting value, and box it back.
```


The autoboxing specification requires that boolean, byte, char <= 127, short, and int between -128 and 127 are wrapped into fixed objects.
```
Integer a = 100; 
Integer b = 100; 
if (a == b) // true
```

* `==` to check if the objects have identical memory locations. call the equals method when comparing wrapper objects.
* First off, since wrapper class references can be null, it is possible for autounboxing to throw a `NullPointerException`.

Wrappers a convenient place to put certain basic methods, such as those for converting strings of digits to numbers.
```
int x = Integer.ParseInt(str);
```
---

## 5.5 Methods with a Variable Number of Parameters
 `...` is part of the Java code. It denotes that the method can  receive an arbitrary number of objects 
 * `Object...` parameter type is exactly the same as `Object[]`

```
public static void main(String... args)
```
---
## 5.6 Enumeration Classes
```
public enum Size { SMALL, MEDIUM, LARGE, EXTRA_LARGE } // is actually a class
```
* use `==` innstead of `equals` to compare values of enumeraterd typed.
* All enumerated types are subclasses of the class `Enum`
---
## 5.7 Reflection
> *reflection library* use to manipulater java code dynamically
* A program that can analyze the capabilities of classes is called reflective.

### 5.7.1 The `Class` Class
> able to access this information by working with a special `Java` class

* The `getClass() `method in the `Object` class returns an instance of Class type.
* can obtain a `Class` object corresponding to a class name by using the static `forName` method
```
Employee e;
...
Class cl = e.getClass();
Class cl1 = Random.class; // if you import java.util.*;
Class cl = Class.forName(className);
Object obj = cl.getConstructor().newInstance(); // construct an instance

```
While your program is running, the Java runtime system always maintains what is called runtime type identification on all objects
* a `Class` object really describes a type, which may or may not be a class. 


### 5.7.3 Resources
The `Class` class provides a useful service for locating resource files. Here are the necessary steps:
```
URL url = cl.getResource("about.gif");
```

### 5.7.4 Using Reflection to Analyze the Capabilities of Classes
>  How to find out the names and types of the data fields of any object
* The three classes `Field, Method, and Constructor` in the `java.lang.reflect `package describe the fields, methods, and constructors of a class,
* You can then use the static methods in the Modifier class in the `java.lang.reflect` package to analyze the integer that `getModifiers` returns.
* `getDeclaredFields`, `getDeclaredMethods`, and `getDeclaredConstructors` methods of the Class class return arrays consisting of all fields, methods, and constructors that are declared in the class.



### 5.7.5 Using Reflection to Analyze Objects at Runtime
> actually look at the contents of the fields
Call `getDeclaredFields` on the Class object. to get value of fields
```
var harry = new Employee("Harry Hacker", 50000, 10, 1, 1989);
Class cl = harry.getClass();
Field f = cl.getDeclaredField("name");
v = f.get(harry); //shoud be Harry Hacker,but it throws IllegalAccessException, Since the name field is a private
```
* You can override access control by invoking the `setAccessible`
* `ObjectAnalyzer` keeps track of objects that were already visited.
### 5.7.6 Using Reflection to Write Generic Array Code
```
Class cl = a.getClass();
if (!cl.isArray()) return null;
Class componentType = cl.getComponentType();
int length = Array.getLength(a);
Object newArray = Array.newInstance(componentType, newLength); 
System.arraycopy(a, 0, newArray, 0, Math.min(length, newLength)); return newArray;

```

---
##  Summary
1. Place common fields and method in superclass
2. Be careful with `protected` fields : anyone can form a subclass of your classes and then write code that directly accesses protected instance fields, thereby breaking encapsulation.
3. Use inheritance to model the “is–a” relationship. But avoid fields or methods conflict when u consider to design this relationship.
4. Don’t change the expected behavior when youo verride a method.
5. Use polymorphism, not type information -> common action should define in interface or super class.
6. Don’t over user eflection: 
