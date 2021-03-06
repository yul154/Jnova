# Collection Interface

* It is root interface of Collection hierarchy
* A collection represents a group of object, know as its elements
* The JDK does not provide any direct implementations of this interface
* it provides implementations of more specific subinterfaces like Set and List,Queue

## Key methods
1. `add(E e)`,`addAll(Collection c)`
2. `iterator()`
3. `remove()`
4. `size()`
5. `contains()`
5. `spliterator()`
6. `stream()`
7. `toArray()`

## Subinterfaces

### 1.List interfaces
*  A list is an order collection
*  The user can access elements by their integer index 
* lists typically allow duplicate elements
* The List interface provides a special iterator, called a ListIterator, that allows element insertion and replacement, and bidirectional access 

**Key methods**
1. `get(int index)`
2. `indexof(object o)`
3. `lastIndexof(oject 0)`
4. `sort(comparator)`
5. `sublist()`


#### Implementations of List
* LinkedList: Doubly-linked list implementation of the List and Deque interfaces. `addFirst()`,`addLast()`,`getFirst()`,`getLast()`
* ArrayList: Resizable-array implementation of the List interface. `ensureCapacity(int minCapacity)`,`trimToSize()`
* Stack
* Vector

### 2.Set interface
* A collection that contains no duplicate elements.
* It represents an unordered collection of objects.

#### Implementations of Set
* Hashset: This class implements the Set interface, backed by a hash table (actually a HashMap instance). 
* Treeset: A NavigableSet implementations. The elements are ordered byb natural ordering,or by a Comparator. `ceiling()`,`comparator`,`subset()`,`tailSet()`

### 3.Queue interface
* represents a classical queue data structure, where objects are inserted into one end of the queue
*  queues provide additional insertion, extraction, and inspection operations. one throws an exception if the operation fails, the other returns a special value

<img width="338" alt="Screen Shot 2020-07-23 at 7 12 51 PM" src="https://user-images.githubusercontent.com/27160394/88350367-8d321400-cd18-11ea-8131-ce5fe13ecaa3.png">

#### Implementations of Queue
* PriorityQueue:  The elements of the priority queue are ordered according to their natural ordering, or by a Comparator 
---
# Map interface
* An object that maps keys to values
* A map cannot contain duplicate keys; each key can map to at most one value.
* The Map interface provides three collection views, which allow a map's contents to be viewed as a set of keys, collection of values, or set of key-value mappings

**Key Methods**
1. `compute()`
2. `containsKey()`
3. `get(object Key)`
4. `keyset()`
5. `entryset()`
6. `put()`
7. `merge()`

## Implmentations

1. Hashmap:HashMap stores items as key/value pairs. Values can be accessed by indexes, known as keys. 
2. Hashtable: Hashtable is similar to HashMap except it is synchronized.Hashtable doesn’t allow any null key or value.
3. LinkedHashMap: Hash table and linked list implementation of the Map interface, with predictable iteration order. This implementation differs from HashMap in that it maintains a doubly-linked list running through all of its entries. This linked list defines the iteration ordering. maintains the insertion order.
4. Treemap: A Red-Black tree based NavigableMap implementation. The map is sorted according to the natural ordering of its keys, or by a Comparator.`ceilingKey(K key)`,`descendingKeySet()`,`firstKey()`,`floorEntry(K key)`,`higherKey(K key)`

# Collections Class
*  It contains polymorphic algorithms that operate on collections,
* The methods of this class all throw a NullPointerException if the collections or class objects provided to them are null.

**Key Methods**
1. `binarySearch()`
2. `checkedCollection`
3. `disjoin(C1,C2)` :Returns true if the two specified collections have no elements in common.
4. `emptyIterator()`
5. `max()`,`min()`:Returns the maximum element of the given collection,
6. `reverse()`
7. `reverseOrder(Comparator<T> cmp)`
8. `rotate()`
9. `shuffle()`
10.`sort()`

# Arrays Class
* This class contains various static methods for manipulating arrays (such as sorting and searching).

**Key Methods**
1. `asList()`
2. `binarySearch()`
3. `copyOf()`
4. `fill()`: ssigns the specified data type value to each element 
5. `copyOfRange()`
6. `sort()`
7. `toString()`






# Differences between `Iterator` and `Iterable` in java
## Iterable 
* interface in `java.lang.Iterable` which repsent a collection
* Only have one method `iterator()`
* If the class is an implementation of Iterable class, it can use `iterator`
````
package java.lang; 

public interface Iterable<AnyType>
{ 
     Iterator<AnyType> iterator(); 
}
````
## Iterator
* An iterator is an interface and is implemented by all collection classes
* The iterator method returns an object that implements the Iterator 
* An object of an iterator interface can be used to traverse through all elements of a collection. Elements can also be modified and removed from the collection while traversal.
```
package java.util; 
public interface Iterator<AnyType>
{ 
    boolean hasNext(); 
    AnyType next(); 
    void remove(); 
}
```

# Describe the Collections Type Hierarchy. What Are the Main Interfaces, and What Are the Differences Between Them?
* The Collection interface inherits from `Iterable`
* The `List`, `Set` and `Queue` interfaces inherit from the Collection interface.
* `Map` interface is also a part of the collection framework, yet it does not extend `Collection`.

# Describe Various Implementations of the Map Interface and Their Use Case Differences.

* Default implementaions of the Map interface is `HashMap`
  * It is a typical hash map data structure that allows accessing elements in constant time, or O(1), 
  * but does not preserve order and is not thread-safe.
  * It may have one null key and multiple null values.
* `LinkedHashMap`: extends the `HashMap`
     * It is implemented by doubly-linked buckets.
     * Keys are ordered by their insertion order,This means that keys must implement the Comparable interface
     * offers 0(1) lookup and insertion
     * * It may have one null key and multiple null values.
* `TreeMap`: Extends the `NavigableMap`, 
   * stores its elements in a red-black tree structure, So All operations in logarithmic time(O(log(n))).
   * Keys are ordered, so if you need to iterate through the keys in sorted order, 
   * It cannot have null key but can have multiple null values.
* `ConcurrentHashMap`:  a thread-safe implementation of a hash map.
* `Hashtable`: 
     * It is a thread-safe hash map, but unlike `ConcurrentHashMap`, 
     * all its methods are simply synchronized, which means that all operations on this map block, even retrieval of independent values.
     * It may have not have any null key or value.
     
# How is Hashmap Implemented in Java? How Does Its Implementation Use Hashcode and Equals Methods of Objects? What Is the Time Complexity of Putting and Getting an Element from Such Structure?

# Explain the Difference Between Linkedlist and Arraylist.
* data structure: ArrayList internally uses a dynamic array to store the elements,LinkedList internally uses a doubly linked list to store the elements.
* search: `get(int index)` in ArrayList gives the performance of O(1) while LinkedList performance is O(n).
* add: LinkedList adding or insertion is O(1) operation . While in ArrayList, if array is full i.e worst case,  there is extra cost of  resizing array and copying elements to the new array , which makes runtime of add operation in ArrayList O(n) , otherwise it is O(1) .
* remove: LinkedList remove operation gives O(1) performance because it just need to reset the pointers of previous and next nodes. No copy or movement is required.
* memory:LinkedList has more memory overhead than ArrayList because in ArrayList each index only holds actual object (data) but in case of LinkedList each node holds both data and address of next and previous node.
* As seen in performance comparison, ArrayList is better for storing and accessing data. LinkedList is better for manipulating data.

# What Is the Difference Between Hashset and Treeset?
* Both `HashSet` and `TreeSet` classes implement the Set interface and represent sets of distinct elements. Additionally,
* `TreeSet` implements the `NavigableSet` interface. This interface defines methods that take advantage of the ordering of elements.
* `HashSet` does not keep elements in any particular order. Iteration over the elements in a `HashSet` produces them in a shuffled order. `TreeSet`, on the other hand, produces elements in order according to some predefined Comparator.

# How Is Hashmap Implemented in Java?, What is collsion and how to fixt it?
* HashMap in Java works on hashing principle. It is a data structure which allows us to store object and retrieve it in constant time O(1)
* When we call put method, hashcode() method of the key object is called so that hash function of the map can find a bucket location to store value object, which is actually an index of the internal array, known as the table
* HashMap internally stores mapping in the form of Map.Entry object which contains both key and value object. When you want to retrieve the object, you call the get() method and again pass the key object. This time again key object generate same hash code and we end up at same bucket location

# How Does Its Implementation Use Hashcode and Equals Methods of Objects? 
* A hashCode is not unique, however, and even for different hashCodes, we may receive the same array position. This is called a collision. There is more than one way of resolving collisions in the hash map data structures. In Java's HashMap, each bucket actually refers not to a single object, but to a red-black tree of all objects that landed in this bucket.
* So when the HashMap has determined the bucket for a key, it has to traverse this tree to put the key-value pair in its place. If a pair with such key already exists in the bucket, it is replaced with a new one.
* To retrieve the object by its key, the HashMap again has to calculate the hashCode for the key, find the corresponding bucket, traverse the tree, call equals on keys in the tree and find the matching one.

#  What Is the Purpose of the Initial Capacity and Load Factor Parameters of a Hashmap? What Are Their Default Values?
* There are two factors which affect the performance of HashMap.
* initial capacity is simply the capacity at the time the hash table is created. 
* The load factor of a HashMap is the ratio of the element count divided by the bucket count,high load factor means a lot of collisions。
* The threshold of an HashMap is the product of current capacity and load factor，Whenever HashMap reaches its threshold， the hash table is rehashed, This process of rehashing is both space and time consuming. So, you must choose the initial capacity, by keeping the number of expected elements (key-value pairs) in mind, so that rehashing process doesn’t occur too frequently.

* The initialCapacity is 16 by default, and the loadFactor is 0.75 by default, 

# Describe Special Collections for Enums. What Are the Benefits of Their Implementation Compared to Regular Collections?
* `EnumSet` and `EnumMap` are special implementations of Set and Map interfaces correspondingly. You should always use these implementations when you're dealing with enums because they are very efficient.

# What Is the Difference Between Fail-Fast and Fail-Safe Iterators?
* Iterators for different collections are either fail-fast or fail-safe,depending on how they react to concurrent modifications
* The concurrent modification is not only a modification of collection from another thread but also modification from the same thread but using another iterator or modifying the collection directly.
* Fail-fast iterators (those returned by HashMap, ArrayList, and other non-thread-safe collections) iterate over the collection's internal data structure, and they throw ConcurrentModificationException as soon as they detect a concurrent modification.
* Fail-safe iterators (returned by thread-safe collections such as ConcurrentHashMap, CopyOnWriteArrayList) create a copy of the structure they iterate upon. They guarantee safety from concurrent modifications. Their drawbacks include excessive memory consumption and iteration over possibly out-of-date data in case the collection was modified.

# How Can You Use Comparable and Comparator Interfaces to Sort Collections?
* Comparable and Comparator both are interfaces and can be used to sort collection elements.
* Comparable is an interface defining a strategy of comparing an object with other objects of the same type.The Comparator interface defines a compare(arg1, arg2) method which can comparing different type object
* We can define multiple different comparison strategies which isn't possible when using Comparable
* Comparable provides compareTo() method to sort elements.Comparator provides compare() method to sort elements.
* Comparable is present in java.lang package. A Comparator is present in the java.util package.
* We can sort the list elements of Comparable type by Collections.sort(List) method.We can sort the list elements of Comparator type by Collections.sort(List, Comparator) method.

