---
layout: post
title: "Core Java Part 2 : Collections Framework Introduction, ArrayList & LinkedList"
slug: "core-java-2-collections-framework-intro-list"
date: 2025-08-07
author: Anubhav Srivastava
tags: [core java, collections framework, arraylist, linkedlist]
version: 1.0
---

{:toc}

---

## 📚 1. Java Collections Framework

> The Java Collections Framework is a **core part of the Java language** that provides a standard architecture to store, retrieve, and manipulate groups of objects.


### 🌲1.1 Java Collections Hierarchy Diagram

```markdown
java.lang.Object
    └── java.lang.Iterable<T>  ← ROOT of Collections Framework
          └── java.util.Collection<T>
                ├── java.util.List<T>
                │     ├── ArrayList
                │     ├── LinkedList
                │     └── Vector / Stack (legacy)
                │
                ├── java.util.Set<T>
                │     ├── HashSet
                │     │     └── LinkedHashSet
                │     └── TreeSet (SortedSet)
                │
                └── java.util.Queue<T>
                      ├── PriorityQueue
                      └── ConcurrentLinkedQueue
                      └── java.util.Deque<T>   ← (Interface)
                            ├── ArrayDeque
                            └── LinkedList  ← also implements Deque

java.util.Map<K, V>  ← NOT a subtype of Collection
    ├── HashMap
    │     └── LinkedHashMap
    └── TreeMap
```
> * LinkedList implements both List and Deque.

### 1.2 `Iterable<T>` - Root

All collection types **must implement** `Iterable<T>` so they can be used in **enhanced for-loops** (for-each loops):

```java
for (String item : collection) {
    System.out.println(item);
}
```

This works because `Iterable` provides:

```java
public interface Iterable<T> {
    Iterator<T> iterator();
}
```

The `iterator()` method returns an `Iterator<T>` — which defines how to iterate over the collection.

```java
public interface Iterator<T> {
    boolean hasNext();
    T next();
    //other optional methods
}
```

> * hasNext() - Returns true if there are more elements in the collection.
> * next() - returns the next element in the collection.

#### ✅ 1.2.1 Custom Example: Implementing Our Own `Iterable`

Iterable is at the root of the collection hierarchy and it must be implemented by each Collection. Hence it's important to know how it works. In the following example, let's construct a custom Iterator implementation.

##### 📦 Defining the class

```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class MyIntCollection implements Iterable<Integer> {
    private int[] data;
    private int size;

    public MyIntCollection(int capacity) {
        data = new int[capacity];
        size = 0;
    }

    public void add(int value) {
        if (size < data.length) {
            data[size++] = value;
        }
    }

    @Override
    public Iterator<Integer> iterator() {
        return new MyIntIterator();
    }

    private class MyIntIterator implements Iterator<Integer> {
        private int index = 0;

        @Override
        public boolean hasNext() {
            return index < size;
        }

        @Override
        public Integer next() {
            if (!hasNext()) throw new NoSuchElementException();
            return data[index++];
        }
    }
}
```

##### ✅ Usage:

```java
public class Main {
    public static void main(String[] args) {
        MyIntCollection collection = new MyIntCollection(5);
        collection.add(10);
        collection.add(20);
        collection.add(30);

        for (int value : collection) {
            System.out.println(value);
        }
    }
}

🟢 Output:
10
20
30
```

This works seamlessly with the **for-each loop** because we implemented `Iterable<Integer>` and returned a custom `Iterator`.

---

### 1.3 `Collection<T>` — The Base Interface

This is the **main interface** for all data structures that represent a group of objects.
It defines common operations like:

* `add(E e)`
* `remove(Object o)`
* `size()`
* `clear()`
* `contains(Object o)`
* `iterator()`

All **List**, **Set**, and **Queue** extend `Collection`.

>❗️Note: `Map` is **not** a subtype of `Collection` because it stores key-value pairs instead of just values.

### 🔸1.4  Interfaces Breakdown

| Interface                    | Purpose                                                 |
| ---------------------------- | ------------------------------------------------------- |
| `List`                       | Ordered collection, duplicates allowed. Indexed access. |
| `Set`                        | Unordered, no duplicates.                               |
| `SortedSet` / `NavigableSet` | Ordered sets (`TreeSet`)                                |
| `Queue`                      | FIFO-based collections with offer/poll/peek methods.    |
| `Deque`                      | Double-ended queue                                      |
| `Map`                        | Key-value pairs, not a `Collection`                     |

---

## ✅ 2. `ArrayList`

> `ArrayList` is a **resizable array** implementation of the `List` interface.

> * Ordered → Maintains insertion order
> * Indexed → We can access elements by position
> * Allows duplicates
> * Not synchronized by default


### 🔧 Internal Structure of `ArrayList`

```java
public class ArrayList<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, Serializable
```

### 🔩 Under the Hood

Internally, `ArrayList` uses a regular **array of Objects** to store elements.

```java
transient Object[] elementData; // the actual backing array
private int size;               // number of elements currently stored
```

### ⚙️ How It Works: Step-by-Step

#### 2.1 **Initialization**

When we create an ArrayList:

```java
ArrayList<String> list = new ArrayList<>();
```

Internally:

* `elementData` is created with **default capacity = 10**
* But it’s not allocated immediately unless we add elements

```java
DEFAULT_CAPACITY = 10;
```

#### 2.2 **Adding Elements**

```java
list.add("Java");
```

Behind the scenes:

1. Checks if current `size < capacity`
2. If yes: Adds the element to `elementData[size]`
3. Increments `size`

```java
elementData[size++] = "Java";
```

---

#### 🔹 2.3 **Resizing (Dynamic Array)**

If capacity is full, it resizes the array:

```java
elementData = Arrays.copyOf(elementData, newCapacity);
```

🔧 **Growth formula** (since Java 8):

```java
newCapacity = oldCapacity + (oldCapacity >> 1); // i.e., 1.5x growth
```

➡️ This means if current capacity is 10, next will be 15, then 22, 33, and so on.

📌 This is why adding elements **occasionally incurs resizing**, which is a costly operation (O(n)).

---

#### 🔹 2.4 **Getting Elements**

```java
String val = list.get(2);
```

Internally:

```java
return (E) elementData[index];
```

This is **O(1)** access, just like arrays.

---

#### 🔹 2.5 **Removing Elements**

```java
list.remove(2);
```

Internally:

* Shifts all elements to the **left** starting from index+1
* Decrements `size`

```java
System.arraycopy(elementData, index + 1, elementData, index, size - index - 1);
```

So this is **O(n)** in worst case.

#### ⚠️ 2.6 Performance Summary

| Operation       | Time Complexity  | Notes                      |
| --------------- | ---------------- | -------------------------- |
| `add(E)`        | O(1) *amortized* | Resizing occasionally O(n) |
| `get(index)`    | O(1)             | Direct array access        |
| `set(index, e)` | O(1)             | Replace by index           |
| `remove(index)` | O(n)             | Needs shifting             |
| `contains(e)`   | O(n)             | Linear search              |


#### 🧠 2.7 Why to Use ArrayList?

✅ Fast random access
✅ Simple resizing logic
✅ Good for **read-heavy** lists
❌ Not ideal for frequent middle insertions/deletions


#### 🔒 Thread-Safety?

* Not thread-safe by default.
* Use `Collections.synchronizedList(list)` for basic sync.
* Or use `CopyOnWriteArrayList` (we’ll cover later) for concurrent scenarios.

> Example

```java
ArrayList<String> list = new ArrayList<>();
list.add("Java");
list.add("Python");
list.add("C++");
```

Internally:

```
elementData: ["Java", "Python", "C++", null, null, null, null, null, null, null]
size: 3
capacity: 10
```

---

## ✅ 3. `LinkedList`

`LinkedList` is a **doubly linked list** implementation of both:

```java
public class LinkedList<E> 
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, Serializable
```

It supports:

* **List** → indexed operations
* **Deque** → add/remove from both ends
* **Queue** behavior (FIFO)

### 🔧 3.1 Internal Structure

Instead of an array, `LinkedList` is made of **nodes** linked together.

* ***Node Structure:**

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
```

And the `LinkedList` keeps two references:

```java
transient Node<E> first;
transient Node<E> last;
```

This makes it a **doubly-linked list** (not singly).

### 📦 3.2 Example

```java
LinkedList<String> list = new LinkedList<>();
list.add("Java");
list.add("Python");
list.add("C++");
```

Internally:

```
first → [Java] ⇄ [Python] ⇄ [C++] ← last
```

> * Each node links forward and backward.


### ⚙️ 3.3 How Key Operations Work in a LinkedList

#### 🔹 3.3.1 **Adding Elements**

```java
list.add("Go");
```

* If adding to end (`add()` or `addLast()`):

  * Create new node with `prev = last`
  * Set `last.next = newNode`
  * Update `last`

➡️ O(1) time.

#### 🔹 3.3.2 **Adding at index**

```java
list.add(1, "Kotlin");
```

* Traverse from head or tail (whichever is closer)
* Insert between two nodes
* Update links

➡️ O(n) in worst case because of traversal.

#### 🔹 3.3.3 **Getting an Element**

```java
list.get(2);
```

* Again, needs **traversal**:

  * From `first` or `last` depending on index
* No direct access like arrays

➡️ O(n) time

#### 🔹 3.3.4 **Removing an Element**

```java
list.remove(1);
```

* Traverse to node
* Update `prev.next` and `next.prev`
* Clear the node

➡️ O(n) due to traversal, but actual unlinking is O(1)

### 3.4 Time Complexity Summary

| Operation       | Time Complexity | Notes                 |
| --------------- | --------------- | --------------------- |
| `add(E)`        | O(1)            | At end of list        |
| `add(index, E)` | O(n)            | Requires traversal    |
| `get(index)`    | O(n)            | No direct access      |
| `remove(index)` | O(n)            | Traversal + unlinking |
| `removeFirst()` | O(1)            | Fast head removal     |
| `removeLast()`  | O(1)            | Fast tail removal     |

---

### 🧠 3.5 When to Use LinkedList

| Use Case                      | Recommendation          |
| ----------------------------- | ----------------------- |
| Frequent insertions/deletions | ✅ Better than ArrayList |
| Random access                 | ❌ Slower than ArrayList |
| Queue/Deque behavior          | ✅ Perfect for FIFO/LIFO |

---

## 🔄 4 Key Differences: ArrayList vs LinkedList

| Feature           | ArrayList                 | LinkedList                 |
| ----------------- | ------------------------- | -------------------------- |
| Backing structure | Dynamic array             | Doubly linked list         |
| Access time       | O(1)                      | O(n)                       |
| Insertion         | O(n) (middle), O(1) (end) | O(1) if pointer known      |
| Deletion          | O(n) (middle)             | O(1) if pointer known      |
| Memory usage      | Less (array)              | More (node objects, links) |

---