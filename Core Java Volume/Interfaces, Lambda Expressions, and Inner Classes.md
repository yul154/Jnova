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
### 6.2.1 Why Lambdas?
Sometimes, A block of code was passed to someone— a timer, or a sort method. That code block was called at some later time. Up to now, giving someone a block of code hasn’t been easy in Java

### 6.2.2 The Syntax of Lambda Expressions
```
(String first, String second) -> first.length() - second.length()
```
If the code carries out a computation that doesn’t fit in a single expression
```
 
(String first, String second) ->
{
if (first.length() < second.length()) return -1; else if (first.length() > second.length()) return 1; else return 0;
}
```
If a lambda expression has no paramete
```
() -> {for (int i = 100; i >= 0; i--) System.out.println(i); }
```
If the parameter types of a lambda expression can be inferred, you can omit them.
```
Comparator<String> comp = (first, second) -> first.length() - second.length();
```
### 6.2.3 Functional Interfaces
> You can supply a lambda expression whenever an object of an interface(functional interface)with a single abstract method is expected. 

Why a functional interface must have a single abstract method
> it has always been possible for an interface to redeclare methods from the Object class 

The Java API defines a number of very generic functional interfaces
* `BiFunction<T, U, R>`, describes functions with parameter types T and U and return type R.
* `Predicate<T>`,receive type t and return boolean,(removeIf)
* `Supplier<T>`:has no arguments and yields a value of type T , used for lazy evaluation.
```
LocalDate hireDay = Objects.requireNonNullOrElseGet(day, () -> new LocalDate(1970, 1, 1));//only calls the supplier when the value is needed.
```

### 6.2.4 Method References
the `::` operator separates the method name from the name of an object or class.
1. `object::instanceMethod`
2. `Class::instanceMethod`
3. `Class::staticMethod`


* The compiler to produce an instance of a functional interface 
* The compiler needs to figure out which one to use, depending on context

### 6.2.5 Constructor References
> Constructor references are just like method references, except that the name of the method is new.
```
ArrayList<String> names = . . .;
Stream<Person> stream = names.stream().map(Person::new); 
List<Person> people = stream.collect(Collectors.toList());
```
* it will call `Person(String)` constructor due to context
* Form constructor references with array types. `int[]::new`


### 6.2.6 Variable Scope
A lambda expression has three ingredients:
1. Ablockofcode
2. Parameters
3. Values for the free variables —the variables that are not parameters and not defined inside the code

Why can capture free variable into lambda
* one can translate a lambda expression into an object with a single method, so that the values of the free variables are copied into instance variables of that object.

The rule of outside variable in lambda
* **In a lambda expression, you can only reference variables whose value doesn’t change**
* **Any captured variable in a lambda expression must be effectively final.**
* It is illegal to declare a parameter or a local variable in the lambda that has the same name as a local variable.
* When you use the this keyword in a lambda expression, you refer to the this parameter of the method that creates the lambda
```
public class Application
{
  public void init()
  {
    ActionListener listener = event -> {System.out.println(this.toString()); ... //calls the toString method of the Application object,
  } 
}
```
### 6.2.7 Processing Lambda Expressions
>  How to write methods that can consume lambda expressions.

The point of using lambdas is *deferred execution*.

### 6.2.8 More about Comparators
The Comparator interface has a number of convenient static methods for creating comparators.
* The static `comparing` method takes a “key extractor” function that maps a type `T` to a comparable type,The function is applied to the objects to be compared, and the comparison is then made on the returned keys.
```
Arrays.sort(people, Comparator.comparing(Person::getName));
```
* can chain comparators with the `thenComparing` method
```
Arrays.sort(people, Comparator.comparing(Person::getLastName).thenComparing(Person::getFirstName));
Arrays.sort(people, Comparator.comparing(Person::getName, (s, t) -> Integer.compare(s.length(), t.length())));
```

* If your key function can return null, you will like the `nullsFirst` and `nullsLast` adapters,The `nullsFirst` method needs a comparator
```
Arrays.sort(people, comparing(Person::getMiddleName, nullsFirst(naturalOrder().reversed())));
```
---
## 6.3 Inner Classes
An inner class is a class that is defined inside another class
* Inner classes can be hidden from other classes in the same package.
* Inner class methods can access the data from the scope in which they are defined—including the data that would otherwise be private.
* An object that comes from an inner class has an implicit reference to the outer class object that instantiated it. 

### 6.3.1 Use of an Inner Class to Access Object State
An inner class method gets to access both its own data fields and those of the outer object creating it.
