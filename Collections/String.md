
# String
> a sequence of characters
* String are immutable which means a constant and cannot be changed once created.

## Creating
1. String literal
```
String s = “GeeksforGeeks”;
```
2. Using new keyword
```
String s = new String (“GeeksforGeeks”);
```

## Main String Methods 
* `int length()`: Returns the number of characters in the String.
* `Char charAt(int i)`: Returns the character at ith index.
* `String substring (int i)`:Return the substring from the ith  index character to end.
* `String concat( String str)`: Concatenates specified string to the end of this string.
* `int indexOf (String s)`:  Returns the index within the string of the first occurrence of the specified string.
* `boolean equals( Object otherObj)`:Compares this string to the specified object.
* ` int compareTo( String anotherString)`: Compares two string lexicographically.
