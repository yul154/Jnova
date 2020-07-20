# Map
> Collections of pairs of values
* Map is the only collections that don't extend or implement the `Collection` interface
--------------
# Hashmap
* HashMap has no predictable order but LinkedHashMap,TreeMap have predictable order
## Operations
* type of key and value are generic type parameters
* `put`:can modify
* `putall`: put all pairs from one table to another
* `putIfAbsent`: put a pair if the key not existing
* `containskey,`containsValue`:boolean
* `clear`: clear up
* `size`: `int`
## Views Over Maps
> `keySet()`,`values()`,`entrySet()`
* `hash_map.keySet()`: The method returns a set having the keys of the hash map.
* `hash_map.values()`: create a collection out of the values of the map
* `hash_map.entrySet()`:a Set view of the pairs contained in this map.
## `Null` in map
* HashMap supports both null keys and values
* LinkedHashMap allow null key and null value but 
* TreeMap doesn't allow null key and null value. 

## Extend Maps interface
### `SortedMap`
* Traversal in key Ascending Order
* Subviews based upon key
* TreeMap is an implementation of SortedMap interface
#### Method
* `SortedMap<K,V> subMap(K fromKey,K toKey)`: Returns a view of the portion of this map 
* `SortedMap<K,V> headMap(K toKey)`:Returns a view of the portion of this map whose keys are strictly less than toKey
* `SortedMap<K,V> tailMap(K fromKey)`:Returns a view of the portion of this map whose keys are greater than or equal to fromKey.
*  `K firstKey()`:Returns the first (lowest) key currently in this map.
* `K lastKey()`:Returns the last (highest) key currently in this map.
### NavigableMap
* `public interface NavigableMap<K,V> extends SortedMap<K,V>`
### Methods
* `lowerEntry(K key)`: returns a key-value mapping associated with the greatest key strictly less than the given key.
* `floorEntry(K key)`: returns a key-value mapping entry which is associated with the greatest key less than or equal to the given key.
* `ceilingEntry(K key)`: returns an entry associated with the lest key greater than or equal to the given key.
* `higherEntry(K key)`: returns an entry associated with the least key strictly greater than the given key.
Note that all these methods return null if there is no such key.
* `firstEntry()`: returns a key-value mapping associated with the least key in the map, or null if the map is empty.
* `lastEntry()`: returns a key-value mapping associated with the greatest key in the map, or null if the map is empty.
* `descendingMap()`: returns a reverse order view of the mappings contained in the map.
* `pollFirstEntry()`: removes and returns a key-value mapping associated with the least key in the map, or null if the map is empty.
* `pollLastEntry()`: removes and returns a key-value mapping associated with the greatest key in the map, or null if the map is empty.
* `lowerKey(K key)`: returns the greatest key strictly less than the given key.
* `floorKey(K key)`: returns the greatest key less than or equal to the given key.
* `headMap(K toKey, boolean inclusive)`
* `subMap(K fromKey, boolean fromInclusive, K toKey, boolean toInclusive)`
* `tailMap(K fromKey, boolean inclusive)`

## Java 8 Enhancement
* Altering and Removing
  * replace(key,value):if there is no mapping for that key, then `replace `will be a no-op (will do nothing) whereas `put` will still update the map.
  * replaceAll(BiFunction<K,V,V>)
  * remove(key,value)
* Updating
  * `getOrDefault`:Returns the value to which the specified key is mapped, or defaultValue if this map contains no mapping for the key.
  * `putIfAbsent`: put a pair if the key not existing
  * `compute`:Attempts to compute a mapping for the specified key and its current mapped value (or null if there is no current mapping).
  * `computeIfAbsent`:If the specified key is not already associated with a value (or is mapped to null), attempts to compute its value using the given mapping function and enters it into this map unless null.
  * `computeIfPresent`:If the value for the specified key is present and non-null, attempts to compute a new mapping given the key and its current mapped value.
  * `merge`:If the specified key is not already associated with a value or is associated with null, associates it with the given non-null value.
* `forEach`: can iterate a map using Map.forEach(action) method and using lambda expression. 
```
map.forEach((k,v) -> System.out.println("Key = "+ k + ", Value = " + v)); 
```

## Maps Implementations

![map-interface-1](https://user-images.githubusercontent.com/27160394/65391938-0c436e00-dd3d-11e9-8772-4f07d5e24421.png)

### Hashmap
* General purpose implementation
* Uses the `.hashcode()` method(hash%bucket_count) to maintains an array of buckets
* Buckets are linked lists to accommodate collsions
* In java 8, the Buckets can be trees
* Increasing the number of buckets is depending on a load factor threshold
### LinkedHashMap
```
LinkedHashMap<Integer, String> map = new LinkedHashMap<>(16, .75f, true);
```
* The first parameter is the initial capacity, followed by the load factor and the last param is the ordering mode. So, by passing in true, we turned out access-order, whereas the default was insertion-order.
* It is an extension of `HashMap`
* with an additional feature of maintaining an order of elements inserted into it
* Keys are sorted on the basis of access orde
* Helpful for Implementing Caches by using `removeEldestEntry`
#### WeakHashMap
* Hash table based implementation of the `Map` interface
* with weak keys. An entry in a WeakHashMap will automatically be removed when its key is no longer in ordinary use
### TreeMap
* Unlike `HashMap` and `LinkedHashMap`, `TreeMap` does not use hashing for storing key(array data structure)
* Implemented using red-black tree
* all its elements store in the TreeMap are sorted by key.
* Only one implmentation in java 8 library that implement `NavigableMap` and `SortedMap`
* Uses comparable/comparator to define the order
### EnumMap
* EnumMap is specialized implementation of Map interface for enumeration types.
* Assumed the keys are enums
* Implementation based upon bitsets

## Summary
### Algorithmic Performance
<img width="350" alt="Screen Shot 2019-09-22 at 2 40 56 PM" src="https://user-images.githubusercontent.com/27160394/65392830-03f03080-dd47-11e9-96cc-0ca52ad653c9.png">
