---
layout: post
title: "Core Java Part 4 : Multithreading & Concurrency Introduction"
slug: "core-java-4-multithreading-concurrency"
date: 2025-10-31
author: Anubhav Srivastava
tags: [core java, multithreading, concurrency]
version: 1.0
---

* TOC
{:toc}

---

### 🚀 Introduction

Have you ever used an app that froze while loading something?
That’s what happens when a program does **everything one step at a time** — no multitasking.

Modern systems demand **speed, responsiveness, and parallel execution**, and that’s exactly what **multithreading** and **concurrency** enable.

---

### 🧩 1. What is a Thread?

A **thread** is simply a **path of execution** inside our program.

When we start a Java application, **one main thread** begins executing the `main()` method.
But we can create **more threads** to do other tasks simultaneously.

#### 🧠 Analogy

Imagine a **restaurant**:

* The restaurant (our program) can hire multiple **waiters (threads)**.
* Each waiter can handle one **customer (task)** independently.
* They all share the same **kitchen (memory)**.

This sharing makes things fast — but also dangerous if not managed properly (we’ll talk about that soon).

---

### ⚙️ 2. Creating Threads in Java

There are two main ways to create a thread in Java:

#### ✅ Method 1: Extending `Thread` class

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread is running: " + Thread.currentThread().getName());
    }
}

public class Example1 {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start(); // Start the thread (calls run() internally)
    }
}
```

🟢 Output:

```
Thread is running: Thread-0
```

---

#### ✅ Method 2: Implementing `Runnable` interface

This is **more flexible** and **recommended** for most cases.

```java
class MyTask implements Runnable {
    public void run() {
        System.out.println("Running task in: " + Thread.currentThread().getName());
    }
}

public class Example2 {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MyTask());
        t1.start();
    }
}
```

🟩 Why Runnable?
Because Java allows **multiple threads to share the same Runnable** and it’s easier to manage when using **thread pools** (discussed later).

---

### 🌀 3. Thread Lifecycle & State Transitions

Every thread in Java passes through well-defined states during its life.

| **State**                   | **Meaning**                         | **Trigger**                   |
| --------------------------- | ----------------------------------- | ----------------------------- |
| **NEW**                     | Thread created but not started      | `new Thread()`                |
| **RUNNABLE**                | Ready to run (waiting for CPU time) | `start()`                     |
| **RUNNING**                 | Actively executing                  | JVM Scheduler                 |
| **WAITING / TIMED_WAITING** | Waiting for another thread          | `wait()`, `join()`, `sleep()` |
| **TERMINATED**              | Finished execution                  | `run()` completed             |

---

#### 🔍 Example

```java
public class ThreadStates {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {}
        });

        System.out.println(t.getState()); // NEW
        t.start();
        System.out.println(t.getState()); // RUNNABLE
        Thread.sleep(100);
        System.out.println(t.getState()); // TIMED_WAITING
        t.join();
        System.out.println(t.getState()); // TERMINATED
    }
}
```

---

### 🪄 4. Thread Synchronization

When multiple threads share the same memory (like shared variables or objects), things can go wrong if they try to modify it **at the same time**.

This is called a **race condition**.

---

#### ❌ Example of Race Condition

```java
class Counter {
    int count = 0;

    public void increment() {
        count++;
    }
}

public class RaceConditionDemo {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) counter.increment();
        });
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) counter.increment();
        });

        t1.start();
        t2.start();
        t1.join();
        t2.join();

        System.out.println("Final Count: " + counter.count);
    }
}
```

🟠 Expected: `2000`
🔴 Actual (often): something less!

Why? Because both threads read and update the same variable at the same time.

---

#### ✅ Solution: `synchronized` Keyword

`synchronized` ensures that **only one thread can access a block or method at a time**.

```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}
```

Now, every increment happens **one at a time**, preserving correctness.

---

#### 💡 Under the Hood

When a thread enters a `synchronized` block, it **acquires a lock** on the object.
No other thread can enter *any synchronized block* on that object until the lock is released.

---

### 🔁 5. `wait()`, `notify()`, and `notifyAll()`

Sometimes, threads need to **communicate and coordinate**.

Example: Producer-Consumer problem.
One thread produces items, another consumes them.
The consumer must **wait** if there’s nothing to consume.

---

#### 🧠 Example: Producer–Consumer with `wait()` & `notify()`

```java
class SharedBuffer {
    private int data;
    private boolean hasData = false;

    public synchronized void produce(int value) throws InterruptedException {
        while (hasData) wait(); // wait until consumed
        data = value;
        hasData = true;
        System.out.println("Produced: " + value);
        notify(); // wake up consumer
    }

    public synchronized int consume() throws InterruptedException {
        while (!hasData) wait(); // wait until produced
        hasData = false;
        System.out.println("Consumed: " + data);
        notify(); // wake up producer
        return data;
    }
}

public class ProducerConsumerDemo {
    public static void main(String[] args) {
        SharedBuffer buffer = new SharedBuffer();

        Thread producer = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                try { buffer.produce(i); } catch (InterruptedException e) {}
            }
        });

        Thread consumer = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                try { buffer.consume(); } catch (InterruptedException e) {}
            }
        });

        producer.start();
        consumer.start();
    }
}
```

🧩 Output (interleaved):

```
Produced: 1
Consumed: 1
Produced: 2
Consumed: 2
...
```

---

### 🔒 6. ReentrantLock — A Modern Alternative to synchronized

Java’s `ReentrantLock` (from `java.util.concurrent.locks`) gives **more control** than `synchronized`.

We can:

* Try to acquire a lock without waiting forever (`tryLock()`)
* Interrupt waiting threads
* Use **multiple condition variables** (like advanced wait/notify)

---

#### Example

```java
import java.util.concurrent.locks.ReentrantLock;

class SafeCounter {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();

    public void increment() {
        lock.lock(); // acquire lock
        try {
            count++;
        } finally {
            lock.unlock(); // always release lock
        }
    }

    public int getCount() {
        return count;
    }
}
```

🧠 Tip:
Always use the `try-finally` pattern so locks are **released even if exceptions occur**.

---

### ⚙️ 7. Callable, Future & ExecutorService

Creating threads manually doesn’t scale for large systems.
Java provides a **thread pool framework** — `ExecutorService` — which **manages threads efficiently**.

---

#### ✅ Example: Using ExecutorService

```java
import java.util.concurrent.*;

public class ExecutorExample {
    public static void main(String[] args) throws Exception {
        ExecutorService service = Executors.newFixedThreadPool(3);

        // Submit a Runnable
        service.submit(() -> System.out.println("Runnable executed"));

        // Submit a Callable (returns a value)
        Future<Integer> future = service.submit(() -> {
            Thread.sleep(1000);
            return 42;
        });

        System.out.println("Result from Callable: " + future.get()); // waits if needed

        service.shutdown();
    }
}
```

Here:

* `ExecutorService` reuses threads instead of creating new ones each time.
* `Callable` is like `Runnable` but **returns a result**.
* `Future` lets us **retrieve** that result later.

---

### ⚠️ 8. Common Concurrency Problems

When working with threads, we’ll encounter these classic issues:

#### 🧱 Deadlock

> Two threads wait for each other’s lock — forever.

Example:

* Thread A locks Object1, waits for Object2.
* Thread B locks Object2, waits for Object1.

Result → Both **stuck** forever.

💡 **Avoidance tip:**
Always acquire locks in **a consistent order**.

#### 🔄 Livelock

> Threads keep responding to each other and **never make progress**.

Analogy: Two people politely trying to pass each other in a hallway —
“After you!” “No, after you!” — and they keep switching sides endlessly.

#### ⏳ Starvation

> One thread **never gets CPU time** because others hog the resource.

Fix: Use fair locking or thread priorities properly.

#### 🧍‍♂️ Thread Safety

A class is **thread-safe** if multiple threads can use it **without breaking behavior**.
We can achieve thread-safety using:

* Immutability
* Synchronization
* Concurrent Collections
* Atomic variables (`AtomicInteger`, `AtomicReference`, etc.)

---

### 🧭 9. Key Takeaways

| Concept                  | Meaning                        | Example                         |
| ------------------------ | ------------------------------ | ------------------------------- |
| **Thread**               | Independent unit of execution  | `Thread`, `Runnable`            |
| **Thread lifecycle**     | Creation → Run → Wait → Death  | `getState()`                    |
| **Synchronization**      | Protect shared data            | `synchronized`, `ReentrantLock` |
| **Thread communication** | Coordinate between threads     | `wait()`, `notify()`            |
| **ExecutorService**      | Thread pool management         | `submit()`, `Future`            |
| **Common issues**        | Deadlock, livelock, starvation | Use ordered locking & fairness  |

Multithreading is like hiring more workers for our app — it can **make it blazing fast**,
but without discipline, it can also **cause chaos**.

Start small. Experiment with:

* Creating threads using `Runnable`
* Protecting shared data with `synchronized`
* Using modern APIs like `ExecutorService`
* And finally, **observe how threads behave** using logs or debuggers.

---
