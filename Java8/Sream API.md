# Stream
> Typed interface
* A stream is a sequence of objects that supports various methods which can be pipelined to produce the desired result
* Efficiently process large amounts of data
  * Pipelined, avoid intermediary computation
  * parallele, leverage the computing power of multicore CPUs.
* A stream is not a data structure instead it takes input from the Collections, Arrays or I/O channels.
* A stream does not hold any data.
* Sall the methods of Stream that return another Stream are lazy.

## Build a stream
* pattern 1
```
List<person> persons=;
Stream<person> stream= persons.steam();
stream.forEach(p -> System.out.println(p));
```
* pattern 2
```
String[] array = {"a", "b", "c", "d", "e"};
Stream<String> stream2 = Stream.of(array);
```
## Operations of Stream
* Intermediary operation: an operation on a Stream that returns a Stream
  * lazy operations: they do not process the stream at the call site, an intermediate operation can only process data when there is a terminal operation.
  * Intermediate operations return a new stream. They are always lazy
  * intermediate operations Includes filter, map and flatMap.
* Terminal 
aaal operations: terminate the pipeline and initiate stream processing. 
    * The stream is passed through all intermediate operations during terminal operation call
    * Terminal operations include forEach, reduce, Collect and sum.
    
### Itermediate Opertaions

#### 1. `map`
* map: The map method is used to map the items in the collection to other objects according to the Function passed as argument.
* map function is modeled by the Function interface.
```
List number = Arrays.asList(2,3,4,5);
List square = number.stream().map(x->x*x).collect(Collectors.toList());
```
#### 2. `filter`
* filter: The filter method is used to select elements as per the Predicate passed as argument.
* follow predicate interface
* filter method returns a Stream（new instance）
```
List names = Arrays.asList("Reflection","Collection","Stream");
List result = names.stream().filter(s->s.startsWith("S")).collect(Collectors.toList());
```
### 3 `sorted`: 
* The sorted method is used to sort the stream.
```
List names = Arrays.asList("Reflection","Collection","Stream");
List result = names.stream().sorted().collect(Collectors.toList());
```
#### 4 `flatMap()`
* Takes an element of type T, and returns an elements of type Stream <R>

#### 5 : `peek`
* return a Stream
* as same as `forEach`
#### 6. `distinct`
* find unique elements in a stream according to their .equals behavior
#### 7. `limit`
* limit the number or truncate elements to be processed in the stream

### Terminal operations 
#### 1: `foreach`
* it takes `Consumer` as a parameter
```
List<Person> persons=....,
Stream<Person> stream= persons.streams();

Consumer<String>c1=list::add;
Consumer<String> c2= System.out::println;
person.stream.forEach(c1.andThen(c2));
``` 
#### 2. `collect`
*  collects together the desired results into a result container such as a Collection
### 3`reduce`
* Terminal operations
* If the Stream is empty, The reduction is the identity element
* If the Stream only has one element, then the reduction is that element
##### available reductions
* max(),min()
* counts
##### Boolean reductions
* allMatch()
* noneMatch()
* anyMatch()

####  Collectors
* Another type of reduction
* Mutable reduction: Instead of aggregating elements, this reduction put them in a container

## Optionals

* The purpose is to provide a type-level solution for representing optional values instead of null references.

### Optional patterns

* `isPresent()`: return true if there is something in the optional
* `get()` returns the value held by this optional
* `orElse()`: encapsulates `isPresnt()` and `get()`
