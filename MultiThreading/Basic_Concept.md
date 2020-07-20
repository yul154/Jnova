# talk about multithread
* two or more threads run concurrently;
* I use multithreading mostly in web applications - Typically in the DAO layer
* the database (JDBC or Hibernate/ORM) code loads all records that were requested by the user, page size irrelevant, If the user wants 50,000 rows then so be it - but that takes a while to retrieve and on which to work.
* If implementing Runnable as I usually do, I’d probably also extend Observable and whenever a new record was added to the variable (list/ map /set / whatever), it would notify observers and when X rows were accumulated
#  What is thread-safe + how to implement it + example in your project
*  it may be used from multiple threads at the same time without causing problems
* There are two ways to create a thread in Java:
1) By extending Thread class.
2) By implementing Runnable interface.

# List statuses of a thread
* New
* Runnable
* Blocked
* Waiting
* Timed Waiting
* Terminated

# talk about synchronized
* one of the way to achieve 'thread safe'.
* When two or more threads need access to a shared resource there should be some way that the resource will be used only by one resource at a time. The process to achieve this is called synchronization.


# List thread-safe data structures
* Hashtable
* StringBuffer
## StringBuilder and StringBuffer
* all of StringBuffer public methods are synchronized，most of the scenarios, we don’t use String in a multithreaded environment
* StringBuilder is faster than StringBuffer because of no synchronization.

ArrayList and LinkedList + implementation
        Comparator and Comparable + how use them
        Interface and Abstract Class
        HashMap and HashSet + implementation
        How Concurrent HashMap be implemented
        What is Collection in Java + architecture of it 


# Types of Exception in Java + when they happen
* ArithmeticException
It is thrown when an exceptional condition has occurred in an arithmetic operation.
* ArrayIndexOutOfBoundsException
It is thrown to indicate that an array has been accessed with an illegal index. The index is either negative or greater than or equal to the size of the array.
* ClassNotFoundException
This Exception is raised when we try to access a class whose definition is not found
* FileNotFoundException
This Exception is raised when a file is not accessible or does not open.
* IOException
It is thrown when an input-output operation failed or interrupted
* InterruptedException
It is thrown when a thread is waiting , sleeping , or doing some processing , and it is interrupted.
* NoSuchFieldException
It is thrown when a class does not contain the field (or variable) specified
* NoSuchMethodException
It is thrown when accessing a method which is not found.
* NullPointerException
* RuntimeException
This represents any exception which occurs during runtime
