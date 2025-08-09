---
layout: post
title: "Core Java Part 2 : Collections Framework HashMap & HashSet"
slug: "core-java-2-collections-framework-hashmap-hashset"
date: 2025-08-08
author: Anubhav Srivastava
tags: [core java, collections framework, hashmap, hashset]
version: 1.0
---

* TOC
{:toc}

---

## ✅ 1. HashMap

> `HashMap<K, V>` is a **key-value pair data structure** that provides:

> * **Fast lookup (O(1))** for get/put operations on average
> * Allows **null** keys and values (but only one null key)
> * **Unordered** — doesn’t preserve insertion order
> * **Not synchronized**

### ⚙️ 1.1 Internal Structure (Java 8+)

* Internally, `HashMap` is an array of buckets:

```java
transient Node<K, V>[] table;
```

Each **bucket** is a **linked list** (or a **balanced tree** if it gets too long).

* **The Node structure:**

```java
static class Node<K, V> implements Map.Entry<K, V> {
    final int hash;
    final K key;
    V value;
    Node<K, V> next;
}
```

### 🔍 1.2 How Does `HashMap` Work?

#### 🧪 1.2.1 How are keys and values stored : `put()` operation?

```java
map.put("language", "Java");
```

**🧠 Internally, following steps are performed:**

##### 🔸 a. **Hashing** the key:

```java
int hash = hash("language");  // spreads hash bits
```

The hash function ensures **even distribution** and reduces collisions.

##### 🔸 b. **Bucket Index:**

```java
int index = (n - 1) & hash; // where n = table.length
```

This computes which **bucket** to use for storing this <key, value> node.

##### 🔸 c. **Insert Node:**

* If bucket is empty → just insert node
* If bucket has nodes:

  * Check keys using `.equals()`
  * If same key → overwrite value
  * If different key → append to list or treeify (see below)

##### 🌳 Treeification (Java 8 feature)

If a bucket has **>8 nodes**, and total map size is **>=64**, the linked list becomes a **balanced Red-Black Tree** to avoid O(n) lookup time.

```java
Node → TreeNode (extends Node)
```

➡️ This makes worst-case lookup time **O(log n)**, not O(n).

---
---

#### 🪣 1.2.1 What is a “Bucket” in `HashMap` (In Depth Explaination) ?

Think of a **bucket as a container** at a specific index in an internal array.
Each element we add to a `HashMap` ends up in one of these buckets based on its **hash value**.

Internally, `HashMap` has:

```java
Node<K, V>[] table;  // an array of buckets
```

Each index in this array is a **bucket**.


##### ✅ Let’s Take an Example

```java
HashMap<String, String> map = new HashMap<>();
map.put("dog", "bark");
map.put("cat", "meow");
map.put("lion", "roar");
```

Here’s how Java stores these:

##### Step 1️⃣: Hash the Key

For each key (`"dog"`, `"cat"`, `"lion"`), Java generates a hash code:

```java
int hash = key.hashCode();
```

Then it calculates the **bucket index** like this:

```java
int index = (hash & (table.length - 1));
```

So every key is mapped to a particular **index in the array**.


##### Step 2️⃣: Find the Right Bucket

Suppose `table.length = 16`. After applying the above formula:

| Key    | hashCode | index | Bucket    |
| ------ | -------- | ----- | --------- |
| "dog"  | 99532    | 4     | table\[4] |
| "cat"  | 98239    | 9     | table\[9] |
| "lion" | 183532   | 4     | table\[4] |

##### Uh-oh! Collision!

Notice both `"dog"` and `"lion"` go to the **same bucket** `table[4]`.
This is where chaining comes in.

##### 🔗 Chaining in Buckets

If multiple entries go into the same bucket, they are **chained as a linked list**.

```java
table[4] → Node("dog", "bark") → Node("lion", "roar")
```

Each node holds:

* `key`
* `value`
* `next` pointer (for the next node in the same bucket)

##### 🤖 So What Happens Internally?

Here's what Java does on `put()`:

1. Calculates the index (bucket) from the key's hash
2. Checks if any node exists at that index
3. If yes:

   * Compares keys using `equals()`
   * If same → updates value
   * If different → appends new node
4. If no:

   * Simply inserts a new node at that bucket

---

##### 📦 Visual Representation

```
Index | Bucket Contents
------+---------------------------
  0   | null
  1   | null
  2   | null
  3   | null
  4   | [dog → bark] → [lion → roar]
  5   | null
  6   | null
  7   | null
  8   | null
  9   | [cat → meow]
...
```

##### 🧠 Summary

* A **bucket** is just a slot (index) in an array.
* Each bucket holds a **linked list (or tree)** of entries that hash to that index.
* If multiple keys hash to the same bucket → Java **chains** them using `Node.next`.
* Java 8+: if more than 8 entries collide in one bucket → switches to a **Red-Black Tree** for faster lookup.

---
---

#### 🧪 1.2.2 What about `get()` operation?

```java
map.get("language");
```

1. Hash the key
2. Locate bucket
3. Search the bucket (linear or binary search depending on list/tree)
4. Compare keys using `.equals()`
5. Return value if found

##### 🔍 Internal Steps of `get(key)` in `HashMap` (In Depth Explanation)

##### Step 1️⃣. Compute the Hash

```java
int hash = hash("lion");  // Java computes a spread hash
```

> Java uses `Objects.hashCode(key)` + a bit-spreading method to get a **uniform distribution**.

##### Step 2️⃣. Find the Bucket Index

```java
int index = (n - 1) & hash;  // n = table.length
```

This gives us the **bucket index** (say, index = 4).

##### Step 3️⃣. Look Inside That Bucket

```java
Node<K,V> first = table[index];
```

That `first` node may be:

```
[dog → bark] → [lion → roar] → null
```

##### Step 4️⃣. Traverse the Chain (Linked List or Tree)

Java traverses the nodes **one by one**, comparing each node’s key:

```java
if (first.hash == hash && (first.key.equals("lion"))) {
    return first.value;
}
```

If not, go to `first.next`, and repeat.

* `"dog"` ≠ `"lion"` → skip
* `"lion"` == `"lion"` → return `"roar"`

##### 🚨 Important: Uses `hashCode()` and `equals()`

To identify the **correct key**, Java checks:

* `hash` → for fast lookup
* `equals()` → for exact match

##### 🌳 If It’s a TreeNode

If the bucket has been **treeified** (i.e. has a Red-Black Tree instead of a list), then it uses **binary search** on the tree nodes:

```java
TreeNode.getTreeNode(hash, key)
```

Which is **O(log n)** instead of O(n)


##### 🧠 Visual Example

Say the internal table looks like this:

```
table[4] →
  ┌─────────────────────────────┐
  │ [hash1, "dog",  "bark"]     │
  │   ↓                         │
  │ [hash2, "lion", "roar"]     │
  └─────────────────────────────┘
```

Doing `map.get("lion")` will:

1. Go to `table[4]`
2. First node: `"dog"` ≠ `"lion"` → move to next
3. Second node: `"lion"` == `"lion"` → return `"roar"`

##### 🧮 Time Complexity for `get()` operation

| Case         | Time     |
| ------------ | -------- |
| Best/Average | O(1)     |
| Worst (list) | O(n)     |
| Worst (tree) | O(log n) |

> Java uses **treeification** to **avoid O(n)** degradation due to too many collisions.

##### 🧪 Summary of `get(key)`

| Step        | What Happens                                          |
| ----------- | ----------------------------------------------------- |
| `hash(key)` | Java generates a spread hash                          |
| Index calc  | `(n - 1) & hash` gives bucket index                   |
| Search      | Traverses linked list or tree at that bucket          |
| Match       | Uses `.equals()` and hash to find the exact key match |
| Return      | Returns value if found, `null` if not                 |

---
---

### 🔄 1.3 Resize / Rehash

If the map exceeds its **load factor (default 0.75)**, it resizes:

```java
newCapacity = oldCapacity * 2;
```

All entries are **rehashed** and redistributed across the new table.

➡️ Resizing is expensive: **O(n)**
✅ But it happens **infrequently**


### ⏱️ 1.4 Operational Time Complexity in HashMap

| Operation  | Best / Average | Worst Case (before Java 8) | Worst Case (Java 8+)(Treeification Introduced) |
| ---------- | -------------- | -------------------------- | -------------------- |
| `put()`    | O(1)           | O(n)                       | O(log n)             |
| `get()`    | O(1)           | O(n)                       | O(log n)             |
| `remove()` | O(1)           | O(n)                       | O(log n)             |

### ⚠️ 1.5 Important Notes

* HashMap Allows **one null key**, multiple null values
* Not thread-safe (use `ConcurrentHashMap` for concurrency)
* Uses **`equals()` and `hashCode()`** to manage key uniqueness


####🧠 1.6 When to Use `HashMap`

✅ Fast access by key
✅ No need for order
✅ Frequent lookups
❌ Avoid when:

* Need **ordering** → use `LinkedHashMap` or `TreeMap`
* Need **thread safety** → use `ConcurrentHashMap`


### 📌 1.7 Summary

| Feature         | Value                              |
| --------------- | ---------------------------------- |
| Backed by       | Array + Linked List / Tree         |
| Lookup Time     | O(1) avg, O(log n) worst (Java 8+) |
| Allows null key | ✅ Yes (only one)                   |
| Thread-safe     | ❌ No                               |
| Maintains order | ❌ No (use LinkedHashMap if needed) |

---

---

## ✅ 2. HashSet

### 🔷 2.1 What is a `HashSet`?

A `HashSet` is a collection that:

* Stores **unique elements**
* Has **no guaranteed order**
* Is backed internally by a **`HashMap`**

### 🔧 2.2 Internal Mechanics

Internally, a `HashSet<E>` is implemented as:

```java
private transient HashMap<E, Object> map;
```

> In other words: `HashSet` uses a `HashMap`, but only the **keys** matter. The values are just dummy placeholders.

```java
private static final Object PRESENT = new Object();
```

* When we call:

```java
set.add("apple");
```

Java does this internally:

```java
map.put("apple", PRESENT);
```

So every item in the `HashSet` becomes a **key** in the backing `HashMap`.

#### 🔁 Example

```java
Set<String> set = new HashSet<>();
set.add("apple");
set.add("banana");
set.add("apple"); // duplicate
```

Internally behaves like:

```java
map.put("apple", PRESENT);
map.put("banana", PRESENT);
// 2nd put("apple", ...) will just overwrite, not add
```

The second `"apple"` is ignored because `HashMap` does not allow duplicate keys.

#### ✅ Uniqueness Guarantee

Uniqueness in `HashSet` depends on:

* Correct `hashCode()` and `equals()` in the key type
* If these are improperly implemented, we may get **duplicates or incorrect behavior**

#### 📦 Storage

* Elements are stored as **keys** in the backing `HashMap`
* The dummy object `PRESENT` takes up minimal memory
* Hash collisions are handled just like in a normal `HashMap` (linked list / tree structure)

### 🚀 2.3 Performance

| Operation    | Big-O Time |
| ------------ | ---------- |
| `add()`      | O(1) avg   |
| `contains()` | O(1) avg   |
| `remove()`   | O(1) avg   |

In worst cases (lots of collisions): O(n)

### 📌 2.4 Summary

* `HashSet` is a thin wrapper over `HashMap`
* Every element is stored as a key in the map
* Ensures uniqueness via `hashCode()` and `equals()`
* Fast access, but unordered

---
