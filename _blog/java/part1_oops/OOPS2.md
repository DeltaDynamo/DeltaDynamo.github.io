---
layout: post
title: "Core Java Part 1 : Object Oriented Principles (Local & Anonymous Classes)"
slug: "core-java-1-oops-local-anonymous-classes"
date: 2025-07-21
author: Anubhav Srivastava
tags: [core java, object oriented programming]
---

## 🧠 Local & Anonymous Classes in Java

In Java, classes can be declared not just at the top-level, but also **inside methods**. These are called **local** and **anonymous** classes. Both are types of **inner classes**, and they are typically used when the class is:

* Used only within a specific scope
* Needed for a **short-lived**, specialized behavior

Let’s break them down intuitively.

---

### 🏠 Local Classes – “A Class Inside a Method”

#### 🔍 What is a Local Class?

A **Local Class** is a class defined **inside a method**, like a helper that exists only **within that method's body**.

#### 🧠 Intuition

> Think of a chef (method) who defines a **recipe (class)** only for this evening’s dish. Tomorrow, it’s gone. No need to make it global.

#### 📘 Example:

```java
public class Hotel {
    public void serveDish() {
        class Recipe {
            void prepare() {
                System.out.println("Cooking secret pasta recipe...");
            }
        }

        Recipe r = new Recipe();
        r.prepare();
    }
}
```

```java
Hotel h = new Hotel();
h.serveDish();  // Output: Cooking secret pasta recipe...
```

#### ✅ Key Points:

* Can **access final or effectively final variables** from the enclosing method.
* Has a **name**.
* Exists **only inside the method**—cannot be used elsewhere.
* Supports **constructors**, fields, and methods.

---

### 🧪 Example with Local Variables:

```java
public class Printer {
    public void printMessage(String msg) {
        int copies = 3; // Effectively final

        class MessagePrinter {
            void print() {
                for (int i = 0; i < copies; i++) {
                    System.out.println(msg);
                }
            }
        }

        MessagePrinter printer = new MessagePrinter();
        printer.print();
    }
}
```

---

### 👻 Anonymous Classes – “Class Without a Name”

#### 🔍 What is an Anonymous Class?

An **Anonymous Class** is a **one-time-use subclass or implementation**, created **on the fly**, **without naming it**.

#### 🧠 Intuition

> We need a **temporary worker** to do a specific task today We don't name them, we just say, “Hey, You! Do this now.”

They’re often used to **implement interfaces or abstract classes on the spot**—especially useful with GUI callbacks or threads.

---

#### 📘 Example: Anonymous Class with Interface

```java
interface Greetable {
    void greet();
}

public class Greeter {
    public void greetSomeone() {
        Greetable g = new Greetable() {
            public void greet() {
                System.out.println("Hello from an anonymous class!");
            }
        };

        g.greet();
    }
}
```

---

#### 📘 Example: Anonymous Thread

```java
public class Task {
    public static void main(String[] args) {
        Thread t = new Thread() {
            public void run() {
                System.out.println("Thread running anonymously");
            }
        };
        t.start();
    }
}
```

Or with `Runnable` interface:

```java
Thread t = new Thread(new Runnable() {
    public void run() {
        System.out.println("Runnable running anonymously");
    }
});
t.start();
```

---

### 🔍 Local vs Anonymous Class – Key Differences

| Feature                  | Local Class                | Anonymous Class                         |
| ------------------------ | -------------------------- | --------------------------------------- |
| Has a name?              | ✅ Yes                      | ❌ No (hence "anonymous")                |
| Can define constructors? | ✅ Yes                      | ❌ No (uses initializer block)           |
| Can extend class?        | ✅ Yes                      | ✅ Yes (but only one)                    |
| Can implement interface? | ✅ Yes                      | ✅ Yes (but only one)                    |
| Typical usage            | Helper class in method     | One-off implementation (e.g. callbacks) |
| Reusability              | 🟡 Limited (within method) | ❌ None – single-use                     |


### 💡 Real-Life Usage Examples

#### ✅ GUI Programming (Pre-Lambda Java)

```java
button.addActionListener(new ActionListener() {
    public void actionPerformed(ActionEvent e) {
        System.out.println("Button clicked!");
    }
});
```

#### ✅ Sorting with Comparator

```java
List<String> list = Arrays.asList("dog", "cat", "elephant");

Collections.sort(list, new Comparator<String>() {
    public int compare(String s1, String s2) {
        return s1.length() - s2.length();
    }
});
```

#### 🧠 How to use?

* Use **Local Classes** when you want a helper class scoped to a method, and you **may need to reuse** it **within that method**.
* Use **Anonymous Classes** when you need a **quick, one-time-use class**, especially for **implementing interfaces or overriding methods inline**.

---
