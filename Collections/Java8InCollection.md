# New Methods on the Collection API
* `stream()` and `parallelStream()`
* `spliterator()` as a iterator to a stream which can split a collection or a stream
* `removeif()`:used to remove all of the elements of this ArrayList that satisfies a given predicate filter which is passed as a parameter to the method.
```
boolean b = list.removeIf(s ->s.length()>4);
```
* `replaceAll()`:returns a string replacing all the sequence of characters matching regex and replacement string.
```
list.replaceAll(String::toUpperCase);
```
* `sort()`: `list.sort(Comparator.naturalOrder())`;
## Comparator
* supports declarations via lambda expressions as it is a Functional Interface
* Comparator has a new method comparing() which uses an instance of java.util.function.Function functional interface, specified using lambda expression or its equivalent method reference
*  Multiple sort criteria can now be clubbed using `comparing()` with a `thenComparing()` method.

```
Comparator<Person> compareLastName= Comparator.comparing(Person::getLastName);
                                              .thenComparing(Person::getFirstNaMe);
```
* Reverse
```
Comparator <Person> tmp=;
Comparator <Person> reversed=tmo.reversed();
```

* Null values
```
Comparator<String> c= Comparator.nullsFirst(Comparator.naturalOrder());
Comparator<String> c= Comparator.nullsLast(Comparator.naturalOrder());# conside null values greater than non-null values
```

## Numbers
* They all got a wrapper type
* New methods: sum,max,min
```
BinaryOperator<Long> sum=(l1,l2)->l1+l2;
                        = Long::sum;
```
* Hash code computation
```
// 7
long l =3123131231313L;
int hash= new Long(l).hashCode();

// 8
long l =132133131313l;
int hash = long.hashCode(l);
```

## Maps
* `forEach`
* `getOrDefault`: solve null return confusion,
```
Person defaultPerson= Person.DEFAULT_PERSON;
Person p= map.getOrDefault(key,defaultPerson);
```
* `putIfAbsent`: don't change the original value
* `replace(k)`: if the key isn't existing, return null
  * `replace(k,v1,v2)`: match both key and value
  * `replaceAll((key,V1)- > V1*V1)`:It replaces values in place and the method returns nothing.
* `remove(key,value)`
* `compute()`,`computeIfPresent()` and `computeIfAbsent()`:update a value in HashMap
```
map.compute("Name", (key, val) -> val.concat(" Singh")); 
```
* `bimaps`: value of map is map
```
Map<String,Map<Integer,Person>> bimap;
Person p=;
bimap.computeIfAbsent(key1,key->new HashMap<>()).put(key2,p);
```
* `Merge()`: it either puts new value under the given key (if absent) or updates existing key with a given value (UPSERT).
```
default V merge(K key, V value, BiFunction<? super V,? super V,? extends V> remappingFunction)；
map.merge(1, 10, (x, y) -> x + y);；
map.merge(2, 10, (x, y) -> x < y ? x : y);
```

## Annotations
* Annotations become repeatable;
* Allows annotations to be put on types;
