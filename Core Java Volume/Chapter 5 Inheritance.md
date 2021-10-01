In this chapter
1. Classes, Superclasses, and Subclasses 
2. bject: The Cosmic Superclass
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

### 5.1.6 Understanding Method Calls

Let's call `x.f(args)`, parameter x is declared to be ann object of Class C.

1. The compiler looks at the declared type of the object and method names
2. The compiler enumerates all methods called f in the class C and all accessible methods called f in the superclasses of C
3. Base on types of arguments, the compiler decide which method should be call --> overloading.

*  The method’s signature: the name and parameter type list for a method
*  If you define a method in a subclass that has the same signature as a superclass method, you override the superclass method.
*  The return type is not part of the signature. However, when you override a method, you need to keep the return type compatible(subtype).

*static binding* :  If  the method is *private*, *static*, *final* or a constructor,then the compiler knows exactly which method to call
*Dynamic  binding*: the method to be called depends on the actual type of the implicit parameter. (overriding) 

* The virtual machine precomputes for each class a method table that lists all method signatures and the actual methods to be called
* When you override a method, the subclass method must be at least as visible as the superclass method. 
      * , if the superclass method is *public*, the subclass method must also be declared *public*. 

### 5.1.7 Preventing Inheritance: Final Classes and Methods
> Classes that cannot be extended are called final classes

* If a class is declared `final`, only the methods, not the fields, are automatically final.

### 5.1.8 Casting
> The process of forcing a conversion from one type to another 
Syntax : Surround the target class name with parentheses and place it before the object reference you want to cast.
```
Manager boss = (Manager) staff[0];
```
* `instanceof` : check before casting from a superclass to a subclass.
* The only reason to make the cast is to use **a method that is unique** to managers

### 5.1.9 Abstract Classes
* a class with one or more `abstract` methods must itself be declared `abstract`.

When you extend an abstract class,
* can leave some or all of the abstract methods undefined; then you must tag the subclass as abstract as well. 
* you can define all methods, and the subclass is no longer abstract.

Abstract classes cannot be instantiated, but you can still create object variables of an abstract class, but such a variable must refer to an object of a nonabstract subclass.
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
The way we see it, there are two distinct scenarios:
* If subclasses can have their own notion of equality, then the symmetry requirement forces you to use the getClass test.
* If the notion of equality is fixed in the superclass, then you can use the instanceof test and allow objects of different subclasses to be equal
to one another.
?
### 5.2.4 The hashCode Method
* A hash code is an integer that is derived from an object.

### 5.2.5 The toString Method
