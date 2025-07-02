---
layout: post
title: "S.O.L.I.D Principles"
slug: "solid-principles"
date: 2025-07-02
author: Anubhav Srivastava
tags: [software engineering, design pattern]
---

## 🧠 What is SOLID?

> **S.O.L.I.D** is an acronym for:

* **S**: Single Responsibility Principle
* **O**: Open/Closed Principle
* **L**: Liskov Substitution Principle
* **I**: Interface Segregation Principle
* **D**: Dependency Inversion Principle

These principles were introduced by Robert C. Martin (Uncle Bob) and are foundational to good software design.

---

### 🔎 Let’s go one-by-one with simple intuition, analogy, and examples.

---

## 🧱 1. **Single Responsibility Principle (SRP)**

> A class should have **only one reason to change**.

### 🎯 Real-world analogy:

* A **chef** cooks food
* A **waiter** serves customers
  Don't mix responsibilities.

### 💻 Java Example — ❌ Violation:

```java
class Report {
    String text;

    void print() {
        // print logic
    }

    void saveToFile() {
        // file I/O logic
    }
}
```

This class does **two things**: manage report data and handle I/O.

### ✅ Refactored:

```java
class Report {
    String text;
    // Only holds data and formatting
}

class ReportPrinter {
    void print(Report r) { /* ... */ }
}

class ReportSaver {
    void saveToFile(Report r) { /* ... */ }
}
```

---

## 🧱 2. **Open/Closed Principle (OCP)**

> Software entities should be **open for extension**, but **closed for modification**.

### 🎯 Real-world analogy:

We can **plug in** new USB devices without **rewriting our motherboard**.

### 💻 Violation:

```java
class PaymentProcessor {
    void pay(String type) {
        if (type.equals("credit")) { /* ... */ }
        else if (type.equals("upi")) { /* ... */ }
    }
}
```

* Adding new payment methods → modify existing class = ❌

### ✅ Refactored using Polymorphism:

```java
interface PaymentMethod {
    void pay();
}

class CreditCardPayment implements PaymentMethod {
    public void pay() { /* ... */ }
}

class UpiPayment implements PaymentMethod {
    public void pay() { /* ... */ }
}

class PaymentProcessor {
    void pay(PaymentMethod method) {
        method.pay();  // open for extension, closed for modification
    }
}
```

---

## 🧱 3. **Liskov Substitution Principle (LSP)**

> Subtypes must be substitutable for their base types **without breaking behavior**.

### 🎯 Real-world analogy:

If every **electric car** is a kind of **car**, it should still drive, stop, turn like any car.

### 💻 Violation:

```java
class Bird {
    void fly() { }
}

class Ostrich extends Bird {
    void fly() { throw new UnsupportedOperationException(); }
}
```

Ostrich can't fly, so substituting it breaks the logic.

### ✅ Fix:

Don’t force all birds to fly — separate the concept.

```java
interface Bird { void eat(); }

interface FlyingBird extends Bird {
    void fly();
}
```

---

## 🧱 4. **Interface Segregation Principle (ISP)**

> Clients should not be forced to depend on interfaces they don’t use.

### 🎯 Real-world analogy:

Don’t give a **printer** the controls of a **3D scanner** if it doesn’t need it.

### 💻 Violation:

```java
interface Machine {
    void print();
    void scan();
    void fax();
}

class SimplePrinter implements Machine {
    public void print() { }
    public void scan() { /* not supported */ }
    public void fax() { /* not supported */ }
}
```

### ✅ Fix:

```java
interface Printer { void print(); }
interface Scanner { void scan(); }

class SimplePrinter implements Printer { ... }
```

Split big interfaces into **role-specific ones**.

---

## 🧱 5. **Dependency Inversion Principle (DIP)**

> High-level modules should not depend on low-level modules — both should depend on **abstractions**.

### 🎯 Real-world analogy:

We don’t directly plug the toaster into the power plant — rather go through a **standard plug interface**.

### 💻 Violation:

```java
class MySQLDatabase {
    void connect() { }
}

class UserService {
    MySQLDatabase db = new MySQLDatabase();  // tight coupling ❌
}
```

### ✅ Fix using interface:

```java
interface Database {
    void connect();
}

class MySQLDatabase implements Database {
    public void connect() { }
}

class UserService {
    Database db;

    UserService(Database db) {
        this.db = db;
    }
}
```

Now we can inject any DB (`PostgreSQL`, `MockDB`, etc.)

---

## 🧾 SOLID Summary Table

| Principle | What it Means                      | Helps Us With          |
| --------- | ---------------------------------- | ----------------------- |
| SRP       | One job per class                  | Maintainability         |
| OCP       | Extend without modifying           | Flexibility             |
| LSP       | Subclass should behave like parent | Reliability             |
| ISP       | Small, focused interfaces          | Simplicity              |
| DIP       | Depend on interfaces, not concrete | Testability, decoupling |

---
