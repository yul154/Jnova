# Collection of Collections

<img width="874" alt="Screen Shot 2019-08-11 at 2 51 37 PM" src="https://user-images.githubusercontent.com/27160394/62838248-93ce9500-bc47-11e9-9581-04ae39d24b65.png">

## Collection Behaviors
| Method|Description|
|-------|-----------|
 `size()`|get the number of elements in the Collection
`isEmpty()` | `true` if `size()==0`, false otherwise
`contains(element)` | True if the element is in this collection, false otherwise
`containsAll(collection)` | returns true if the target Collection contains all of the elements in the specified Collection.
`add(element)`| Add the element at the beginning of this collection
`addAll(collection)`| adds all of the elements in the specified Collection to the target Collection.
`remove(element)`| remove the element from this collection
`removeAll(collection)` | removes from the target Collection all of its elements that are also contained in the specified Collection.
`retainAll(collection)` | removes from the target Collection all its elements that are not also contained in the specified Collection. That is, it retains only those elements in the target Collection that are also contained in the specified Collection.
`clear()` | removes all elements from the Collection.

# Collections Opertaions
## 1. Algorithms
* `Collections.rotate`: Rotates the elements in the specified list by the specified distance.
* `Collections.shuffle`: Randomly permute the specified list using the specified source of randomness.
* `Collections.sort`:used to sort the elements present in the specified list of Collection in ascending order.
  * `Collections.sort(al, Collections.reverseOrder())`
  * `Collections.sort(List<T> list, Comparator<? super T> c)`: Sorts the specified list according to the order induced by the specified comparator.
```
List<String>  mylist = new ArrayList<String>();  
mylist.add("Java");  
mylist.add("Python");  
mylist.add("Cobol");  
mylist.add("Perl");  
mylist.add("Android");  
System.out.println("Original List : " + mylist);    
//Rotate the element by distance 3  
Collections.rotate(mylist, 3);    
System.out.println("Rotated List: " + mylist);  
Collections.shuffle(mylist);
Collections.sort(mylist, Comparator.comparing(String::length));
mylist.sort(Comparator.comparing(String::length));
```
## 2. Factories
> Create a new collections with some specific purpose
* `Singletons`: Immutable single valu of collections
```
Set<Integer> set= Collections.singleton(1)
List<Integer> list =Collections.singletonList("one")
Map<Integer, String>map= Collections.SingletonMap(1,"one");
```
* Empty Collections: Immutabl empty collection
```
Set<Integer> set= Collections.emptySet()
List<Integer> list =Collections.emptyList("");
Map<Integer, String>map= Collections.emptyMap();
```
* unmodifiable collection factory
  * used to return an unmodifiable view of the specified collections collection type.
  * allows modules to provide users with “read-only” access to internal lists.
## 3. Utilities
* `Collections.addAll(a,b,c,)`
* `Collection.min(strings,String.size())`
