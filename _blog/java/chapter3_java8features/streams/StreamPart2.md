---
layout: post
title: "Core Java Chapter 3.2.2 - Java Stream API Guide Part 2 (Practical)"
slug: "core-java-3-2-2-stream-api-part-2-practical"
date: 2025-09-16
modified_date: 2025-09-16
author: Anubhav Srivastava
tags: [core java, java 8 features, stream api, data processing pipeline]
version: 1.0
---

* TOC
{:toc}

---

## 1. Stream Ops

### 1.1 Initialization (Sources)

| Method                                | Parameters          | Returns                   | When to Use                                                                 |
| ------------------------------------- | ------------------- | ------------------------- | --------------------------------------------------------------------------- |
| `collection.stream()`                 | —                   | `Stream<T>`               | Default choice for processing lists/sets in a single thread                 |
| `collection.parallelStream()`         | —                   | `Stream<T>`               | When working on large data and operations are independent (CPU-heavy tasks) |
| `Arrays.stream(array)`                | array               | `Stream<T>` / `IntStream` | When input is an array instead of collection                                |
| `Stream.of(values...)`                | varargs             | `Stream<T>`               | Quick test data or small fixed inputs                                       |
| `Stream.builder()`                    | —                   | Builder                   | When elements are added conditionally before creating stream                |
| `Stream.generate(Supplier)`           | `() -> T`           | `Stream<T>`               | Infinite stream (e.g., random values, dummy data)                           |
| `Stream.iterate(seed, fn)`            | seed, next fn       | `Stream<T>`               | Sequence generation (e.g., Fibonacci, counters)                             |
| `Stream.iterate(seed, condition, fn)` | seed, condition, fn | `Stream<T>`               | Controlled iteration (Java 9+)                                              |
| `IntStream.range(start, end)`         | start, end          | `IntStream`               | Loop replacement for numeric iteration                                      |
| `IntStream.rangeClosed(start, end)`   | start, end          | `IntStream`               | When end should be inclusive                                                |
| `Files.lines(path)`                   | path                | `Stream<String>`          | File processing line by line (memory efficient)                             |
| `optional.stream()`                   | —                   | `Stream<T>`               | When integrating Optional into stream pipelines                             |
| `Stream.empty()`                      | —                   | `Stream<T>`               | Safe default to avoid null checks                                           |

---

### 1.2 Intermediate Operations

👉 These are **lazy** and define *how data should be transformed*

#### 1.2.1 Filtering & Slicing

| Method                 | Parameters     | Returns     | When to Use                                                        |
| ---------------------- | -------------- | ----------- | ------------------------------------------------------------------ |
| `filter(Predicate)`    | `T -> boolean` | `Stream<T>` | When we need to **remove unwanted data** (most common first step) |
| `distinct()`           | —              | `Stream<T>` | When duplicate removal is needed (uses `equals()` internally)      |
| `limit(n)`             | n              | `Stream<T>` | When only **top N results** are needed (pagination, previews)      |
| `skip(n)`              | n              | `Stream<T>` | When implementing **offset/pagination**                            |
| `takeWhile(Predicate)` | condition      | `Stream<T>` | When data is ordered and we want to stop early                    |
| `dropWhile(Predicate)` | condition      | `Stream<T>` | Skip initial matching elements (ordered streams only)              |

#### 1.2.2 Transformation

| Method                 | Parameters       | Returns          | When to Use                                                                  |
| ---------------------- | ---------------- | ---------------- | ---------------------------------------------------------------------------- |
| `map(Function)`        | `T -> R`         | `Stream<R>`      | When converting objects (e.g., User → name, Order → price)                   |
| `mapToInt/Long/Double` | mapper           | primitive stream | When doing **numeric operations (sum, avg)** efficiently                     |
| `flatMap(Function)`    | `T -> Stream<R>` | `Stream<R>`      | When dealing with **nested structures** (List<List<T>>, object → collection) |
| `flatMapToInt...`      | mapper           | primitive stream | Flatten + numeric operations                                                 |

👉 **Key insight**

* `map` → one-to-one
* `flatMap` → one-to-many (flattening)

#### 1.2.3 Sorting

| Method               | Parameters | Returns     | When to Use                                |
| -------------------- | ---------- | ----------- | ------------------------------------------ |
| `sorted()`           | —          | `Stream<T>` | Natural ordering (numbers, strings)        |
| `sorted(Comparator)` | comparator | `Stream<T>` | Custom sorting (by field, multiple fields) |

#### 1.2.4 Debugging / Inspection

| Method           | Parameters  | Returns     | When to Use                                    |
| ---------------- | ----------- | ----------- | ---------------------------------------------- |
| `peek(Consumer)` | `T -> void` | `Stream<T>` | Debugging pipelines (never for business logic) |

---

### 1.3 Terminal Operations

👉 These **execute the pipeline and produce results**

#### 1.3.1 Collection (Most Common in Real Problems)

| Method               | Parameters | Returns   | When to Use                                  |
| -------------------- | ---------- | --------- | -------------------------------------------- |
| `toList()`           | —          | `List<T>` | Default way to collect results (modern Java) |
| `collect(Collector)` | collector  | `R`       | When we need **custom aggregation**         |

#### 1.3.2 Important Collectors (Very Important)

##### 1.3.2.1 toMap

| Method                          | Parameters     | Returns    | When to Use                         |
| ------------------------------- | -------------- | ---------- | ----------------------------------- |
| `toMap(keyMapper, valueMapper)` | `T->K`, `T->V` | `Map<K,V>` | Convert list → map                  |
| `toMap(..., mergeFn)`           | merge fn       | `Map<K,V>` | Handle duplicate keys               |
| `toMap(..., mergeFn, supplier)` | map supplier   | `Map<K,V>` | Use specific map (HashMap, TreeMap) |

👉 Use case:

* ID → object mapping
* lookup tables

##### 1.3.2.2 groupingBy

| Method                                         | Parameters      | Returns           | When to Use                      |
| ---------------------------------------------- | --------------- | ----------------- | -------------------------------- |
| `groupingBy(classifier)`                       | `T -> K`        | `Map<K, List<T>>` | Group data (e.g., users by city) |
| `groupingBy(classifier, downstream)`           | collector       | `Map<K, D>`       | Aggregate per group              |
| `groupingBy(classifier, supplier, downstream)` | map + collector | `Map<K, D>`       | Custom map type                  |

👉 Use case:

* grouping transactions by type
* grouping employees by department

##### 1.3.2.3 partitioningBy

| Method                                  | Parameters     | Returns                 | When to Use              |
| --------------------------------------- | -------------- | ----------------------- | ------------------------ |
| `partitioningBy(predicate)`             | `T -> boolean` | `Map<Boolean, List<T>>` | Split into two groups    |
| `partitioningBy(predicate, downstream)` | collector      | `Map<Boolean, D>`       | Aggregate each partition |

👉 Use case:

* pass vs fail
* active vs inactive

#### 1.3.3 Reduction

| Method                                    | Parameters | Returns       | When to Use                      |
| ----------------------------------------- | ---------- | ------------- | -------------------------------- |
| `reduce(identity, accumulator)`           | `(T,T)->T` | `T`           | Custom aggregation (sum, concat) |
| `reduce(accumulator)`                     | `(T,T)->T` | `Optional<T>` | When no default value            |
| `reduce(identity, accumulator, combiner)` | 3 args     | `U`           | Parallel streams                 |

#### 1.3.4 Matching / Finding (Short-Circuiting)

| Method                 | Parameters | Returns       | When to Use                              |
| ---------------------- | ---------- | ------------- | ---------------------------------------- |
| `findFirst()`          | —          | `Optional<T>` | First matching element (ordered streams) |
| `findAny()`            | —          | `Optional<T>` | Faster in parallel streams               |
| `anyMatch(Predicate)`  | condition  | `boolean`     | Check existence                          |
| `allMatch(Predicate)`  | condition  | `boolean`     | Validate all elements                    |
| `noneMatch(Predicate)` | condition  | `boolean`     | Ensure none match                        |

#### 1.3.5 Iteration

| Method              | Parameters  | Returns | When to Use                      |
| ------------------- | ----------- | ------- | -------------------------------- |
| `forEach(Consumer)` | `T -> void` | void    | Side effects (logging, printing) |
| `forEachOrdered()`  | consumer    | void    | Maintain order in parallel       |

---

#### 1.3.6 Numeric Operations

| Method            | Parameters | Returns        | When to Use       |
| ----------------- | ---------- | -------------- | ----------------- |
| `sum()`           | —          | number         | Total calculation |
| `min()` / `max()` | —          | Optional       | Find extremes     |
| `average()`       | —          | OptionalDouble | Average           |

#### 1.3.7 Counting

| Method    | Parameters | Returns | When to Use    |
| --------- | ---------- | ------- | -------------- |
| `count()` | —          | long    | Count elements |


## Golden Patterns (Memorize These)

```text
filter → map → collect      (most common)

filter → map → reduce       (aggregations)

flatMap → collect           (nested data)

groupingBy → collect        (grouping problems)

partitioningBy              (boolean split)
```

---

## 2. Stream Patterns (Cheat Sheet)

| Pattern               | Structure                 | When to Use            |
| --------------------- | ------------------------- | ---------------------- |
| Basic Transform       | `map → collect`           | Transform data         |
| Filter + Transform    | `filter → map → collect`  | Most common case       |
| Filtering Only        | `filter → collect`        | Remove unwanted data   |
| Aggregation           | `map → reduce` / `sum`    | Total, sum, concat     |
| Find Element          | `filter → findFirst`      | First matching element |
| Matching              | `anyMatch / allMatch`     | Condition checks       |
| Flattening            | `flatMap → collect`       | Nested collections     |
| Grouping              | `groupingBy`              | Categorize data        |
| Partitioning          | `partitioningBy`          | Split into 2 groups    |
| Sorting               | `sorted → collect`        | Order data             |
| Top-N / Pagination    | `sorted → limit / skip`   | Ranking / paging       |
| Distinct              | `distinct → collect`      | Remove duplicates      |
| Counting              | `filter → count`          | Count matching items   |
| Map Creation          | `collect(toMap)`          | Convert to lookup map  |
| Multi-level Grouping  | `groupingBy → groupingBy` | Nested grouping        |
| Aggregation per Group | `groupingBy → downstream` | Sum/avg per group      |

### 2.1 Practice Problems (Patterns) + Solutions

#### 1. Basic Transform

- Problem

Convert list of strings to uppercase.

- Solution

```java
List<String> result =
    names.stream()
         .map(String::toUpperCase)
         .toList();
```

####  2. Filter + Transform (MOST IMPORTANT)

- Problem

From a list of users, get names of active users.

- Solution

```java
List<String> result =
    users.stream()
         .filter(user -> user.isActive())
         .map(user -> user.getName())
         .toList();
```

####  3. Filtering Only

- Problem

Get all even numbers from a list.

- Solution

```java
List<Integer> result =
    numbers.stream()
           .filter(n -> n % 2 == 0)
           .toList();
```

#### 4. Aggregation (Sum)

- Problem

Find total salary of all employees.

- Solution

```java
double total =
    employees.stream()
             .map(Employee::getSalary)
             .reduce(0.0, Double::sum);
```

OR better:

```java
double total =
    employees.stream()
             .mapToDouble(Employee::getSalary)
             .sum();
```

#### 5. Find First Match

- Problem

Find first user older than 30.

- Solution

```java
Optional<User> user =
    users.stream()
         .filter(u -> u.getAge() > 30)
         .findFirst();
```

#### 6. Matching

- Problem

Check if any user is inactive.

- Solution

```java
boolean exists =
    users.stream()
         .anyMatch(u -> !u.isActive());
```

#### 7. Flattening (VERY IMPORTANT)

- Problem

We have `List<List<Integer>>`. Flatten into a single list.

- Solution

```java
List<Integer> result =
    list.stream()
        .flatMap(List::stream)
        .toList();
```

#### 8. Grouping

- Problem

Group employees by department.

- Solution

```java
Map<String, List<Employee>> result =
    employees.stream()
             .collect(Collectors.groupingBy(Employee::getDepartment));
```

#### 9. Partitioning

- Problem

Split numbers into even and odd.

- Solution

```java
Map<Boolean, List<Integer>> result =
    numbers.stream()
           .collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

#### 10. Sorting

- Problem

Sort users by age.

- Solution

```java
List<User> result =
    users.stream()
         .sorted(Comparator.comparing(User::getAge))
         .toList();
```

#### 11. Top-N (Important)

- Problem

Get top 3 highest salaries.

- Solution

```java
List<Employee> result =
    employees.stream()
             .sorted(Comparator.comparing(Employee::getSalary).reversed())
             .limit(3)
             .toList();
```

#### 12. Distinct

- Problem

Get unique elements.

- Solution

```java
List<Integer> result =
    numbers.stream()
           .distinct()
           .toList();
```

#### 13. Counting

- Problem

Count how many numbers are even.

- Solution

```java
long count =
    numbers.stream()
           .filter(n -> n % 2 == 0)
           .count();
```

#### 14. Map Creation

- Problem

Convert list of users into map (id → user).

- Solution

```java
Map<Integer, User> map =
    users.stream()
         .collect(Collectors.toMap(User::getId, u -> u));
```

#### 15. Multi-level Grouping

- Problem

Group employees by department, then by role.

- Solution

```java
Map<String, Map<String, List<Employee>>> result =
    employees.stream()
             .collect(Collectors.groupingBy(
                 Employee::getDepartment,
                 Collectors.groupingBy(Employee::getRole)
             ));
```

#### 16. Aggregation per Group (VERY IMPORTANT)

- Problem

Find total salary per department.

- Solution

```java
Map<String, Double> result =
    employees.stream()
             .collect(Collectors.groupingBy(
                 Employee::getDepartment,
                 Collectors.summingDouble(Employee::getSalary)
             ));
```

### Final Insight (Very Important)

Almost every problem maps to one of these:

```text
filter → map → collect
flatMap → collect
groupingBy → aggregation
sorted → limit
```

---

## 3. LeetCode Problems - Pattern Based

##### 🔹 3.1. Filter + Map Pattern (Most Important)

| Problem                               | Why it fits                    |
| ------------------------------------- | ------------------------------ |
| Two Sum                               | Filter + lookup transformation |
| Find All Numbers Disappeared in Array | Filtering missing elements     |
| Squares of a Sorted Array             | Map (square transform)         |
| Remove Duplicates from Sorted Array   | Filter logic                   |
| Move Zeroes                           | Filter + reconstruction        |

##### 🔹 3.2. Aggregation (Sum / Reduce)

| Problem                         | Why it fits                      |
| ------------------------------- | -------------------------------- |
| Running Sum of 1d Array         | Prefix sum (map/reduce thinking) |
| Maximum Subarray                | Reduction logic                  |
| Best Time to Buy and Sell Stock | Aggregation logic                |
| Richest Customer Wealth         | Sum inside map                   |
| Find Pivot Index                | Left-right sum comparison        |

##### 🔹 3.3. Finding / Matching (Short-Circuiting)

| Problem                         | Why it fits            |
| ------------------------------- | ---------------------- |
| Find First Palindromic String   | `findFirst`            |
| Check If N and Its Double Exist | `anyMatch`             |
| Valid Anagram                   | Matching/counting      |
| Contains Duplicate              | `anyMatch` / set logic |

##### 🔹 3.4. Flattening (flatMap — VERY IMPORTANT)

| Problem                      | Why it fits                 |
| ---------------------------- | --------------------------- |
| Flatten Nested List Iterator | Classic flatMap             |
| Combination Sum              | Tree → flatten thinking     |
| Subsets                      | Nested structure flattening |

##### 🔹 3.5. Grouping (groupingBy — VERY IMPORTANT)

| Problem                       | Why it fits      |
| ----------------------------- | ---------------- |
| Group Anagrams                | Classic grouping |
| Top K Frequent Elements       | Group + count    |
| Sort Characters By Frequency  | Group + sort     |
| Find Duplicate File in System | Group by content |

##### 🔹 3.6. Sorting + Top K

| Problem                      | Why it fits     |
| ---------------------------- | --------------- |
| Kth Largest Element in Array | Sorting + limit |
| Top K Frequent Words         | Sort + limit    |
| Merge Intervals              | Sort + process  |
| Meeting Rooms                | Sort + compare  |

##### 🔹 3.7. Map Creation (toMap)

| Problem                                        | Why it fits           |
| ---------------------------------------------- | --------------------- |
| Two Sum                                        | Map building          |
| Isomorphic Strings                             | Mapping relationships |
| Word Pattern                                   | Map consistency       |
| Longest Substring Without Repeating Characters | Map-based tracking    |

##### 🔹 3.8. Counting

| Problem                | Why it fits      |
| ---------------------- | ---------------- |
| Majority Element       | Counting         |
| First Unique Character | Frequency count  |
| Valid Anagram          | Count comparison |

##### 🔹 3.9. Partitioning (Boolean Split)

| Problem              | Why it fits          |
| -------------------- | -------------------- |
| Sort Array By Parity | Split even/odd       |
| Partition Labels     | Logical partitioning |

---

