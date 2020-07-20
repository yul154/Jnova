# Generics
* Generics stop runtie errors at compile time.
* enable types (classes and interfaces) to be parameters when defining classes, interfaces and methods in order to provide compile-time type safety
* use `<>` to specify parameter types in generic class creation
* create an instance of generic class 
```
BaseType <Type> obj = new BaseType <Type>()
```
* Create an instance of class
```
class Test<T> 
{ 
    // An object of type T is declared 
    T obj; 
    Test(T obj) {  this.obj = obj;  }  // constructor 
    public T getObject()  { return this.obj; } 
} 
```
## Generic Collections
### List
* `iterator` is also a generic interface
```
List<Integer> nums= new ArraryList<Integer>();
nums.add(0);
nums.add(1)
for (int i=0;i<nums.size();i++)
{
  System.out.println(nums.get(i))
}
final Interator<Integer> iterator= nums.Iterator();
while (iterator.hasNext())
{
  iterator.next();
}

for (int i: nums)
{
  System.out.println(i);
}
```
### Map
* Three iteration combinations
```
Map<Integer,String> map= new HashMap<Integer, String>;
map.put(1,"one");
map.pint(2."two");
for (Integer num: map.keysets()){//.values(), entrySet()
    System.out.println(num)}
```

## Generic Classes and Interfaces
* Implementing a Generic Type
```
customercomparator implements comparator <Integer>
```
* Passing a Parameter to a Generic type
```
reverser <T> implements c2<T>
```
* Type Bounds
```
sorteds <T extends  Comparaable<T>>
```
## Generic on Methods

## Wildcard

Upper Bounded |Lower Bounded| Unbounded
--------------|-------------|----------
`List<? extends Cls>`| `List<? super Cls>`| `List<?>`

### Substitution principle(LSP)
* When we 

### Upper bounded
* restricts the unknown type to be a specific type or a subtype of that type and is represented using the extends keyword
* use the wildcard character ('?'), followed by the extends keyword, followed by its upper bound.
* `extends` is used in a general sense to mean either "extends" (as in classes) or "implements" (as in interfaces).
* The upper bounded wildcard, `<? extends Foo>`, where Foo is any type, matches Foo and any subtype of Foo.

### Lower bounded
* restricts the unknown type to be a specific type or a super type of that type
* expressed using the wildcard character ('?'), following by the super keyword, followed by its lower bound:` <? super A> `

### Unbounded 
* Single <?> is called an unbounded wildcard in generic and it can represent any type, similar to Object in Java

## Raw Type
*  using a generic type without specifying a type parameter
* `List` is a raw type, while `List<String>` is a parameterized type
```
List list = new ArrayList(); // raw type
List<Integer> listIntgrs = new ArrayList<>(); // parameterized type
```
* Raw type provide compatibbility
