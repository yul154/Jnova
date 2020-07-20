# List
* Order
* Elements in list have index(`int`)

Special Method| Description
--------------|---------------
`void add(int index, E e)`| add element on given index, and shuffle everything after it
`E.get(int index)`| move to index and extract the element at that index
`E.remove(int index)`|delete element on a given index and return that element's value back
`E.set(int index, E element)`| replace existing element at to give an index with a new element
`boolean addAll(int index, Collection <? extends E> c)`| insert given collection into current list with given position
`int indexof(Object o)`| find where this object is in the list, return -1 if not in
`int lastIndexOf(Object o)`| return last element if same element in same list
`list<E> subList(int fromIndex int toIndex)`| a view which means you will modify origin list if you modify view

## Implementations
### 1 ArrayList
* Default Implementations.
* Dynamic sized list
* Good Choice to add, iterate and get.
* The ArrayList double size of tha backing array when it runs ou of storage.
* LinkedList and ArrayList require O(n) time to find if an element is present or not. Both However we can do Binary Search on ArrayList if it is sorted and therefore can search in O(Log n) time.
### 1.2 LinkedList
* Doubled-link list: each element have two pointers
* It is a good choice if frequently add or remove element
* It is slower to iterator over

<img width="330" alt="Screen Shot 2019-09-22 at 8 41 29 PM" src="https://user-images.githubusercontent.com/27160394/65396902-619d7080-dd79-11e9-8fed-afc707922f8b.png">
