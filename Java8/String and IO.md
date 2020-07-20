# Update in Java 8
* The Stringclass, StrinJoiner
* Easy ways to  create streams on text files
* Simple ways to visit directories
* New methods on Iterable,Collections and List
* New patterns to create Comparator
* Userful methods on the number wrapper classes
* New methods on Map
* Annotations
------------
# String Class
##  StringJoiner
* The String class is an immutable class whereas StringBuffer and StringBuilder classes are mutable.
* Concatenation with "+" sign is not a efficient way to combine string
* `StringBuffer` is synchronized  and `Stringbuilder` is not synchronized, So It means two threads can call the methods of StringBuilder simultaneously.
```
String Buffer sb1= new StringBuffer();
sb1.append("hello");
sb1.append(" ").append("world");
String s=sb1.toString();
```

```
StringBuilder strb = new StringBuilder();

// add few elements to StringJoiner for concatenation 
// by using add(String str) method

// First we will append prefix if we want
strb.append("{");

// Second we will append required delimeter after every element
strb.append("Java");
strb.append(";").append("C");
strb.append(";").append("C++");

// Finally we will append suffix if we want
strb.append("}");
```
* `StringJoiner` is more simple way to join String in java 8
* `StringJoiner` join the strings based on a given delimiter, prefix, and suffix in the constructor simultaneously.
* `StringJoiner` is a final class and we can't override this class.
```
StringJoiner object_name = new StringJoiner(delimeter,prefix,suffix);

StringJoiner sj= new StringJoiner(",","{","}");
sj.add("one").add("two").add("three");
```

# Java I/O enhancements
* A `lines()` method has been added on the `BufferedReader` class, which can be used to read a file line by line in Java. 
* it reads all lines from a file as Stream of String
```
private static void readLinesUsingFileReader() throws IOException//java 7
{
    File file = new File("c:/temp/data.txt");
 
    FileReader fr = new FileReader(file);
    BufferedReader br = new BufferedReader(fr);
 
    String line;
    while((line = br.readLine()) != null)
    {
        if(line.contains("password")){
            System.out.println(line);
        }
    }
    br.close();
    fr.close();
}
```
```
private static void readStreamOfLinesUsingFilesWithTryBlock() throws IOException
{
    Path path = Paths.get("c:/temp", "data.txt");
 
    //The stream hence file will also be closed here
    try(Stream<String> lines = Files.lines(path))
    {
        Optional<String> hasPassword = lines.filter(s -> s.contains("password")).findFirst();
 
        if(hasPassword.isPresent()){
            System.out.println(hasPassword.get());
        }
    }
}
````
* Read directory entries by using `.list()`
* Visit the whole subtree by using `Files.walk(path)` method

