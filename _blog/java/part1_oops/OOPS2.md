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

### 🏠 1. Local Classes – “A Class Inside a Method”

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

### 👻 2. Anonymous Classes – “Class Without a Name”

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

### 🔍 What Can Local and Anonymous Classes Access in Java?

When working with **local or anonymous classes**, a common question is:

> **"What variables can they access from their surrounding context?"**

Let’s break it down clearly — with examples and practical reasoning.

---

#### ✅ 1. **Instance Variables of the Enclosing Class**

Local/anonymous classes can access non-static fields of the enclosing class directly — even if they’re `private`.

```java
class Outer {
    private int value = 42;

    public void show() {
        Runnable r = new Runnable() {
            public void run() {
                System.out.println(value); // ✅ Accesses instance variable
            }
        };
        r.run();
    }
}
```

**Why it works:**
The inner class keeps an **implicit reference** to the outer class (`Outer.this`), so it can freely access its fields.

---

#### ✅ 2. **Static Variables of the Enclosing Class**

They can also access static fields of the outer class without issue.

```java
class Outer {
    private static String label = "Static Value";

    public void show() {
        Runnable r = () -> System.out.println(label); // ✅ Allowed
        r.run();
    }
}
```

**Why it works:**
Static fields belong to the class, not the instance — so they’re always accessible once the class is loaded.

---

### ✅ 3. **Final or Effectively Final Local Variables**

This is the most important (and often misunderstood) rule:

```java
void printMessage() {
    String message = "Hello"; // effectively final

    Runnable r = () -> System.out.println(message); // ✅ Allowed
    r.run();
}
```

If we try to **change `message` later**, it will no longer be "effectively final" and cause a compile-time error:

```java
message = "Hi"; // ❌ Now 'message' is no longer effectively final
```

**Why this restriction?**
Java captures the value at the time the inner class is created. It **copies** the value into a hidden field. If the variable kept changing afterward, this would lead to inconsistent or confusing behavior — so Java forbids it.

---

#### ✅ 4. **Final or Effectively Final Method Parameters**

Parameters behave just like local variables.

```java
void greet(String name) {
    Runnable r = () -> System.out.println("Hello, " + name); // ✅ OK
    r.run();
}
```

If we try to modify `name`, we'll break the "effectively final" rule and get a compile error.

#### 🧠 Summary Table

| Variable Type                        | Accessible? | Reason                                       |
| ------------------------------------ | ----------- | -------------------------------------------- |
| Instance variable of outer class     | ✅ Yes       | Lives in the heap; inner class has reference |
| Static variable of outer class       | ✅ Yes       | Always available via class                   |
| Final or effectively final local var | ✅ Yes       | Safely captured at compile time              |
| Non-final local var                  | ❌ No        | Risk of inconsistency                        |
| Final/effectively final parameter    | ✅ Yes       | Same rule as local vars                      |
| Reassigned parameter                 | ❌ No        | No longer effectively final                  |


🧩 **Quick Tip:**
If ever unsure why a variable isn't accessible in a lambda or anonymous class, we should ask ourself:

> *"Is this variable guaranteed to stay the same after it's used?"*
> If **yes**, it’s probably effectively final. If not — Java will block it.

---