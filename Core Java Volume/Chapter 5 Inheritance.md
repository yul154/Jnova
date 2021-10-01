In this chapter
1. Classes, Superclasses, and Subclasses 5.2 Object: The Cosmic Superclass
2. Generic Array Lists
3. Object Wrappers and Autoboxing
4. Methods with a Variable Number of Parameters 5.6 Enumeration Classes
5. Reflection
6. Design Hints for Inheritance
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
* *static binding* :  If  the method is *private*, *static*, *final* or a constructor,then the compiler knows exactly which method to call
