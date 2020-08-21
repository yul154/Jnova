# New feature in java8
* Lambda Expressions
* Functional interface
* Steam API
* Default Methods
* Method References
* Date and Time API
* String and IO 
* Optional class

# what is Lambda function 
* The Lambda expression is used to provide the implementation of an interface which has functional interface.
* it is a more easy way to write instance anonymous inner class or single class which implement functional interface
* It's lik an anonymous function so it has everything a function does without a name, it actually a shorthand to write and us functions
* it an object of typed functional interface
# java8 functional interface
*  an interface that contains only one abstract method. 
*  The java.util.function package in Java 8 contains many builtin functional interfaces  like Suppliers,consumers, function, prediction

# Sorted method in java8 and java 6
* Java 8 provides new ways of defining Comparators by using lambda expressions and the comparing() static factory method.
```
listDevs.sort((Developer o1, Developer o2)->o1.getAge()-o2.getAge());
listDevs.sort((o1, o2)->o1.getName().compareTo(o2.getName()));		
```
```
humans.sort(Comparator.comparing(Human::getName).thenComparing(Human::getAge));
```
* Comparable is an interface defining a strategy of comparing an object with other objects of the same type.
* The sorting order is decided by the return value of the compareTo() method.
* Comparator is external to the element type we are comparing

# Default method
* Before Java 8, interfaces could have only abstract methods. The implementation of these methods has to be provided in a separate class. So, if a new method is to be added in an interface, then its implementation code has to be provided in the class implementing the same interface. 
* allows the developer to add new methods to the interfaces without breaking their existing implementation.
* Collection interface can have a default implementation of `forEach` method without requiring the classes implementing this interface to implement the same.

# Explain Stream api
* Streams bring functional programming to java
* it provides a functional approach to efficient processing collections of objects. 
* A stream pipeline consists of a source, followed by intermediate opertaions and a terminal operations
* Streams don’t change the original data structure, they only provide the result as per the pipelined methods.

## Terminal operations and Intermediate operations
* Intermeidate operations such as filter, map, sort  return a stream so we can chain multiple
* orde matters, filter first, and then sort or map
* Terminal operations such as forEach,Collection,reduce are either void o return non-stream result
*  because intermediate operations are not evaluated unless terminal operation is invoked.

# what is Method References
* Method references are a special type of lambda expressions.
* A method reference can be identified by a double colon separating a class or object name and the name of the method

# Date and Time API
1. Not thread safe : Unlike old java.util.Date which is not thread safe the new date-time API is immutable and doesn’t have setter methods.
2. Less operations : In old API there are only few date operations but the new API provides us with many date operations.
* New API, package is java.time
* a point on the time line which precision is the nanosecond.
* Duration: time elapsed between two different Instants.
* Local : Simplified date-time API with no complexity of timezone handling.
* Zoned : Specialized date-time API to deal with various timezones.

# Do you know java8 observer
The Observer Pattern. Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically
* `update()`

# What’s the difference between spliterator and iterator(3)

* A Spliterator can be used to split given element set into multiple sets so that we can perform some kind of operations/calculations on each set in different threads independently, possibly taking advantage of parallelism.
* An Iterator is a simple representation of a series of elements that can be iterated over. 

# What different between foreach loop and for loop
The biggest differences are that a foreach loop processes an instance of each element in a collection in turn, while a for loop can work with any data and is not restricted to collection elements alone

# default method, add default method cause error?(2)
