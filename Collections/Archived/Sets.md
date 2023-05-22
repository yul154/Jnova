# Sets
> Collections of distinct elements 

## Set interface
* `add( )`: Adds an object to the collection.
* `clear( )`: Removes all objects from the collection.
* `contains( )`: Returns true if a specified object is an element within the collection.
* `isEmpty( )`: Returns true if the collection has no elements.
* `iterator( )`: Returns an Iterator object for the collection, which may be used to retrieve an object.
* `remove( )`:Removes a specified object from the collection.
* `size( )`: Returns the number of elements in the collection.




## Set Implementations
<img width="300" alt="Screen Shot 2019-09-22 at 4 35 47 PM" src="https://user-images.githubusercontent.com/27160394/65394075-11151b80-dd57-11e9-8316-ed144e776046.png">

### `java.util.HashSet`
*  Based  upon HashMap: Calls `hashCode()` on element an looks up locations
*  hashcode can be defined by `.hash()`
* Equals contract
  1) If two objects are equal, then they must have the same hash code.
  2) If two objects have the same hash code, they may or may not be equal.
* Object duplication:
  * Stream
  * override `euqals()` and `hashCode()`: if override `euqals`, you should override `hashCode()`
#### 1. Create
```
Set<String> hash_Set = new HashSet<String>(); 
```
*  create a Set from an existing collection
```
List<Integer> nums= Arrays.asList(3, 9, 1, 4, 7, 2, 5, 3, 8, 9, 1, 3, 8, 6);
Set<Integer> uninums= newHashSet<>(nums)
```
#### 2. Add
* `add(E e)`:Adds the specified element to this set if it is not already present.
* `addAll(Collectio C)`:append all of the elements from the mentioned collection to the existing set
#### 3.Search
*  boolean contains(Object o):
* `Hashset.clone()`: returns the exact same copy of the hashSet object

### `java.util.LinkedHashSet`
* extends the `HashSet` class
* it maintains the insertion-order of the elements
  * that is elements in the LinkedHashSet are stored in the sequence in which they are inserted
  
### `java.util.TreeSet`
* Uses a Binary Tree wit a required sort order
* An Implementation of `NavigableSet`
* The elements are ordered 
  1. using their natural ordering, 
  2. by a Comparator provided at set creation time, depending on which constructor is used.
* This implementation provides guaranteed log(n) time cost for the basic operations (add, remove and contains).
* the ordering maintained by a set must be consistent with equals if it is to correctly implement the Set interface
* this implementation is not synchronized.

###  `java.util.EnumSet`
* Uses a bitset  based upon the ordinal of the enum
* Use when storing sets of enums

## Extended interface
> A collection with distinct elements that also have order.

### `SortedSet`
```
public interface SortedSet extends Set           
{          
    // Range views          
    SortedSet subSet(E fromElement, E toElement);    
    SortedSet headSet(E toElement);          
    SortedSet tailSet(E fromElement);          

    // Endpoints          
    E first();          
    E last();          

    // Comparator access    
    Comparator comparator();    
}       
```
### `NavigableSet`
```
public interface SortedSet extends Set           
{                  
    // Endpoints          
    E lower(e);          
    E higher(e);
    E floor(e);
    E ceiling(e);
    E pollFirst();
    E pollLast();
}       
```









# Questions
* `hashcode` VS `hashset`
* how to get key by value using hashmap
* diff between hashmap and hashtable
* comparator and comparable

