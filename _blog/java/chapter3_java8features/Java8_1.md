---
layout: post
title: "Core Java Part 3 : Java 8 Features (Lambdas, Functional Interfaces & Stream API)"
slug: "core-java-3-java8-features-lambdas-functional-interfaces-stream"
date: 2025-09-14
modified_date: 2025-09-14
author: Anubhav Srivastava
tags: [core java, java 8, lambdas, functional interfaces, streams]
version: 1.0
---

* TOC
{:toc}

---

### 🧠 1. Lambdas in Java 8 – The Heart of Functional Programming

#### 1.1 Introduction

Before Java 8, writing concise, functional-style code in Java was painful. You’d often find yourself creating **anonymous inner classes** just to pass a behavior to a method — verbose and hard to read.

Java 8 introduced **Lambdas** to solve this problem.
Think of a Lambda as **"a small piece of behavior packaged as data"** that you can pass around in your program.

In simple words:

> A **Lambda Expression** is a **short block of code** that takes inputs and returns a value (or performs an action).
> It’s Java’s way of treating functions like **first-class citizens** — just like variables or objects.

#### 1.2 Why Lambdas? (The Intuition)

Imagine you go to a restaurant and order food.

* **Before Java 8:** You write down a long form describing **how the chef should cook** — a big verbose anonymous class.
* **With Java 8 Lambdas:** You simply **tell the chef what you want**, like "Grill the chicken" — short, direct, and clear.

Lambdas **reduce boilerplate code**, make APIs more **expressive**, and enable **functional programming patterns** like map, filter, and reduce.


#### 1.3 Lambda Syntax

Here’s the general syntax of a Lambda:

```java
(parameters) -> { body }
```

* **parameters** → Input to the Lambda (like method parameters)
* **arrow (`->`)** → Separates parameters from the logic
* **body** → The actual logic to execute

##### Examples:

Traditional Java:
```java
Runnable r = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello Java 8");
    }
};
```

Java 8+
```java
Runnable r = () -> System.out.println("Hello Java 8");
```

**Boom! 🎇** Just one line of code — cleaner and easier to read.

#### 1.4 Step-by-Step Examples

##### Example 1: Sorting with Lambdas
Before Java 8, sorting by string length required a full Comparator implementation:

```java
import java.util.*;

public class LambdaExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Jack", "Alexander", "Bob");

        // Before Java 8
        Collections.sort(names, new Comparator<String>() {
            @Override
            public int compare(String a, String b) {
                return Integer.compare(a.length(), b.length());
            }
        });
        System.out.println("Before Java 8: " + names);

        // Java 8 Lambda
        Collections.sort(names, (a, b) -> Integer.compare(a.length(), b.length()));
        System.out.println("With Lambda: " + names);
    }
}
```

**Output:**

```text
Before Java 8: [Bob, Jack, Alexander]
With Lambda: [Bob, Jack, Alexander]
```

💡 *Cleaner, concise, and directly focuses on the core logic.*

##### Example 2: Filtering a List (with Streams)

Lambdas are often used with the **Streams API**:

```java
import java.util.*;

public class LambdaFilterExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");

        // Filter names starting with 'A'
        names.stream()
             .filter(name -> name.startsWith("A"))
             .forEach(System.out::println);
    }
}
```

**Output:**

```
Alice
```

Here, `name -> name.startsWith("A")` is a Lambda passed to the `filter()` method.

#### 1.5 Common Lambda Patterns

| **Pattern**                | **Example**                                          |
| -------------------------- | ---------------------------------------------------- |
| No parameters, no return   | `() -> System.out.println("Hi!")`                    |
| One parameter, no return   | `x -> System.out.println(x)`                         |
| Multiple parameters        | `(a, b) -> a + b`                                    |
| With a block of statements | `(x, y) -> { System.out.println(x); return x + y; }` |

#### 1.6 Where Lambdas Shine

* **Collections & Streams:** Filtering, mapping, reducing data
* **Asynchronous Programming:** With `Runnable` and `Callable`
* **Event Handling:** GUI programming, reactive programming
* **Custom Behavior Passing:** Like strategy patterns without creating tons of classes

#### 1.7 Caveats

1. **Only work with Functional Interfaces**

   * A Lambda can only be used where there is a **functional interface** (an interface with exactly one abstract method).
     Example: `Runnable`, `Callable`, `Comparator`, etc.

2. **Local Variables Must Be Effectively Final**

   ```java
   int counter = 0;
   Runnable r = () -> System.out.println(counter); // ✅ OK
   // counter++; ❌ Error if you try to modify counter
   ```

   Java enforces this to avoid concurrency issues.

3. **Don’t Overcomplicate**
   If a Lambda becomes too long or complex, move it to a named method for clarity.

---

### 🧠 2. Functional Interfaces in Java – The Backbone of Lambdas

#### 2.1 Introduction

Imagine you're in a company, and the boss says:
*"I just need **one person** to handle **one specific task**."*

That’s exactly what a **Functional Interface (FI)** is in Java:

> A **Functional Interface** is an interface that has **exactly one abstract method** (SAM – *Single Abstract Method*).

Java 8 introduced Lambdas, but **Lambdas need a target type** to know:

* **What parameters** it takes
* **What it returns**

Functional Interfaces provide this target type.
In short, **Lambdas exist because of Functional Interfaces.**

#### 2.2 Key Rule

* An interface with **only one abstract method** is a functional interface.
* It **can have** multiple:
  * **default methods**
  * **static methods**
  * **private methods** (Java 9+)
* But **only one abstract method**, otherwise it's no longer functional.

#### 2.3 Example of a Functional Interface

```java
@FunctionalInterface
interface Greeter {
    void greet(String name);
}
```

This is the simplest functional interface.
It just says, *“I will define **how to greet** someone.”*

Using a Lambda with it:

```java
public class FunctionalInterfaceExample {
    public static void main(String[] args) {
        // Lambda implementation of Greeter
        Greeter greeter = name -> System.out.println("Hello, " + name);

        greeter.greet("Alice");
    }
}
```

**Output:**

```
Hello, Alice
```

#### 2.4 The `@FunctionalInterface` Annotation

* **Purpose:** It **ensures at compile-time** that the interface follows the single abstract method rule.
* If you accidentally add a second abstract method, the compiler will yell at you.

```java
@FunctionalInterface
interface InvalidInterface {
    void doSomething();

    // ❌ ERROR: This breaks the functional interface rule
    void doSomethingElse();
}
```

> 💡 *Best practice:* Always use `@FunctionalInterface` to avoid accidental errors.

#### 2.5 Built-in Functional Interfaces (java.util.function Package)

Java 8 ships with a set of **ready-to-use functional interfaces**, so you don’t need to create your own every time.

| **Interface**         | **Method Signature**    | **Purpose**                                       |
| --------------------- | ----------------------- | ------------------------------------------------- |
| `Predicate<T>`        | `boolean test(T t)`     | Returns **true/false** based on a condition       |
| `Function<T, R>`      | `R apply(T t)`          | Transforms input `T` into output `R`              |
| `Consumer<T>`         | `void accept(T t)`      | Performs an action, **no return**                 |
| `Supplier<T>`         | `T get()`               | Supplies a value, **no input**                    |
| `BiFunction<T, U, R>` | `R apply(T t, U u)`     | Function with **two inputs**                      |
| `BiConsumer<T, U>`    | `void accept(T t, U u)` | Consumer with **two inputs**                      |
| `UnaryOperator<T>`    | `T apply(T t)`          | Function where input and output are **same type** |
| `BinaryOperator<T>`   | `T apply(T t1, T t2)`   | Combines two inputs of the same type              |

---

#### 2.6 Practical Examples

##### (A) Predicate – Testing a condition

```java
import java.util.function.Predicate;

public class PredicateExample {
    public static void main(String[] args) {
        Predicate<String> startsWithA = str -> str.startsWith("A");

        System.out.println(startsWithA.test("Apple"));  // true
        System.out.println(startsWithA.test("Banana")); // false
    }
}
```

> **Real-life analogy:**
> Predicate is like a **security guard** who says *Yes* or *No* based on a rule.

##### (B) Function – Transforming data

```java
import java.util.function.Function;

public class FunctionExample {
    public static void main(String[] args) {
        Function<String, Integer> lengthFinder = str -> str.length();

        System.out.println(lengthFinder.apply("Java"));   // 4
        System.out.println(lengthFinder.apply("Lambda")); // 6
    }
}
```

> **Analogy:** Function is like a **converter machine** — it takes raw material (input) and outputs a finished product.

##### (C) Consumer – Doing something

```java
import java.util.function.Consumer;

public class ConsumerExample {
    public static void main(String[] args) {
        Consumer<String> printUpperCase = str -> System.out.println(str.toUpperCase());

        printUpperCase.accept("hello");
        printUpperCase.accept("world");
    }
}
```

**Output:**

```
HELLO
WORLD
```

> **Analogy:** Consumer is like a **robot** that takes an item and **performs an action** — but doesn’t return anything.

##### (D) Supplier – Providing data without input

```java
import java.util.function.Supplier;
import java.util.Random;

public class SupplierExample {
    public static void main(String[] args) {
        Supplier<Integer> randomSupplier = () -> new Random().nextInt(100);

        System.out.println(randomSupplier.get());
        System.out.println(randomSupplier.get());
    }
}
```

> **Analogy:** Supplier is like a **vending machine** — you don’t give it input, but it gives you something when you ask.

---

#### 2.7 Combining Functional Interfaces

Java allows chaining and combining them, leading to **powerful functional pipelines**.

Example with `Predicate`:

```java
Predicate<Integer> isEven = x -> x % 2 == 0;
Predicate<Integer> isPositive = x -> x > 0;

// Combine them
Predicate<Integer> isPositiveEven = isEven.and(isPositive);

System.out.println(isPositiveEven.test(4));   // true
System.out.println(isPositiveEven.test(-2));  // false
```

#### 2.8 Why Built-in Interfaces Are Important

* **Standardization:** Everyone understands `Predicate` and `Function` instantly.
* **Plug & Play:** Java Streams API is designed to work seamlessly with these interfaces.
* **Less Boilerplate:** No need to create new interfaces every time.

#### 2.9 Common Pitfalls

1. **Using wrong interface type**

   * Example: Using a `Consumer` when you need a return value — should use `Function` instead.

2. **Not using method references**

   * Instead of:

     ```java
     Consumer<String> printer = str -> System.out.println(str);
     ```

     Use:

     ```java
     Consumer<String> printer = System.out::println;
     ```

3. **Forgetting about chaining**

   * Many interfaces have `andThen()` or `compose()` methods for creating pipelines.

---

### 🧠 3. Streams API in Java 8 – Data Processing Made Elegant

#### 1. Introduction

Before Java 8, if you wanted to process a collection — say, filter a list, sort it, and then transform the elements — you had to write **loops inside loops**, with tons of temporary variables and boilerplate code.

For example:

```java
List<String> names = Arrays.asList("John", "Alice", "Bob", "Charlie");
List<String> result = new ArrayList<>();

for (String name : names) {
    if (name.startsWith("A")) {
        result.add(name.toUpperCase());
    }
}
Collections.sort(result);
System.out.println(result);
```

**Problems:**

* Verbose
* Hard to read
* Not scalable
* Difficult to parallelize


**Enter Java 8 Streams!** 🚀

> A **Stream** is a pipeline for processing data — you pass data through **a series of operations**, and each step transforms it.

Think of it like an **assembly line in a factory**:

* The **input** is raw material (a collection).
* Each **machine** (stream operation) does some work like filtering or mapping.
* At the end, you get the **final product**.

#### 3.2 Stream API Key Features

* Declarative style (**What to do**, not **How to do it**)
* Supports **chainable operations** (like a fluent API)
* **Lazy evaluation** – operations run only when needed
* Easy **parallelization** for performance
* Works perfectly with **Functional Interfaces** like `Predicate`, `Function`, `Consumer`

#### 3.3 Streams Workflow

A Stream pipeline has **three stages**:

| Stage                | Example                                | Description                                            |
| -------------------- | -------------------------------------- | ------------------------------------------------------ |
| **Source**           | `list.stream()`                        | Where data comes from (`Collection`, array, I/O, etc.) |
| **Intermediate Ops** | `.filter()`, `.map()`, `.sorted()`     | Transform the stream (lazy, can be chained)            |
| **Terminal Op**      | `.collect()`, `.forEach()`, `.count()` | Produces a final result and **closes the stream**      |

> ⚡ *Once a terminal operation is invoked, the stream **cannot be reused.***

#### 3.4 Basic Example

```java
import java.util.*;
import java.util.stream.Collectors;

public class StreamBasicExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Alice", "Bob", "Charlie");

        List<String> result = names.stream()
                                   .filter(name -> name.startsWith("A")) // Keep names starting with A
                                   .map(String::toUpperCase)            // Convert to uppercase
                                   .sorted()                             // Sort alphabetically
                                   .collect(Collectors.toList());        // Collect back to list

        System.out.println(result);
    }
}
```

**Output:**

```
[ALICE]
```

Here’s what happens step-by-step:

1. **Source:** `names.stream()` – Converts list into a stream.
2. **Filter:** `.filter(name -> name.startsWith("A"))` – Keep only names starting with "A".
3. **Map:** `.map(String::toUpperCase)` – Convert remaining names to uppercase.
4. **Sorted:** `.sorted()` – Sort the stream.
5. **Terminal:** `.collect(Collectors.toList())` – Convert the stream back to a list.

#### 3.5 Intermediate Operations (Transformers)

Intermediate operations **don’t execute immediately**.
They just **define the pipeline** and are **lazy** — meaning they run **only when a terminal operation is called**.

| Operation             | Purpose                                 | Example                      |
| --------------------- | --------------------------------------- | ---------------------------- |
| **filter(Predicate)** | Keep only elements matching a condition | `.filter(x -> x > 10)`       |
| **map(Function)**     | Transform each element                  | `.map(String::toUpperCase)`  |
| **flatMap(Function)** | Flatten nested structures               | `.flatMap(List::stream)`     |
| **distinct()**        | Remove duplicates                       | `.distinct()`                |
| **sorted()**          | Sort stream elements                    | `.sorted()`                  |
| **limit(n)**          | Keep first `n` elements                 | `.limit(5)`                  |
| **skip(n)**           | Skip first `n` elements                 | `.skip(2)`                   |
| **peek(Consumer)**    | Debug or peek at values                 | `.peek(System.out::println)` |

##### Example: filter + map

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

numbers.stream()
       .filter(n -> n % 2 == 0)    // Keep even numbers
       .map(n -> n * n)            // Square them
       .forEach(System.out::println);
```

**Output:**

```
4
16
```

##### flatMap vs map

* **map** → 1-to-1 transformation
* **flatMap** → 1-to-many transformation (flattens collections)

Example:

```java
List<List<String>> listOfLists = Arrays.asList(
    Arrays.asList("A", "B"),
    Arrays.asList("C", "D")
);

listOfLists.stream()
           .flatMap(List::stream)
           .forEach(System.out::println);
```

**Output:**

```
A
B
C
D
```

---

#### 3.6 Terminal Operations

Terminal operations **trigger execution** and produce a result.

| Operation       | Purpose                                 | Example                         |
| --------------- | --------------------------------------- | ------------------------------- |
| **collect()**   | Gather results into a list, set, or map | `.collect(Collectors.toList())` |
| **forEach()**   | Perform an action on each element       | `.forEach(System.out::println)` |
| **count()**     | Count elements                          | `.count()`                      |
| **reduce()**    | Combine elements into a single result   | `.reduce(0, (a, b) -> a + b)`   |
| **min()/max()** | Find smallest/largest element           | `.min(Integer::compareTo)`      |

##### Example: reduce

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

int sum = numbers.stream()
                 .reduce(0, (a, b) -> a + b);

System.out.println(sum); // 15
```

Here:

* **0** → Initial value
* `(a, b) -> a + b` → Combiner function

#### 3.7 Collectors

The `Collectors` class provides powerful ways to **collect stream results**.

| Collector Method                | Purpose                               |
| ------------------------------- | ------------------------------------- |
| `toList()`                      | Collect into a `List`                 |
| `toSet()`                       | Collect into a `Set`                  |
| `toMap(keyMapper, valueMapper)` | Collect into a `Map`                  |
| `groupingBy(classifier)`        | Group by a property                   |
| `partitioningBy(predicate)`     | Partition into two groups             |
| `joining()`                     | Join elements into a single string    |
| `mapping()`                     | Combine mapping with other collectors |
| `reducing()`                    | Custom reduce operation               |

##### Example: groupingBy

```java
import java.util.*;
import java.util.stream.*;

public class GroupingExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Alex", "Charlie");

        Map<Character, List<String>> grouped = names.stream()
            .collect(Collectors.groupingBy(name -> name.charAt(0)));

        System.out.println(grouped);
    }
}
```

**Output:**

```
{A=[Alice, Alex], B=[Bob], C=[Charlie]}
```

##### Example: partitioningBy

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

Map<Boolean, List<Integer>> partitioned = numbers.stream()
    .collect(Collectors.partitioningBy(n -> n % 2 == 0));

System.out.println(partitioned);
```

**Output:**

```
{false=[1, 3, 5], true=[2, 4, 6]}
```

#### 3.8 Parallel Streams

Streams make parallelization **super easy**:

```java
numbers.parallelStream()
       .map(n -> n * n)
       .forEach(System.out::println);
```

⚠ **Caution:**

* Parallel streams are **not always faster** — they add overhead.
* Best for **large datasets** with **CPU-bound operations**.
* Be careful with **shared mutable state** — can cause bugs.

#### 3.9 Performance Pitfalls

* Avoid repeatedly creating streams in loops.
* Use **filter early** to reduce data size before heavy operations.
* Avoid `parallelStream()` on small data or I/O heavy tasks.
* Remember: **Stream is single-use** — once closed, create a new one.

#### 3.10 Final Analogy

Think of a stream like a **water pipeline**:

* **Source:** The water tank (collection or data source)
* **Intermediate Operations:** Filters and purifiers (transforming the water)
* **Terminal Operation:** The final tap where you collect the clean water

#### 3.11 Summary Table

| **Stage**    | **Examples**                                    | **Executes Immediately?** |
| ------------ | ----------------------------------------------- | ------------------------- |
| Source       | `stream()`, `Arrays.stream()`                   | No                        |
| Intermediate | `filter()`, `map()`, `sorted()`, `limit()`      | No (Lazy)                 |
| Terminal     | `collect()`, `reduce()`, `forEach()`, `count()` | Yes                       |