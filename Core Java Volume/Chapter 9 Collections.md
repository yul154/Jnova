# Catalog
1. The Java Collections Framework
2. Interfaces in the Collections Framework 
3. Concrete Collections
4. Maps
5. Views and Wrappers
6. Algorithms
7. Legacy Collections
---
## 9.1 The Java Collections Framework
### 9.1.1 Separating Collection Interfaces and Implementation

The Java collection library separates interfaces and implementations
```
public interface Queue<E> {
	
	void add(E element);
	E remove();
	int size();

}

public class CircularArrayQueue<E> implements Queue<E> {
	private int head;
	private int tail;
	CircularArrayQueue(int capacity){};
	public void add (E element){};
	public E remove() {return E};
	public int size() {.};
	private E[] elements;
	
}
```
Use the concrete class only when you construct the collection object. Use the interface type to hold the collection reference
```
Queue<Customer> expressLane = new CircularArrayQueue<>(100);
expressLane.add(new Customer("Harry"));
```

>  you want to implement your own queue class, you will find it easier to extend AbstractQueue than to implement all the methods of the Queue interface.

### 9.1.2 The Collection Interface
`Collection` interface is the fundamental interface for collection classes.
```
public interface Collection<E>{
    boolean add(E element);
    Iterator<E> iterator(); //returns an object that implements the Iterator interface.
}
```
### 9.1.3 Iterators
```
public interface Iterator<E>
   {
      E next();
      boolean hasNext();
      void remove();
      default void forEachRemaining(Consumer<? super E> action);
}
```
*  the *for each* loop into a loop with an iterator.
*  The *for each* loop works with any object that implements the `Iterable` interface,
```
public interface Iterable<E>]{
     Iterator<E> iterator();
}
```
The `Collection` interface extends the `Iterable` interface. So *for each* loop with any collection in the standard library.

`iterator.forEachRemaining(element -> do something with element);
List<String> fruits = new ArrayList<>();
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Grapes");
fruits.add("Orange");

Iterator<String> iterator = fruits.iterator();

iterator.forEachRemaining((fruit) -> System.out.println(fruit));`

` forEachRemaining`: The order in which the elements are visited depends on the collection type

* It is illegal to call `remove` if it wasn’t preceded by a call to `next`.
```
it.remove();//error
it.remove();//error

it.remove(); 
it.next(); 
it.remove(); // OK
```
### 9.1.4 Generic Utility Methods

the library supplies a class `AbstractCollection` that leaves the fundamental methods  `size` and `iterator` abstract but implements others.
---
## 9.2 Interfaces in the Collections Framework
<img width="448" alt="Screen Shot 2021-10-09 at 9 52 04 PM" src="https://user-images.githubusercontent.com/27160394/136660614-4f06b636-ab49-4f57-8cc7-2a8430fce678.png">

Set : 
  * The `equals` method of a set should be defined so that two sets are identical if they have the same elements, but not necessarily in the same order. 
  * The `hashCode` method should be defined so that two sets with the same elements yield the same hash code.
---
## 9.3 Concrete Collections
<img width="461" alt="Screen Shot 2021-10-09 at 10 06 26 PM" src="https://user-images.githubusercontent.com/27160394/136661182-2b92ecd1-77b7-430c-a66a-18f439b7c937.png">

### 9.3.1 Linked Lists
In the Java programming language, all linked lists are actually doubly linked;
* There is no `add` method in the Iterator interface. Bcs some of impelmented class have no order
* The collections library supplies a subinterface `ListIterator` that contains an add method.

### 9.3.2 Array Lists
Why use an `ArrayList` instead of a `Vector`?
*  All methods of the Vector class are synchronized. It is safe to access a Vector object from two threads
*  ArrayList methods are not synchronized, But it is fast.

### 9.3.3 Hash Sets
If you define your own classes, you are responsible for implementing your own `hashCode` method
* If `a.equals(b)` then a and b must have the same hash code.

In Java, hash tables are implemented as arrays of linked lists. Each list is called a bucket
* As of Java 8, the buckets change from linked lists into balanced binary trees when they get full.

The Java collections library supplies a `HashSet` class that implements a set based on a `hash table`

### 9.3.4 Tree Sets
> The `TreeSet` class is similar to the hash set, with one added improvement. A tree set is a *sorted* collection.
 The elements in `TreeSet`must i
 1. mplement the `Comparable` interface, 
 2.  you must supply a Comparator when constructing the set.
```
TreeSet<String> staff = new TreeSet<String>(Comparator.comparing(String::length));
```
### 9.3.5 Queues and Deques
Java 6 introduced a `Deque` interface. It is implemented by the `ArrayDeque` and `LinkedList` classes

Operatipn|Throws exception	|Returns special value
|----|-----------------|-------------------
Insert	| add(e)	| offer(e)
Remove	| remove()|	poll()
Examine	| element()|	peek()

### 9.3.6 Priority Queues
> A priority queue retrieves elements in sorted order after they were inserted in arbitrary order.

By default, the priority queue in Java is min Priority queue with natural ordering. To make it max, we have to use a custom comparator.
```
PriorityQueue<Integer> maxPQ = new PriorityQueue<>((a,b) -> b.compareTo(a)); 

```
The priority queue makes use of an elegant and efficient data structure called a heap.
* A heap is a self-organizing binary tree in which the add and remove operations cause the smallest element to gravitate to the root,
---
## 9.4 Maps
### 9.4.1 Basic Map Operations
> Java provided `HashMap` and `TreeMap`. Both classes implement the `Map` interface.

Retrieve data
```
var staff = new HashMap<String, Integer> (); // HashMap implements Map
staff.put("leo", 120);
int id = staff.get("asd"); // return null
int id = staff.getOrDefault("asd",0)//return 0
```
* it is able to set default for no present keys by using `getOrDefault`

Traveral
* The easiest way of iterating over the keys and values of a map is the `forEach`
```
staff.forEach((k,v) -> System.out.println(k+"-->"+v));
```
### 9.4.2 Updating Map Entries

If the key is encountered in the first time, Then `get` return null, and a  `NullPointerException`
```
 counts.put(word, counts.get(word) + 1);
```

 use the `getOrDefault` method
 ```
counts.put(word, counts.getOrDefault(word,0) + 1);
 ```
 OR use `putIfAbsent`: It only puts a value if the key was previously absent
 ```
counts.putIfAbsent(word, 0);
counts.put(word, counts.get(word) + 1); 
 ```
 Or use `merge` after java 1.8: associates word with 1 if the key wasn’t previously present, and otherwise add 1, using the `Integer::sum` function.
 ```
 counts.merge(word, 1, Integer::sum);
 ```
 ### 9.4.3 Map Views
 There are three views: 
 1. the set of keys, 
 2. the collection of values (which is not a set)
 3. the set of key/value pairs
 ```
 Set<K> keySets();
 Collections<V> values();
 Set<Map,Entry<K,V>> entrySet();
 ```
If you want to look at both keys and values,
```
for (Map.Entry<String, Employee> entry : staff.entrySet()) { // var entry : map.entrySet()
      String k = entry.getKey(); 
      Employee v = entry.getValue(); 
      do something with k, v
}

map.forEach((k,v) ->{}); 
```
### 9.4.4 Weak Hash Maps
> The `Weak Hash Map` cooperates with the garbage collector to remove key/value pairs when the only reference to the key is the one from the hash table entry.
Work flow
1. The WeakHashMap uses *weak references* to hold keys, A WeakReference object holds a reference to another object.
2. If the object is reachable only by a *WeakReference*, the garbage collector still reclaims the object.
3. Places the weak reference that led to it into a queue
4. *WeakHashMap* periodically check that queue for newly arrived weak references
5. The arrival of a weak reference in the queue signifies that the key was no longer used by anyone and has been collected
6. The *WeakHashMap* then removes the associated entry.

### 9.4.5 Linked Hash Sets and Maps
> The `LinkedHashSet` and `LinkedHashMap` classes remember in which order you inserted item.
LinkedHashMap has special constructor to create the access order map
```
LinkedHashMap<K, V>(initialCapacity, loadFactor, true)
```
* Every time you call `get` or `put`, the affected entry is removed from its current position and placed at the end of the linked list of entries.
* Access order is useful for implementing a “least recently used” discipline for a cach

### 9.4.6 Enumeration Sets and Maps
> The `EnumSet` is an efficient set implementation with elements that belong to an `enumerated` type. 
* the `EnumSet` is internally implemented simply as a sequence of bits.
```
enum Weekday { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY 
EnumSet<Weekday> always = EnumSet.allOf(Weekday.class);
EnumSet<Weekday> never = EnumSet.noneOf(Weekday.class);
```
* The `EnumSet` class has no public constructors. Use a static factory method to construct the set.

`EnumMap`is a map with keys that belong to an enumerated type
```
var personInCharge = new EnumMap<Weekday, Employee> (Weekday.class);
```
### 9.4.7 Identity Hash Maps
* the hash values for the keys computed by `System.identityHashCode` instead of `hashCode` method.
* `System.identityHashCode`that Object.hashCode uses to compute a hash code from the object’s memory address.
*  Using reference-equality in place of object-equality when comparing keys (and values).
* The IdentityHashMap uses` ==,` not `equals`.
* Different key objects are considered distinct even if they have equal contents. 
---
## 9.5 Views and Wrappers
the `keySet` method returns an object of a class that implements the `Set` interface and whose methods manipulate the original map
### 9.5.1 Small Collections
> Java 9 introduces static methods yielding a set or list with given elements, and a map with given key/value pairs.
```
List<String> names = List.of("Peter", "Paul", "Mary");
Map<String, Integer> scores = Map.of("Peter", 2, "Paul", 3, "Mary", 5);
 Map<Integer,String> map = Map.ofEntries（entry(1, "a");
```
*  The `List` and `Set` interfaces have eleven `of` methods with zero to ten arguments
*  `ofEntries` that accepts an arbitrary number of `Map.Entry<K, V>` objects,
*  These collection objects are unmodifiable
*  If you want a mutable collection, you can pass the unmodifiable collection to the constructor.
```
var names = new ArrayList<>(List.of("Peter", "Paul", "Mary"));
```
* `Collections.nCopies(n, anObject)`: returns an immutable object that implements the List interface
```
List<String> settings = Collections.nCopies(100, "DEFAULT");
```
### 9.5.2 Subranges
Use the `subList` method to obtain a `view` into the subrange of the `list`
```
List<Employee> group2 = staff.subList(0,10);
```
For sorted sets and maps, you use the sort order, not the element position, to
form subranges.
```
SortedSet<E> subSet(E from, E to)
SortedMap<K, V> subMap(K from, K to)
```
The `NavigableSet` interface introduced in Java 6 gives more control over these subrange operations.
```
NavigableSet<E> subSet(E from, boolean fromInclusive, E to,boolean toInclusive)
NavigableSet<E> headSet(E to, boolean toInclusive)
NavigableSet<E> tailSet(E from, boolean fromInclusive)
```
### 9.5.3 Unmodifiable Views
These views add a runtime check to an existing collection. 
  * If an attempt to modify the collection is detected, an exception is thrown and the collection remains untouched.

The views wrap the interface and not the actual collection
  *  so you only have access to those methods that are defined in the interface
  *  the LinkedList class has `addFirst` and `addLast`, that are not part of the List interface. These methods are not accessible through the unmodifiable view.
  
### 9.5.4 Synchronized Views
> The view mechanism to make regular collections thread safe
```
var map = Collections.synchronizedMap(new HashMap<String, Employee>());
```
* You can now access the map object from multiple threads.

### 9.5.5 Checked Views
> Checked views are intended as debugging support for a problem that can occur with generic types
---
## 9.6 Algorithms
The Java library is not quite so rich, but it does contain the basics: sorting, binary search, and some utility algorithms.
### 9.6.1 Why Generic Algorithms?
you only need to implement your algorithms once.In order to achieve same purpose with different data type.
```
public static <T extends Comparable> T max(Collection<T> c)
```
### 9.6.2 Sorting and Shuffling
**Sorting**
```
Collections.sort(list);
```
* We can define sort order
```
Collections.sort(Comparator.comparingInt(String::length))
list.sort(Comparator.reverseOrder());
```
* The sort algorithm used in the collections library is a bit slower than Quick- Sort,but stable(it doesn’t switch equal
elements.)

**Shuffle**
> it randomly permutes the order of the elements in a list
```
ArrayList<Card> cards = . . .; 
Collections.shuffle(cards);
```
### 9.6.3 Binary Search
> Note that the collection must already be sorted
 If the collection is not sorted by the `compareTo` element of the `Comparable` interface, supply a `comparator` object as well.
 ```
 i = Collections.binarySearch(c, element, comparator);
 ```
 If the return value is negative,
 * you can use the return value to compute the location where you should insert element into the collection to keep it sorted.
 * insertionPoint = -i - 1;
```
List<Integer> lst =  new ArrayList<>();
lst.add(1);
lst.add(2);
lst.add(3);
System.out.println(Collections.binarySearch(lst, 6)); // return -4, add position is 3
```
### 9.6.5 Bulk Operations
bulk opertation can do operations on intersection part of data.
```
colum1.removeAll(colum2)
colum1.retainAll(colum2);
```
* remove or retain the element from colum1 that are or not in  column2

### 9.6.6 Converting between Collections and Arrays
The array returned by the toArray method was created as an `Object[]` array
* you cannot use a cast
* So it is unable to do same type date convert.

Solutions 1: use a variant of the `toArray` method and give it an array of length 0 of the type
```
String[] values = staff.toArray(new String[0]);
```
Solutions 2：If you like, you can construct the array to have the correct size:
```
staff.toArray(new String[staff.size()]);
```

#### `Collection.toArray(new T[0]) or .toArray(new T[size])`

`toArray()` 
*  allocates a new in-memory array with a length equal to the size of the collection. 
*  Internally, it invokes the `Arrays.copyOf` on the underlying array backing the collection. 

`.toArray(new T[size])`
* If the length of the pre-allocated array < the collection's size, a new array of the required length and the same type is allocated
* If the length of input array >=  the collection's elements, it's returned with those elements inside:

**Summary**: `toArray(new T[0])` variant is faster than the `toArray(new T[size])` and, therefore, should always be the preferred option when we have to convert a collection to an array

[Explaination](https://www.baeldung.com/java-collection-toarray-methods)

---
## 9.7 Legacy Collections
> A number of “legacy” container classes have been present before there was a collections framework.

<img width="324" alt="Screen Shot 2021-10-10 at 1 34 11 PM" src="https://user-images.githubusercontent.com/27160394/136683756-45fe2da4-c085-48d9-80e1-2a1b9183efb0.png">

###  9.7.1 The Hashtable Class
The `Hashtable` methods are synchronized. 
* If you do not require compatibility with legacy code, you should use a `HashMap` instead. 
* If you need concurrent access, use a` ConcurrentHashMap`

### 9.7.2 Enumerations
The `Enumeration` interface has two methods, `hasMoreElements` and `nextElement`.
* These are entirely analogous to the `hasNext` and `next` methods of the Iterator interface.

### 9.7.3 Property Maps
Particular characteristics
* The keys and values are strings.
* The map can easily be saved to a file and loaded from a file. 
* There is a secondary table for default values.

`Propertie`s implements a property map: useful in specifying configuration options for programs.
```
var settings = new Properties(); 
settings.setProperty("width", "600.0");
var out = new FileOutputStream("program.properties"); 
settings.store(out, "Program Properties");
```
The `Properties` class has two mechanisms for providing defaults.
1.  you can specify a default that should be used automatically when the key is not present.
```
String filename = settings.getProperty("filename", "");
```
2. you can pack all the defaults into a secondary property map and supply that map in the constructor of your primary property map
```
var defaultSettings = new Properties();
var settings = new Properties(defaultSettings);
```
### 9.7.4 Stacks
The `Stack` class extends the `Vector` class
* which is not satisfactory from a theoretical perspective -you can apply such un-stack-like operations as insert and remove to insert and remove values anywhere

### 9.7.5 Bit Sets
> The Java platform’s BitSet class stores a sequence of bits.
The `BitSet` class gives you a convenient interface for reading, setting, and resetting individual `bit`
* A bit set packs the bits into bytes, so it is far more efficient to use a bit set than an ArrayList of Boolean objects.
