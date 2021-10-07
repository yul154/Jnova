# Catalog
1. Why Generic Programming?
2. Defining a Simple Generic Class
3. Generic Methods
4. Bounds for Type Variables
5. Generic Code and the Virtual Machine 
6. Restrictions and Limitations
7. Inheritance Rules for Generic Types 
8. Wildcard Types
9. Reflection and Generics
----
## 8.1 Why Generic Programming?
> Generic programming means writing code that can be reused for objects of many different type

Generic classes and mehods have type parameter, It is advantageous when they are instantiated with multiple type. (`ArrayList`)

### 8.1.1 The Advantage of Type Parameters

Generics offer a better solution: type parameters `<>`.It avoids casting and the compiler can check that you don’t insert objects of the wrong type

### 8.1.2 Who Wants to Be a Generic Programmer?
---
## 8.2 Defining a Simple Generic Class
> A generic class is a class with one or more type variables
```
public class Pair<T, U> { . . . } // A generic class can have more than one type variable

Pair<String> // instantiate the generic type
```
---
## 8.3 Generic Methods
It it able to also define a single method with type parameters
```
public static <T> T getMiddle(T... a){
    return a[a.length/2];
}
```
The type variables are inserted after the modifiers (public static, in our case) and before the return type.
```
String middle = ArrayAlog.<String>getMiddle{"Johnn,"leo”};
```
Place actual type before the method name
---
## 8.4 Bounds for Type Variables
> Place restrictions on type variable
Sometimes, we need to restrict variable by giving a bound for the type variable in order to make sure its implementated necessary interface.

```
<T extends BoundingType>

public static <T extends Comparable> T min(T[] a)....
```
T should be a subtype of the bounding type.
---
## 8.5 Generic Code and the Virtual Machine
The virtual machine does not have objects of generic types—all objects belong to ordinary classes
### 8.5.1 Type Erasure
1. whenever a generic type defined. a corresponding `raw` type is automatically provided.
2. The type variables are  erased and replaced bby their bounding type.
3. Since `T` is an unbounded type variable, it is simply replaced by Object.

* The raw type replaces type variables with the first bound, 
  * or Object if no bounds are given.

### 8.5.2 Translating Generic Expressions
The compiler translates the method call into two virtual machine instructions:
 1. A call to the raw method
 2. A cast of the returned `Object` to the type.
 
### 8.5.3 Translating Generic Methods

The type erasure interferes with polymorphism.To fix this problem, the compiler generates a bridge method 
```
public void setSecond(Object second) { setSecond((LocalDate) second); }
```
* There are no generics in the virtual machine, only ordinary classes and methods.
* All type parameters are replaced by their bounds.
* Bridge methods are synthesized to preserve polymorphism. 
* Casts are inserted as necessary to preserve type safety.
---
## 8.6 Restrictions and Limitations
### 8.6.1 Type Parameters Cannot Be Instantiated with Primitive Types
It's not allow to substitute a primitive type for a type parameter. Bcs  Bound type `Object` can't store their values,Thus, there is not `Pair<double>`, only `Pair<Double>`.
### 8.6.2 Runtime Type Inquiry Only Works with Raw Types
* Objects in the virtual machine always have a specific nongeneric type. Therefore, all type inquiries yield only the raw type
* when you try to inquire whether an object belongs to a generic type. it will ger a compiler error.

### 8.6.3 You Cannot Create Arrays of Parameterized Types
```
var table = new Pair<String>[10]; // ERROR
```
After erasure, table is `Pair[10]`. which as different as real type.
```
ArrayList: ArrayList<Pair<String>> // effective solution.
```
### 8.6.4 Varargs Warnings
The rules have been relaxed for this situation which passing instances of a generic type to a method with a variable number of arguments
### 8.6.5 You Cannot Instantiate Type Variables

```
public Pair{ 
first = new T();
second = new T()://error
}
```
* The best workaround, available since Java 8, is to make the caller provide a constructor expression (method reference/lambda expressio).
```
Pair<String> p = Pair.makePair(String::new);
```
* Another way is to construct generic objects through reflection, by calling the `Constructor.newInstance` method. must design the API so that you are handed a `Class` object.

### 8.6.6 You Cannot Construct a Generic Array
```
T[] mm = new T[2]; // ERROR
```
It is best to ask the user to provide an array constructor expression
```
String[] names = ArrayAlg.minmax("Tom", "Dick", "Harry"); // wrong
String[] names = ArrayAlg.minmax(String[]::new, "Tom", "Dick", "Harry");

```
### 8.6.7 Type Variables Are Not Valid in Static Contexts of Generic Classes
```
private static T singleInstance; // ERROR
```
### 8.6.8 You Cannot Throw or Catch Instances of a Generic Class
You can neither throw nor catch objects of a generic class. In fact, it is not even legal for a generic class to extend Throwable
```
public class Problem<T> extends Exception { /* . . . */ } // ERROR--can't extend Throwable
```
### 8.6.9 You Can Defeat Checked Exception Checking

### 8.6.10 Beware of Clashes after Erasure
A class or type variable may not at the same time be a subtype of two interface types which are different parameterizations of the same interface.
There would be a conflict with the synthesized bridge methods

---
## 8.7 Inheritance Rules for Generic Types
There is no relationship between `Pair <S>` and `Pair <T>`. Even S is superclass of T. Subclass and superclass refer to same object.
---
## 8.8 Wildcard Types
### 8.8.1 The Wildcard Concept
> In a wildcard type, a type parameter is allowed to vary.
```
Pair<? extends Employee>
```
### 8.8.2 Supertype Bounds for Wildcards
> `? super Manager`
Wildcard bounds are similar to type variable bounds, but they have an added capability—you can specify a supertype bound

### 8.8.3 Unbounded Wildcards

`Pair<?>` VS `Pair`
* `setFirst` method can never be called for `Pair<?>`, not even with an `Object`, But you can call the `setFirst` method of the raw Pair class with any `Object`=

### 8.8.4 Wildcard Capture
 we can’t write code that uses `?` as a type.
---
## 8.9 Reflection and Generics
### 8.9.1 The Generic Class Class
The `Class` class is now generic : `String.class` is an object of class `Class<String>`

### 8.9.2 Using `Class<T>` Parameters for Type Matching.
The type parameter `T` of the `makePair` method matches `Employee`, and the compiler can infer that the method returns a `Pair<Employee>`.
 
### 8.9.3 Generic Type Information in the Virtual Machine.
you can reconstruct everything about generic classes and methods that their implementors declared
* In order to express `generic` type declarations, use the interface Type in the java.lang.reflect package.

### 8.9.4 Type Literals
with generic classes, erasure poses a problem. How can you have different actions for `arrayList<Integer>` and `arrayList<String>`
* You can capture an instance of the Type interface that you encountered in the preceding section. Construct an anonymous subclass
```
var type = new TypeLiteral<ArrayList<Integer>>(){} 
```
