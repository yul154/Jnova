# Array
* The Arrays class in java.util package is a part of the Java Collection Framework
* To save large numbers of data elements, either primitives or objects.
```
public class Arrays extends Object
```
## Declaration
* All elements in an array must be the same type, either primitive or object.
* Precede variable with element type and square brackets
```
int[] scores
```
* Memory to store array elements must be allocated with new. Eg,
```
int[] scores;         
scores = new int[12];  
```
* It's very common to combine the declaration with the allocation.
```
int[] scores = new int[12];  // Declare and allocate memory in one statement.
```
* When memory is allocated for an array, it is a fixed size block. It does not expand.

* A curly brace surrounded list of values can be specified on the declaration. This allocates exactly enough space to hold the array. 
```
String[] names = {"Mickey", "Minnie", "Donald"};
```

## Array initial values
* For references (anything that holds an object) that is null.
* For int/short/byte/long that is a 0.
* For float/double that is a 0.0
* For booleans that is a false.
* For char that is the null character '\u0000' (whose decimal equivalent is 0).

## Accessing elements
```
scores[5] = 86;   // Assigns the value 86 as the 5th value.
scores[ss]++;     // Increments the score of element whose number is in ss
```
## Iterating over an array

* The size of an array can be found by referencing the length field：array.length
* For loops are the most common way to iterate over array elements
```
int[] scores = new int[12];
int total = 0;
for (int i = 0; i < scores.length; i++) {// for (int scr : scores)
    total += scores[i];
}
```

## Arrays Main Methods
* `Arrays.asList()`: returns a fixed-size list backed by the specified array
