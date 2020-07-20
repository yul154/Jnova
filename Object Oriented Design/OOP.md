# Object
> A way of storing mulitple pieces of data an abilities under a single type
## Data types
### Primitive Value
* int,double,char,boolean
* stores directly in memory
* value semantics
### Object
* Array,String,Scanner,ArrayList
* Computer separate out a box and memory for the reference for 
* Reference semantics
### `Null`:no object
* `Null Pointer Exception`:perform a method, on a object that is null(not empty object).
### Reference Semantics
* Primitives are all directly in memory,Difference variable store same value, two separate places in memory, copying the value.
* Object variables store a reference to the object,difference variable store same object. two addresses for the same thing.
## Arrays
>  A data structure that groups together a collection of variables of the sanme type
* Creating an Array
```
type[] name= new type[length];
type [][] name= new type[row][col]# 2-D
int [] nums=new int[10];
char[] chars=new char[8];
```
* Access value in array
```
array_name[index]
```
* Modify the element
```
name[index]=value;
```
* Add multiply values
```
datatype[] name={value1,value2,value3}
```
* New an array, The initialization value of each index is "zero" value
* Once you create the array the capacity cannot be changed
### Array class
* Java also provides an Arrays class that has a series of methods you can use on your own array instances

Method|example|description
------|-------|-------------
toString(array)|	Arrays.toString(a)|	returns a String representation of the array using square brackets and commas like so: [value, value, value]
equals(array1, array2)|	Arrays.equal(a, b) OR a.equals(b)|	returns true if the two arrays contain the same elements in the same order
fill(array, value)|	Arrays.fill(a, 10)|	fills every index of the array with a copy of the given value
copyOf(array, newLength)|	Arrays.copyOf(a, 10)|	creates a new array object with the given length and fills it with values in the same order as the original array. If there are left over indexes those are filled with the data type's zero value
sort(array)|	Arrays.sort(a)|	arranges the values in the array in sorted order from smallest to largest
binarySearch(array, value)|	Arrays.binarySearch(a, 100)|	returns the index of th given value in a sorted array. Will return a negative number if the value doesn't exist in the array.

#### Reference
>computer creates a special space in memory for the data and then stores the address of that location in the variable
* object variables store reference to the object
* original values are modified by variable copies
* create a new variable without affecting the original array?
```
b=Arrays.copyOf(a,a.length);
```
### Array Lists
> Dynamic sized arrray
* Limitations in Array
  1. static in size
  2. it is difficult to operate data(insert,remove,change order) on original array
#### Construction
```
ArrayList<String> names=new ArrayList<String>();
names.add("Leo")
String s=name.get(0)
names.indexof("e")
names.remove(0)
names.set(o."y")
names.size()
names.toString()
```
* ArrayLists require that you give them an object as a data type 
  * A wrapper class is the way that Java allows you to use primitives when an object is required
  * Instead of primitives data type, using Wrapper classes in data structures
PRIMITIVE|WRAPPER CLASS
`int`|Integer
`boolean`|Boolean
double|Doub
char|Character


## Build object
* Object is a combination of "state" and "behavior"
* Key words `private`:prevent your user from directly changing the values of your fields
* A class can have multiple constructors,The constructor is overloaded

### build a new object
```
public class Student{
   Strnig name;/**
   int grad;    *
   int ID;      * states,field
   double GPA;  * 
   int abs;     */
   public Student(n,g,I,grades,ab){# constructor
      this.name=n;
      this.grad=g;
      this.ID=I;
      this.GPA=grades;
      this.abs=abs;
    }
}
```
### run an object
```
public class StudentManages{
    public static void main(String[] args){
          Student ada=new Student("ADA", 2019,123,3.5,4)
    }
}         
```
### Encapsulation
> The act of hiding implementation details from users
* abstraction : separates how to use something from the details of how it’s implemented
#### `private`
* can apply fields and methos 
* java default `public`
* accessor methods(read-only mode)
* mutator methods(allows external code to modify the state of object)

```
public class MyObject{#getter method
    private String pri_field;
    public String getpri_field(){
        return pri_field
        }
    public void setpri_field(String new){#setter method
        pri_field=new;
        }
}
```
#### `static`
> belongs to class,not an object

* all object method are not static
* if one instance changes, that change is gonna be represented across all instances.
* Accessible by other clssses
```
ClassName.staticFieldName
```
### Inheritance
> A way to form new class based on existing classes, taking on their attributes/behavior
* A way to group related classes
* all object method are not statics w
* all object method are not statics
#### `super`
* a class can only ever inherit from one super class
* if you overwrote a method, but you need to use the superclass's implementation
* if the super class has a non default constrcutor defined, that would not inherited,use `super(name)` to call super class constructor
```
super.MethodName()
super.Variable
```
### Polymorphism
> reuse the same code within tw different bu related objects and for that code's behavior t automatically adjust appropriately
* Override: same function name and parameter in different but related class
```
public class Animal{
  public void eat(){ System.out.println("plants")}
 }
public class Fish extends Animal{
   public void eat(){ System.out.println("rubbish")}
}
```
### Abstract
* Abstract methods: a method definition without implementations

#### Abstract class
> A class that includes abstract methods
```
public Abstract class Animal
```
* is just regular super class that guarantee common behavior
* specific subclasses that need to extend general classes.



### Interfaces
> A list of method definitions that guarantess behavior of an object 
```
public interace name{
}
```

```
public class objectNane implements interfaceName{
```
#### Comparable interface
* how to compare this object 
#### Iterable
* return an iterator that allows object to be used by a fo each loop
*  relate specific behavior across otherwise unrelated classes
*  need to relate multiple classes to a single class and have already used "extend"
