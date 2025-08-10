---
layout: post
title: "Core Java Part 2 : Collections Framework (TreeMap & TreeSet)"
slug: "core-java-2-collections-framework-treemap-treeset"
date: 2025-08-10
author: Anubhav Srivastava
tags: [core java, collections framework, treemap, treeset]
version: 1.0
---

* TOC
{:toc}

---

In Java, **TreeMap** and **TreeSet** are part of the `java.util` package and belong to the **Sorted Collections** family.
Both store elements in **sorted order**, but they differ in what they store and how we use them.

Think of them like:

* **TreeMap** = Dictionary (word → meaning) sorted by word.
* **TreeSet** = Unique list of sorted items.

---

## 🌲 1. TreeMap

A **TreeMap** stores **key-value pairs** and automatically keeps the keys sorted according to their *natural order* or a provided  `Comparator`.

#### Example: Student Marks

Imagine we’re storing students’ marks with their names as keys:

```java
import java.util.*;

public class TreeMapExample {
    public static void main(String[] args) {
        TreeMap<String, Integer> studentMarks = new TreeMap<>();
        
        studentMarks.put("John", 85);
        studentMarks.put("Alice", 92);
        studentMarks.put("Bob", 78);

        System.out.println(studentMarks);
    }
}
```

**Output:**

```
{Alice=92, Bob=78, John=85}
```

Here the names (`keys`) are sorted alphabetically.

### 🔍 1.1 How TreeMap Works Internally

* **Data Structure:** **Red-Black Tree** (a type of self-balancing binary search tree).
* **Sorting:** Always sorts by **key**.
* **Time Complexity:**

  * `put()` → O(log n)
  * `get()` → O(log n)
  * `remove()` → O(log n)
* **Nulls:**

  * **Key:** Only one `null` key allowed (and only if no custom comparator that forbids it).
  * **Value:** Multiple `null` values allowed.

Internally, each key is placed into the tree such that **left child < parent < right child**, and the tree re-balances after each insertion or deletion.

### 📝 1.2 Example Code

```java
import java.util.TreeMap;

public class TreeMapExample {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();

        // Insert data
        map.put(20, "Twenty");
        map.put(10, "Ten");
        map.put(30, "Thirty");
        map.put(25, "Twenty-Five");

        // Retrieve data
        System.out.println(map.get(25)); // Twenty-Five
    }
}
```

### 📦 1.3 How `put()` Works Internally?

Let’s go step by step:

#### 1️⃣ First Insert

```java
map.put(20, "Twenty");
```

* Tree is empty, so `(20, "Twenty")` becomes the **root** node.
* Tree now:

```
(20, "Twenty") [BLACK]
```

#### 2️⃣ Second Insert

```java
map.put(10, "Ten");
```

* Compare `10` with `20`: **10 < 20**, go left.
* Left is empty → insert `(10, "Ten")` as **left child** of `20`.
* Red-Black Tree rules are checked (OK here).

```
    (20, "Twenty") [BLACK]
    /
(10, "Ten") [RED]
```


#### 3️⃣ Third Insert

```java
map.put(30, "Thirty");
```

* Compare `30` with `20`: **30 > 20**, go right.
* Right is empty → insert `(30, "Thirty")` as **right child** of `20`.

```
    (20, "Twenty") [BLACK]
    /               \
(10,"Ten")[RED]   (30,"Thirty")[RED]
```

* Still balanced, no rotations needed.

#### 4️⃣ Fourth Insert

```java
map.put(25, "Twenty-Five");
```

* Compare `25` with `20`: **25 > 20**, go right.
* Compare `25` with `30`: **25 < 30**, go left.
* Left of `30` is empty → insert `(25, "Twenty-Five")`.

```
        (20,"Twenty")[BLACK]
       /                \
 (10,"Ten")[RED]     (30,"Thirty")[RED]
                   /
       (25,"Twenty-Five")[RED]
```

* Now Red-Black Tree rules detect **two consecutive reds** (30 and 25), so:

  * Perform **recoloring** or **rotation** to maintain balance.
```
          (20,"Twenty")[BLACK]
       /                \
 (10,"Ten")[RED]     (25,"Twenty-Five")[RED]
                           \
                     (30,"Thirty")[BLACK]
```
  * End result is still sorted and height-balanced.

### 1.4 🔍 How `get()` Works Internally?

```java
map.get(25);
```

1. Compare `25` with `20` → greater → go right.
2. Compare `25` with `30` → smaller → go left.
3. Found `25` → return `"Twenty-Five"`.

* Complexity: **O(log n)** because height is balanced.

### 1.5 🚀 Key Takeaways

* `TreeMap` **does not** use buckets like `HashMap`.
* All operations are **logarithmic time** due to balancing.
* Keys are **always sorted** based on `Comparable` or a custom `Comparator`.
* Backed by a **Red-Black Tree**, which is a self-balancing BST.

---

## 🌿 2. TreeSet

A **TreeSet** stores **unique elements** and automatically keeps them sorted.
It’s actually backed by a **TreeMap internally**, where each element becomes a key and a dummy object is the value.

#### Example: Unique Contest Rankings

Imagine we’re tracking unique scores in a coding contest:

```java
import java.util.*;

public class TreeSetExample {
    public static void main(String[] args) {
        TreeSet<Integer> scores = new TreeSet<>();
        
        scores.add(85);
        scores.add(92);
        scores.add(78);
        scores.add(85); // Duplicate - ignored

        System.out.println(scores);
    }
}
```

**Output:**

```
[78, 85, 92]
```

Duplicates are ignored, and elements are in ascending order.

### 🔍 2.1 How TreeSet Works Internally

* **Data Structure:** Also a **Red-Black Tree**.
* **Sorting:** Always sorts elements in natural order (or by provided `Comparator`).
* **Time Complexity:**

  * `add()` → O(log n)
  * `contains()` → O(log n)
  * `remove()` → O(log n)
* **Nulls:**

  * Allows **one `null`** only if no custom comparator (but adding more will cause `NullPointerException`).

⚡ **Internally, `TreeSet` uses a **TreeMap** with a dummy constant object as the value.**

### 💡 2.2 TreeSet Use Cases

* Maintaining a sorted list of unique items.
* Ranking systems without duplicates.
* Autocomplete suggestions in sorted order.

---

## ⚔️ 3. Summary & Key Differences

| Feature      | TreeMap                        | TreeSet                      |
| ------------ | ------------------------------ | ---------------------------- |
| Stores       | Key-Value pairs                | Only values                  |
| Sorting      | By key                         | By value                     |
| Duplicates   | Keys unique, values can repeat | No duplicates at all         |
| Null Support | One null key allowed           | One null value allowed       |
| Backed By    | Red-Black Tree                 | Red-Black Tree (via TreeMap) |

> Both **TreeMap** and **TreeSet** are powerful when we need **sorted data** without manual sorting.

> * Use **TreeMap** when we need key-value pairs in sorted order.
> * Use **TreeSet** when we need only unique, sorted values.

They may be slightly slower than `HashMap` or `HashSet` for huge data because of O(log n) operations, but they give us the priceless advantage of **sorted access**.

---
