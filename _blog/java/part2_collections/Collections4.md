---
layout: post
title: "Core Java Part 2 : Collections Framework (Fail-Fast vs Fail-Safe Iterators & Comparators)"
slug: "core-java-2-collections-framework-iterators-comparators"
date: 2025-08-10
author: Anubhav Srivastava
tags: [core java, collections framework, iterator, comparator]
version: 1.0
---

* TOC
{:toc}

---

## 🔒 1. Immutability

Immutability means **once an object is created, its state cannot change**. Instead of modifying an object, we create a new one when we need different data.

📌 Examples:

* `String` in Java is immutable.
* Wrapper classes like `Integer`, `Double`, etc., are immutable.

```java
String name = "Anubhav";
name.concat(" S"); // Does not change 'name'
System.out.println(name); // "Anubhav"
```

### 1.1 Why Do We Need It?

1. **Thread-safety** – Immutable objects can be shared without synchronization.
2. **Security** – Prevents unauthorized or accidental changes.
3. **Predictability** – No hidden side effects from changing state.
4. **Safe for caching** – Can be safely reused without fear of mutation.

### 1.2 How to Make a Class Immutable

Rules:

1. Declare the class `final` (can’t be subclassed).
2. Make all fields `private final`.
3. Don’t provide setters.
4. Initialize fields via constructor.
5. Return deep copies of mutable fields.

```java
public final class Student {
    private final String name;
    private final int rollNo;

    public Student(String name, int rollNo) {
        this.name = name;
        this.rollNo = rollNo;
    }

    public String getName() {
        return name;
    }

    public int getRollNo() {
        return rollNo;
    }
}
```

---

## 🔄 2. Iterators in Java

Iterators allow us to traverse collections. But what happens if a collection is modified while we’re iterating over it?

That’s where **Fail-Fast** and **Fail-Safe** iterators differ.

### 2.1 Fail-Fast Iterators

* Throw a **`ConcurrentModificationException`** if the collection is modified **structurally** during iteration (except via iterator’s own `remove()`).
* Detects changes by comparing an **`expectedModCount`** with the collection’s internal `modCount`.

**Example:**

```java
import java.util.*;

public class FailFastDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A"); list.add("B"); list.add("C");

        for (String s : list) {
            if (s.equals("B")) {
                list.remove(s); // Throws ConcurrentModificationException
            }
        }
    }
}
```

💡 Works this way in **ArrayList, HashMap, HashSet, etc.**

### 2.2 Fail-Safe Iterators

* Do **not** throw an exception when the collection is modified during iteration.
* Iterate over a **clone** of the collection.
* Slower and use more memory.

**Example:**

```java
import java.util.*;
import java.util.concurrent.CopyOnWriteArrayList;

public class FailSafeDemo {
    public static void main(String[] args) {
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        list.add("A"); list.add("B"); list.add("C");

        for (String s : list) {
            if (s.equals("B")) {
                list.remove(s); // No exception
            }
        }
        System.out.println(list);
    }
}
```

💡 Works this way in **CopyOnWriteArrayList, ConcurrentHashMap, etc.**


### 2.3 Key Differences

| Feature               | Fail-Fast                                | Fail-Safe                               |
| --------------------- | ---------------------------------------- | --------------------------------------- |
| Modification allowed? | ❌ No (throws exception)                  | ✅ Yes                                   |
| How it works          | Checks `modCount` for structural changes | Works on a cloned snapshot              |
| Memory usage          | Low                                      | Higher                                  |
| Speed                 | Faster                                   | Slower                                  |
| Examples              | ArrayList, HashMap, HashSet              | CopyOnWriteArrayList, ConcurrentHashMap |

---

## 🔍 3. Comparators in Java

A **Comparator** in Java is used to define a custom order for objects that **don’t have a natural ordering** or when we want to override the natural ordering temporarily.

There are two main ways to provide ordering:

* **`Comparable`**: The class itself implements `compareTo()` (natural ordering).
* **`Comparator`**: An external object defines ordering logic.

**Why use a Comparator?**

* When we can’t modify the source class (e.g., it’s from a library).
* When we want different sorting strategies for the same class.

### 🛠 3.1 Implementing Comparators

#### A. Implementing via a Separate Class

```java
import java.util.*;

class Employee {
    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

class EmployeeNameComparator implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {
        return e1.name.compareTo(e2.name);
    }
}

public class ComparatorExample {
    public static void main(String[] args) {
        List<Employee> list = Arrays.asList(
            new Employee(1, "Charlie"),
            new Employee(2, "Alice"),
            new Employee(3, "Bob")
        );

        Collections.sort(list, new EmployeeNameComparator());

        list.forEach(e -> System.out.println(e.name));
    }
}
```

#### B. Using an Anonymous Class

```java
Collections.sort(list, new Comparator<Employee>() {
    @Override
    public int compare(Employee e1, Employee e2) {
        return e1.id - e2.id;
    }
});
```
#### C. Using Lambda Expressions

```java
list.sort((e1, e2) -> e1.name.compareTo(e2.name));
```

#### D. Passing Comparator to Collections like TreeSet

```java
Set<Employee> employees = new TreeSet<>((e1, e2) -> e1.id - e2.id);
employees.add(new Employee(1, "Charlie"));
employees.add(new Employee(2, "Alice"));
```

Here, the ordering logic is **bound to the collection itself**.

---

### ⚖ 3.2 Why Comparator Logic Should Be Consistent with equals()

In Java Collections, **ordering** and **equality** are often linked.

For example:

* **`HashSet`**: uses `equals()` + `hashCode()` to check duplicates.
* **`TreeSet` / `TreeMap`**: uses `compare()` to determine ordering **and** uniqueness.

If our `compare()` logic is inconsistent with `equals()`:

* We may end up with "duplicate-looking" elements in a `HashSet` but **missing elements** in a `TreeSet`.
* Or elements might not be found when searching.



#### 🚨 Example of Inconsistency

```java
class Person {
    String name;
    int age;

    Person(String name, int age) { this.name = name; this.age = age; }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Person)) return false;
        Person p = (Person) o;
        return name.equals(p.name) && age == p.age;
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

// Comparator only compares by name (inconsistent!)
Comparator<Person> cmp = (p1, p2) -> p1.name.compareTo(p2.name);
```

**Problem:**

* Two people with the same `name` but different `age` will be considered **equal** in a `TreeSet` but **different** in a `HashSet`.
* This can cause unpredictable behavior when switching collections.

#### ✅ Best Practice

If we use a Comparator for **ordered collections** (`TreeSet`, `TreeMap`), make sure:

```text
compare(a, b) == 0  ⇔  a.equals(b) == true
```

This ensures consistent behavior across all collections.

### 📌 3.3 Key Takeaways

1. **Comparator** is for custom ordering; `Comparable` is for natural ordering.
2. Pass Comparators to `sort()` or to ordered collections like `TreeSet`.
3. Ensure **compare()** and **equals()** define equality consistently.
4. Inconsistencies lead to **data duplication or loss** in collections.

---