# Sameness (Lecture 11 & 12)
Sameness is defined as:
- **Reflexivity**: every object should be the same as itself.
- **Symmetry**: if object x is the same as object y, then y is the same as x.
- **Transitivity**: if two objects are both the same as a third object, then they are the same as each other.
- **Totality**: we can compare any two objects of the same type, and obtain a correct answer.

```java
abstract class AFoo implements IFoo {
  public boolean sameX(X that) { return false; }
  public boolean sameY(Y that) { return false; }
  public boolean sameZ(Z that) { return false; }
}
class X extends AFoo {
  public boolean sameFoo(IFoo that) { return that.sameX(this); }
  public boolean sameX(X that) { ... compares two X values ... }
}
class Y extends AFoo {
  public boolean sameFoo(IFoo that) { return that.sameY(this); }
  public boolean sameY(Y that) { ... compares two Y values ... }
}
class Z extends AFoo {
  public boolean sameFoo(IFoo that) { return that.sameZ(this); }
  public boolean sameZ(Z that) { ... compares two Z values ... }
}
```

# Function Objects and Lambdas (Lecture 13)
`Function<A, R>` where `A` is the expected type of the argument and `R` is the expected type of the result.

`BiFunction<A1, A2, R>` where `A1` and `A2` are the arguments' types and `R` is the expected type of the result.

`Predicate<A>` where `A` is the expected type of the argument.

Lambdas, like in DrRacket, are created like this: `(n -> n + 1)` or `((n1 n2) -> n1 + n2)`. The first part, before the arrow, defines the input variables. The second part, after the arrow, is the actual operation using the variables. Lambdas should only be used for simple operations.

`Comparator<T>` is used to compare two objects of the same type `T`. A result of `<= -1` denotes that the first item is less than the second. Vice-versa for `>= 1`. A `0` result means that the two objects are the same.

# Generics (Lecture 15)
In Java, it is possible to create classes/interfaces/methods that can deal with various types of data. This is specified using `<T>`. For instance, instead of an `IBookPredicate`, we can create a `Predicate<Book>`. 

For classes/interfaces, here is an example:
```java
interface IList<T> {}

class MtList<T> implements IList<T> {}

class ConsList<T> implements IList<T> {
  T first;
  IList<T> rest;
  
  ConsList(T first, IList<T> rest) {
    this.first = first;
    this.rest = rest;
  }
}
```

**NOTE**: For primitive types, you must capitalize the name. So, `int` -> `Integer`, `double` -> `Double`.

# Visitors (Lecture 16)
Visitors are another "abstraction mechanism" which allow us to easily implement methods without modifying the underlying interfaces/classes that we create. 

In this example, `IFoo` is an interface that has classes `X`, `Y`, and `Z`.

```java
// To implement a function over Foo objects, returning a value of type R
interface IFooVisitor<R> {
  R visitX(X x);
  R visitY(Y y);
  R visitZ(Z z);
}

// In IFoo:
// To return the result of applying the given visitor to this Foo
<R> R accept(IFooVisitor<R> visitor);

// In X:
// To return the result of applying the given visitor to this X
public <R> R accept(IFooVisitor<R> visitor) { return visitor.visitX(this); }

// In Y:
// To return the result of applying the given visitor to this Y
public <R> R accept(IFooVisitor<R> visitor) { return visitor.visitY(this); }

// In Z:
// To return the result of applying the given visitor to this Z
public <R> R accept(IFooVisitor<R> visitor) { return visitor.visitZ(this); }
```

# Mutation (Lecture 17 - 20)
Instead of creating new objects each time we want to change a certain field, we can use mutation! And its easy!
```java
this.field = ... ;

// or you can do it in a method
// EFFECT: modifies this Author's book field to refer to the given Book
void updateBook(Book b) {
  this.book = b;
}
```
Notice how `updateBook()` returns `void` rather than `Book`. This allows us to forgo a return statement. Additionally, all of our test methods can now return `void` rather than `boolean`. It may help to use an `initData()` method to create your examples now so that mutation does not effect your tests.

**Extensional Equality**: two items are extensionally equal if their fields are the same: either they’re equal primitive values, or they’re objects that are themselves extensionally equal.

**Intensional Equality**: two items are intensionally equal if they are the exact same object.

**Be careful about [aliasing](https://course.ccs.neu.edu/cs2510/lecture19.html#%28part._.Another_example_of_aliasing%29)!!!**

# ArrayLists (Lecture 21 & 22)
`import java.util.ArrayList;`
ArrayLists are similar to and can replace our `IList<T>` interace. Here is an example:
```java
ArrayList<String> strings = new ArrayList<String>();
strings.add("First");
strings.add("Second");
strings.get(0); // -> "First"
strings.get(1); // -> "Second"
strings.add(1, "New Second");
strings.get(1); // -> "New Second"
strings.get(2); // -> "Second"
```

If you want to use some common utility methods, you can find some [here](https://course.ccs.neu.edu/cs2510/lecture22.html).

# Loops (Lecture 23 & 24)
## For-Each Loops
A for-each loop iterates through an iterable list (such as an `ArrayList<T>`). Example:
```java
ArrayList<Integer> nums = new ArrayList<Integer>();
for (Integer i : nums) {
  ... body ...
}
```

## Counted-For Loops
A counted-for loop can be used to iterate through a list, build a new list, and do many other things. There are three statements required in a for loop. The first one initializes the variable, second one checks when to end the loop, the third one updates the variable after running the body.

```java
for (int i = 0; i < 10; i += 1) {
    ... body ...
}
```

## While Loops
A while loop continues to run as long as its conditional phrase returns true. Here is a while loop that can replace a for-loop that iterates through an indexed list:

```java
int idxVar = start;
while (idxVar < end) {
  ...body...
  idxVar = idxVar + 1;
}
```

## Conclusion to Loops
There is usually multiple types of loops that are applicable to a problem. Any may be correct and it is often up to the developer to make a decision.

It is important to NOT REMOVE ITEMS FROM A LIST WHILE ITERATING THROUGH IT. You must either create a temporary variable to hold the items of the list or take into account the removal of an item. You will know you made this error when java throws a `ConcurrentModificationException`.

# Iterator and Iterable (Lecture 25)
An `Iterator<T>` can be used to replace for-each loops using a while loop. In a while loop, we need something to check if there are more items left and what the next item is. Java has a built-in interface already!
```java
// Represents the ability to produce a sequence of values
// of type T, one at a time
interface Iterator<T> {
  // Does this sequence have at least one more value?
  boolean hasNext();
  // Get the next value in this sequence
  // EFFECT: Advance the iterator to the subsequent value
  T next();
  // EFFECT: Remove the item just returned by next()
  // NOTE: This method may not be supported by every iterator; ignore it for now
  void remove();
}

// while-loop replacement of a for-each loop
Iterator<T> listIter = new ArrayListIterator<T>(tList);
while (listIter.hasNext()) {
  T t = listIter.next();
  ...body...
}
```

In order to make an `Iterator<T>` functional with our own classes/interfaces, we must make it iterable. So, our classes/interfaces must extend the `Iterable<T>` interface. This extension forces us to implement the `iterator()` method:
```java
class ArrayList<T> implements Iterable<T> {
  ... lots of other details ...
  //
  public Iterator<T> iterator() {
    return new ArrayListIterator<T>(this);
  }
}

class ArrayListIterator<T> implements Iterator<T> {
  // the list of items that this iterator iterates over
  ArrayList<T> items;
  // the index of the next item to be returned
  int nextIdx;
  // Construct an iterator for a given ArrayList
  ArrayListIterator(ArrayList<T> items) {
    this.items = items;
    this.nextIdx = 0;
  }
 
  // Does this sequence (of items in the array list) have at least one more value?
  public boolean hasNext() {
    return this.nextIdx < this.items.size();
  }
 
  // Get the next value in this sequence
  // EFFECT: Advance the iterator to the subsequent value
  public T next() {
    T answer = this.items.get(this.nextIdx);
    this.nextIdx = this.nextIdx + 1;
    return answer;
  }
 
  public void remove() {
    throw new UnsupportedOperationException("Don't do this!");
  }
}
```

```java
interface IList<T> extends Iterable<T> {
  ... everything as before ...
}

class ConsList<T> implements IList<T> {
  ... everything as before ...
  public Iterator<T> iterator() {
    return new IListIterator<T>(this);
  }
}

class MtList<T> implements IList<T> {
  ... everything as before ...
  public Iterator<T> iterator() {
    return new IListIterator<T>(this);
  }
}

class IListIterator<T> implements Iterator<T> {
  IList<T> items;

  IListIterator(IList<T> items) {
    this.items = items;
  }

  public boolean hasNext() {
    return this.items.isCons();
  }

  public T next() {
    ConsList<T> itemsAsCons = this.items.asCons();
    T answer = itemsAsCons.first;
    this.items = itemsAsCons.rest;
    return answer;
  }

  public void remove() {
    throw new UnsupportedOperationException("Don't do this!");
  }
}
```
This implementation of `IList<T>` allows for the interface to be used in a for-each loop like this (since it is `Iterable<T>`):
```java
for (T item : myList) {
  // iterates forward through myList
  ...
}
```

It's also possible now to create iterable lists in multiple directions, read [here](https://course.ccs.neu.edu/cs2510/lecture25.html#%28part._.Iteration_in_multiple_directions%29). Or iterators that iterate through a tree-shaped data structure, read [here](https://course.ccs.neu.edu/cs2510/lecture25.html#%28part._.Iterators_over_tree-shaped_data%29).

# Hashmaps (Lecture 26)
Hashmaps are a data-structure that allows fast-access to objects within the map. It uses a *key*, unique to each object, to add, remove, or modify items.
```java
import java.util.HashMap;

Hashmap<K, V> // where K = type of key, V = type of value

// example
Hashmap<String, String> rooms = new HashMap<String, String>();
// add data to hashmap
rooms.put("Ben Lerner", "WVH314");
rooms.put("Leena Razzaq", "WVH310B");
rooms.put("Olin Shivers", "WVH308");
rooms.put("Matthias Felleisen", "WVH308B");
// get value using key
rooms.get("Olin Shivers"); // -> "WVH310B"
rooms.get("Ben Lerner"); // -> "WVH314"
rooms.get("Amal Ahmed"); // -> null
// check if key exists
rooms.containsKey("Leena Razzaq"); //true
rooms.containsKey("Amal Ahmed"); //false
```
Hashmaps must not contain *key collisions* (i.e., two keys cannot be the same but refer to different values). However, this doesn't seem to be too important for the exam. More information can be found [here](https://course.ccs.neu.edu/cs2510/lecture26.html#%28part._.Hash_collisions%29).

For custom classes/interfaces, we must define our own `hashCode()` method. There is no 'right' way to do this it seems but here is a simple example (the goal is to create a unique key for each book):
```java
class Book {
  Author author;
  String title;
  int year;

  public int hashCode() {
    return this.author.hashCode() * 10000 + this.year;
  }
}
```

It is also possible to define our own `equals()` method that overides the general `equals()` method. Following the previous example, we can implement it this way:
```java
class Book {
  Author author;
  String title;
  int year;
  
  public boolean equals(Object other) {
    if (!other instanceof Book) { return false; }
    // this cast is safe, because we just checked instanceof
    Book that = (Book)other;
    return this.author.equals(that.author)
        && this.year == that.year
        && this.title.equals(that.title);
  }
}
```
We can also call other sameness methods that we have learned about before. For example, in the `IShape` interface, we could override `equals()` and call `sameShape`. Read more [here](https://course.ccs.neu.edu/cs2510/lecture26.html#%28part._.Defining_custom_equals_methods_for_itemizations%29).
