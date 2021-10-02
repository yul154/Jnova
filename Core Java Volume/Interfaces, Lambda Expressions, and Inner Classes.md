# In this chapter
1. Interfaces
2. Lambda Expressions 
3. Inner Classes
4. Service Loaders
5. Proxies
----
## 6.1 Interfaces
### 6.1.1 The Interface Concept
>  An interface is not a class but a set of requirements for the classes
* `sort` method of `Arrays` class when the object must belong to class that implement `Comparable` interface.
```
public  interface Comparable<T>{
  int compareTo(T other)
}
```
* it is mandatory to implement all the methods in a class that implements an `interface `until and unless that class is declared as an `abstract` class.
* All methods of an interface are automatically public. No need to declare public on methods
* `compareTo` method should be compatibble with `equals` methods. bubt exception is `BigDecimal`

1. If subclasses have different notions of comparison,outlaw comparison of objects that belong to different classes.
2. If there is a common algorithm for comparing subclass objects, simply provide a single compareTo method in the superclass and declare it as final

### 6.1.2 Properties of Interfaces
* we can use `instanceof` to check whether an object implements an interfaces
* you can supply constants(always `public static final`) in interfaces

### 6.1.5 Default Methods
> set a `default` implementation for any interface method

* `default` method will not break the current implementation of interfaces

### 6.1.6 Resolving Default Method Conflicts

if the exact same method is defined as a default method in one interface and then again as a method of a superclass or another interface?
1. Super classes win if a superclass provides a concrete methods.
2. Need to provide method implemtations in sub class

Inheriting the same method from superclass or interface
*  only the superclass method matters

### 6.1.7 Interfaces and Callbacks
> Specify the action that should occur whenever a particular event happen

### 6.1.8 The Comparator Interface
```
public interface Comparator<T>
   {
      int compare(T first, T second);
   }
```
* `comparator` can define different sort stratgy.

### 6.1.9 Object Cloning
> `Cloneable` interface
The `clone` method is a `protected` method of `object`, A subclass can call a protected clone method only to clone its own objects.

* default cloning operation is “shallow”—it doesn’t clone objects that are referenced inside other objects
 1. If the subobject shared between the original and the shallow clone is immutable, then the sharing is safe. 
 2. Subobjects are mutable, and you must redefine the clone method to make a deep copy that clones the subobjects 
 
* . A tagging interface(`Cloneable`) has no methods; its only purpose is to allow the use of `instanceof` in a type inquiry.
---
## 6.2 Lambda Expressions
