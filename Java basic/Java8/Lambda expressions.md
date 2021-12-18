# Lambda Expression

* Implement interface
```
public interface FileFilter{
  boolean accpet(File file)
}

public class JavaFileFilter implements FileFilter{
  
  public boolean accept(File file){
    return file.getName().endsWith(".java");
    }
}
JavaFileFilter fileFilter= new JavaFileFilter();
File dir = new File("d:/tmp");
File[] javafiles= dir.listFiles(fileFilter);
```
* Use an anonymous class
```
FileFilter fileFilter= new FileFilter() {
    @Override
    public boolean accept(File file){
      return file.getName().endsWith(".java")}
  }
File dir = new File("d:/tmp");
File[] javafiles= dir.listFiles(fileFilter); 
```
* Lambda expression
```
FileFilter filter= (File file) -> file.getName().endsWith(".java")
```
## Introduction of Lambda expression
* It is a more reableable way of writing instances of anonymous classes.
* express instances of single-method classes more compactly.
* it is a more easy way to write instance anonymous inner class or single class which implement functional interface
* Compare anonymous class,
  * lambda expression is created without `new`, function declartion.
### Lambda expression Syntax

```
parameter -> expression body

Comparator<String> c= (String s1, String s2) -> Integer.compare(s1.length(),s2.length());
```

* Optional .
```
//with type declaration
MathOperation addition = (int a, int b) -> a + b;	

//with out type declaration
MathOperation subtraction = (a, b) -> a - b;

//with return statement along with curly braces
MathOperation multiplication = (int a, int b) -> { return a * b; };

//without return statement and without curly braces
MathOperation division = (int a, int b) -> a / b;
```
* Lambda Expression can be put in a vairiable
  * A lambda can be taken as a method parameter, and can be returned by a method.

------------------------------------

# Functional Interface
> An interface with only one `abstract` method
* A functional interface can have any number of default methods.
* Lambda is a type of functional interface
* Examples
```
public interfaxec Runnable{
    run();
}

public interface Comparator<T>{
  int compare(T t1,T t2);
}
```
* A functional interface can be annotated

## Funtional Interfaces Toolsbox
* `java.util.function`: a rich set of functional interfaces
### 1. Suppliers
* does not take any arguments and provide new object. It is typically used for lazy generation of values
```
public interface Supplier<T>{
    T get();
}
```
```
Map<String, Integer> nameMap = new HashMap<>();
Integer value = nameMap.computeIfAbsent("John", s -> s.length());
```
### 2. Consumers
*  the Consumer accepts an object and returns nothing. 
* It is a function that is representing side effects.
```
public interface Consumer<T>{
    void accpet(T t);
}

public BiConsumer<T, U>{# can be different type 
    Void accpet(T t,U u);
}
```

### 3. Predicate
* a function that receives an object as parameter and returns a boolean value.
```
public interface Predicate<T>{

    boolean test(T t);
 }

public BiPredicate<T, U>{# can be different type 

    boolean test(T t,U u);
 }
```
#### 4. Function
*  receives one value and returns another.
```
public interface Function<T, R>{

  R apply(T t);
}

public BiFunction<T, U, R>{# can be different type 

    R apply(T t,U u);
```
#### 4.1 Special case: UnaryOperator
* take a object and returns another object of the same type

```
public interface UnaryOperator<T> extends Function<T,T>{
}
```
*  BinaryOperator
```
public interface BinaryOperator<T> extends BiFunction<T,T,T>{
}
```
----------------------------
#  Method References
> clearer to refer to the existing method by name 
* A method reference can be identified by a double colon separating a class or object name and the name of the method
```
Comparator<Integer> c=(i1,i2)-> Integer.compare(i1,i2);

Comparator<Integer> c=Integer:: compare;
```
----------------------
## Collection with lambda
* Sorting collections
  * We can use `Comparator` interface to sort, It only contain one abstract method:: `compare()`. 
```
Collections.sort(al, (o1, o2) -> (o1 > o2) ? -1 : (o1 < o2) ? 1 : 0); 
                                       
TreeSet<Integer> h =  new TreeSet<Integer>((o1, o2) -> (o1 > o2) ?  -1 : (o1 < o2) ? 1 : 0);
```
* `forEach `
  * ` void forEach(Consumer<? super T> action)`
  * It is defined in Iterable and Stream interface. 
  * It is a default method defined in the Iterable interface
  * `forEach`, we can iterate over a collection and perform a given action on each element,
```
for (String name : names) {
    System.out.println(name);
}
names.forEach(name -> {
    System.out.println(name);
});
```
* Different between `forEach` and `for`
  * the enhanced for-loop is an external iterator whereas the new forEach method is an internal one
  * get an iterator and step over it, that is an external iterator
  * pass a function object to a method to run over a list, that is an internal iterator
```
List<Customer> list=...;
list.forEach(System.out::prinln);
```
---------------------------
# Default and static methods
> A default method is a method with an implementation – which can be found in an interface.

* It allows to change the old interfaces without breaking the existing implementations.
* `Collection` interface can have a default implementation of `forEach` method without requiring the classes implementing this interface to implement the same.
* It allows new patterns
* static methods also allowed in Java 8 interfaces 
----------------------------------------------------
# Functional composition
* a technique to combine multiple functions into a single function which uses the combined functions internally.

## Predicate Composition
* `and()`: a default method which used to combine two other predicate functions in the same way,
  * it will return true, if both of the Predicate instances it was composed from also return true. 
* `or()`: it will return False, if either of the Predicate instances it was composed from also return False . 

## Function Composition
* `compose()`: first call the Function passed as parameter to compose(), and then it will call the Function which compose() was called on
* `andThen()`:first call the Function that andThen() was called on, and then it will call the Function passed as parameter to the andThen()
```
Function<Integer, Integer> multiply = (value) -> value * 2;
Function<Integer, Integer> add      = (value) -> value + 3;

Function<Integer, Integer> com1 = multiply.compose(add);
Function<Integer, Integer> com2 = multiply.andThen(add);
Integer result1 = addThenMultiply.apply(3);
com1--> 12, com2-->9
```
