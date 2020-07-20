# Queues
> First in ,First out
* Carefully use `Linkedlist` to implmentate Queue, Beacuse it allows `null` value, Queue is returning `null` to indicate empty
## Queue Interface Opertaions
<img width="474" alt="Screen Shot 2019-09-22 at 9 15 42 PM" src="https://user-images.githubusercontent.com/27160394/65397454-3b2e0400-dd7e-11e9-8e5d-126e6b2cacb5.png">

## 1. Priority Queue
* `Queue <String> pQueue = new PriorityQueue<String>();`
```
public static void main(String[] args) {
        Comparator<String> stringLengthComparator = new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return s1.length() - s2.length();
            }
        };
        PriorityQueue<String> namePriorityQueue = new PriorityQueue<>(stringLengthComparator);
```
#### 3.3 Stacks
* Implementate stacks with `deque`.
* `Deque` is an Interface,`ArrayDeque` is an iomplementation of `Deque`.

Type of Operation|	First Element (Beginning of the Deque instance)|	Last Element (End of the Deque instance)
-----------------|------------------------------------------------|-------------
Insert|	addFirst(e), offerFirst(e)|	addLast(e), offerLast(e)
Remove|	removeFirst(),pollFirst()|	removeLast(),pollLast()
Examine|	getFirst(), peekFirst()|	getLast(), peekLast()
```
Deque<Integer> stack = new ArrayDeque<Integer>();
stack.push(2);
stack.push(5);
stack.push(6);

System.out.println("Current element at the top of the stack: " + stack.peek());

System.out.println("Element popped from the stack: " + stack.pop());

System.out.print("Current elements in the stack: ");
while(!stack.isEmpty()) {
    System.out.print(stack.pop() + " ");
  }
}
```
## Implementations
![queueclassdiagram](https://user-images.githubusercontent.com/27160394/65438956-694e2b00-ddf4-11e9-99c3-038cec8c0728.png)
