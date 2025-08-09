---
layout: post
title: "Core Java Part 6 : Garbage Collection & JVM Memory Management"
slug: "core-java-6-garbage-collection-jvm"
date: 2025-07-21
author: Anubhav Srivastava
tags: [core java, garbage collection, memory]
version: 1.0
---

* TOC
{:toc}

---

## 🔄 1. Java Object Lifecycle: From Birth to Garbage Collection

Everything in Java is an **object** (well, almost everything). But have we ever wondered **what happens to an object behind the scenes**, from creation to destruction?

This process is called the **Object Lifecycle**—and it’s one of the most fundamental things needed to be known as a Java Developer.


### 🛠️ Stage 1: Object Creation (Birth)

#### 🧠 Intuition

> Think of creating a new car in a factory. We allocate resources (steel, engine, etc.), assemble them, and then bring it to life.

In Java, when we create an object like this:

```java
Car c = new Car();
```

Here's what happens step-by-step:

#### 🔍 Under the Hood:

1. **Memory is allocated** on the heap for a new `Car` object.
2. The object is initialized with default values (e.g., `0`, `null`, `false`).
3. The constructor is called to initialize the object explicitly.
4. A reference (`c`) is created to point to that object.

---

#### 📘 Code Example:

```java
class Car {
    Car() {
        System.out.println("Car constructor: Object created!");
    }
}
```

```java
Car c = new Car();  // Car constructor: Object created!
```

---

### 🧪 Stage 2: Object Usage (Life)

Once the object is created, it **lives in memory**, ready to perform actions, hold data, and collaborate with other objects.

#### 🧠 Analogy

> A car on the road: it drives, turns, plays music—this is the active life phase.

We interact with it via methods, access properties, and pass it around.

#### 📘 Example:

```java
class Car {
    String model;

    Car(String model) {
        this.model = model;
    }

    void drive() {
        System.out.println(model + " is driving...");
    }
}
```

```java
Car c = new Car("Tesla");
c.drive();  // Tesla is driving...
```

---

### 🗑️ Stage 3: Object Eligible for Garbage Collection (Old Age)

#### 🔍 What is Garbage Collection?

Java uses **automatic garbage collection** we don’t manually free memory.
Instead, when an object is **no longer referenced**, it becomes **eligible for GC** (garbage collection).

> GC = Janitor that cleans up unused objects from the heap.

---

#### 🧠 When Does an Object Become Eligible for Garbage Collection ?

When **no variable points to it anymore**.

```java
Car c = new Car("Ford");
c = null;  // Now the Ford object is unreachable
```

OR:

```java
Car c1 = new Car("Ford");
Car c2 = new Car("Tesla");

c1 = c2; // The "Ford" object is no longer referenced
```

---

### ⚰️ Stage 4: Object Finalization (Before Death)

#### ❌ Deprecated: `finalize()` method

Java earlier allowed us to override the `finalize()` method as a **"last chance"** to clean up before the object is collected:

```java
protected void finalize() throws Throwable {
    System.out.println("Object is being garbage collected");
}
```

⚠️ But `finalize()` has been **deprecated since Java 9** and **removed in Java 18**
Why? Because:

* It’s **unpredictable**
* It may never be called
* It delays GC

---

#### ✅ Modern Alternatives: `AutoCloseable` & `try-with-resources`

If our object holds **external resources** (like files, sockets), we **shouldn’t wait for GC**—**instead close them ourselves** using `AutoCloseable`.

##### 📘 Example:

```java
class MyResource implements AutoCloseable {
    public void use() {
        System.out.println("Using resource...");
    }

    public void close() {
        System.out.println("Resource closed.");
    }
}
```

```java
try (MyResource res = new MyResource()) {
    res.use();
}
// Output:
// Using resource...
// Resource closed.
```

> We should use this pattern if we need **deterministic cleanup**.

---

## 🧠 2. Lifecycle Recap

Let’s summarize the full lifecycle in human-like terms:

| Stage             | Java Equivalent                      | Real-World Analogy        |
| ----------------- | ------------------------------------ | ------------------------- |
| Born              | `new` keyword + constructor          | Car built in factory      |
| Alive             | Object is in use                     | Car driving on the road   |
| Abandoned         | All references removed               | Car left in a junkyard    |
| Garbage Collected | GC picks up and frees memory         | Car scrapped and recycled |
| Final Words       | `finalize()` (deprecated) or close() | Saying goodbye or cleanup |

---

### 🛠️ Behind the Scenes: How GC Works (High-Level)

* **Mark-and-Sweep**: Marks reachable objects, then deletes unreachable ones.
* **Stop-the-world**: GC can pause all threads temporarily.
* **Generational GC**:

  * **Young Generation** (new objects)
  * **Old Generation** (long-living objects)
  * **PermGen/Metaspace** (for class metadata)

We don’t need to micromanage memory, but understanding this helps us:

* Avoid memory leaks
* Use `try-with-resources` correctly
* Write memory-efficient applications

---

### ✅ Tips for Object Lifecycle Management

1. **Avoid memory leaks** by releasing references when done.
2. **Use weak references** for cache-like behavior.
3. **Use `try-with-resources`** for managing file/DB resources.
4. **Avoid overriding `finalize()`** — it's legacy.
5. **Understand escape analysis** if we're tuning performance (advanced).

---

## 🧠 3. JVM Memory Management: What we as Java Developer's Must Know

The Java Virtual Machine (JVM) is like the **engine** behind every Java program. Understanding **how it manages memory** helps us avoid issues like:

* Memory leaks
* StackOverflowErrors
* OutOfMemoryErrors
* Inefficient object usage

---

### 📦 JVM Memory Areas (Simplified)

When a Java program runs, the JVM divides memory into **distinct regions**:

### 🗂️ 1. **Stack** – Short-lived & Thread-specific

* Stores **method calls**, **local variables**, and **reference copies**.
* One **stack per thread**.
* Memory is allocated/deallocated automatically as methods are called/returned.
* Faster than heap.

> 📌 Think of it like a stack of plates—last in, first out.

---

#### 📘 Stack Example:

```java
public void compute() {
    int a = 10;        // stored in stack
    String name = "Bob";  // reference stored in stack, object in heap
}
```

---

### 🧺 2. **Heap** – Where Objects Live

* Stores **all Java objects and arrays**.
* Shared across all threads.
* Garbage-collected automatically.

> 📌 Think of it like a big object warehouse.

---

#### 📘 Heap Example:

```java
Person p = new Person("Alice");
// 'p' is on stack, but new Person is in heap
```

---

### 🧠 3. **Method Area / Metaspace**

* Stores **class metadata**: class names, method definitions, constant pools, etc.
* Used to be called **PermGen** (before Java 8), now it’s **Metaspace**.

---

### 🛠️ 4. **PC Register** & **Native Method Stack**

* Internal machinery for bytecode execution.
* Mostly useful for JVM engineers.


#### 🔄 How Memory is Allocated

```java
public void createPerson() {
    Person p = new Person("John");
}
```

* Stack:

  * method `createPerson()` call
  * reference `p`
* Heap:

  * `Person` object with field `"John"`

When the method ends:

* Stack memory is cleaned up automatically
* Heap object remains **until no references** → then **garbage collected**

---

### ⚖️ Heap vs Stack — Comparison Table

| Feature       | Stack                    | Heap                  |
| ------------- | ------------------------ | --------------------- |
| Scope         | Method-local             | Application-wide      |
| Lifetime      | Until method ends        | Until GC cleans it    |
| Speed         | Very fast                | Slower                |
| Access        | LIFO (Last In First Out) | Random                |
| Thread Safety | Thread-local             | Shared across threads |
| Stores        | Primitives, references   | Objects, arrays       |

---

## 🚀 4. Escape Analysis – Optimize Object Allocation

Java 6+ JVMs are smart. They use **escape analysis** to decide **where to allocate objects**: stack or heap.

#### 🧠 What is Escape Analysis?

> Determines whether an object “escapes” the method or thread where it was created.

If an object:

* ❌ Does NOT escape → JVM can **allocate it on the stack** (faster)
* ✅ Does escape → JVM allocates on heap (slower, but persistent)

---

#### 📘 Example: Does NOT escape

```java
public int calculate() {
    Point p = new Point(5, 10);
    return p.x + p.y;
}
```

* The object `p` is **used only in this method** → may be stack-allocated.


#### 📘 Example: Escapes to outside

```java
public Point createPoint() {
    return new Point(1, 2);
}
```

* Object escapes via the return statement → must go to heap.

---

### 💡 Why Escape Analysis Matters

* **Reduces GC pressure** by avoiding heap allocation
* **Improves performance** via stack allocation and scalar replacement
* Enabled by default in modern JVMs (`-XX:+DoEscapeAnalysis`)

> ⚠️ We can't control it explicitly—but knowing it helps us to **write cleaner methods** with better optimization opportunities.

---

## 🧼 5. Garbage Collection — How JVM Cleans the Heap

GC is a **background process** that:

* Finds unreachable objects
* Frees their memory
* Reclaims heap space

---

#### 🔁 GC Generations (Simplified):

| Generation      | Description                         | Frequency            |
| --------------- | ----------------------------------- | -------------------- |
| Young Gen       | New objects                         | Collected often      |
| Old Gen         | Long-lived objects                  | Collected less often |
| Survivor Spaces | Transition area between generations | Internal             |

---

#### 🛠️ GC Algorithms (Java HotSpot):

* **Serial GC**: Best for small apps (single-threaded)
* **Parallel GC**: Good for throughput
* **G1 GC** (default in Java 9+): Balanced, low-pause collector
* **ZGC, Shenandoah**: Ultra-low latency, large heaps (JDK 11+)

We can configure GC with JVM flags like:

```bash
-XX:+UseG1GC
-XX:+UseZGC
```

---

## 🧠 6. Summary: Java Memory & Object Lifecycle

| Step             | Involves                               | Memory Area  |
| ---------------- | -------------------------------------- | ------------ |
| Object creation  | `new` + constructor                    | Heap         |
| Method call      | Parameters + local variables           | Stack        |
| Object use       | References to heap                     | Stack + Heap |
| Object forgotten | Reference removed or goes out of scope | —            |
| Object destroyed | GC removes unreachable objects         | Heap         |

---

## ✅ 7. Tips for Java Memory Efficiency

1. **Keep methods short and focused** – enables escape analysis
2. **Use local variables where possible** – stack is faster
3. **Avoid unnecessary object creation** – reuse when possible
4. **Use `try-with-resources`** – ensures deterministic cleanup
5. **Monitor with tools**:

   * `jconsole`, `VisualVM`, `Java Mission Control`

---


