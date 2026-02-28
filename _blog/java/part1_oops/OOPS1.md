---
layout: post
title: "Core Java Part 1 : Object Oriented Principles (Four Pillars & Object Equality)"
slug: "core-java-1-oops-four-pillars"
date: 2025-07-20
author: Anubhav Srivastava
tags: [core java, object oriented programming]
version: 1.1
---

* TOC
{:toc}

---

## 🧠 Object Oriented Programming Basics : Polymorphism, Encapsulation, Inheritance & Abstraction

Object-Oriented Programming (OOP) is not just a paradigm - it’s a mindset. To write clean, reusable, and scalable code in Java, we must **internalize** the four key pillars of OOP:

---

## 1. 🔁 Polymorphism – “Same Action, Different Behavior”

### 🧠 1.1 Intuition

> Imagine we press a “Play” button. On a music player, it plays music. On a video player, it plays a movie. On a game, it starts the level.
> One button, many behaviors.
> This is **Polymorphism** - from the Greek words "poly" (many) and "morph" (forms).

In Java, **polymorphism allows us to treat different types of objects in the same way**, while letting them behave differently.

### ✅ 1.2 Two Types:

* **Compile-time (Static) Polymorphism**: Method Overloading
* **Runtime (Dynamic) Polymorphism**: Method Overriding

---

#### 📘 Example 1: Method Overloading (Static Polymorphism)

```java
class Calculator {
    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }

    int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

* All methods are named `add` but differ in **parameters**.
* At compile time, Java picks the correct version → **static polymorphism**.
* While the return type can be different, it is not a factor in distinguishing overloaded methods; only the parameter list matters.

---

#### 📘 Example 2: Method Overriding (Dynamic Polymorphism)

```java
class Animal {
    void speak() {
        System.out.println("Animal speaks");
    }
}

class Dog extends Animal {
    @Override
    void speak() {
        System.out.println("Dog barks");
    }
}

class Cat extends Animal {
    @Override
    void speak() {
        System.out.println("Cat meows");
    }
}

public class Test {
    public static void main(String[] args) {
        Animal a1 = new Dog();
        Animal a2 = new Cat();

        a1.speak();  // Dog barks
        a2.speak();  // Cat meows
    }
}
```

> The reference is of type `Animal`, but the actual object is `Dog` or `Cat`.
> At **runtime**, the JVM decides which `speak()` to call.

---

##### How Does Method Overriding Work Internally in Java (Using the Memory Model)?

When a method is overridden in Java, the decision of which method to execute is made at runtime, not compile time. This is called dynamic dispatch.
Let’s understand it using the JVM memory model.

1️⃣ What Gets Stored in Memory?

In the JVM:
>Heap → Stores objects

>Stack → Stores method calls and references

>Method Area (Metaspace) → Stores class metadata (method definitions, bytecode)

Suppose:

``` Java
class Parent {
    void show() { System.out.println("Parent"); }
}

class Child extends Parent {
    void show() { System.out.println("Child"); }
}
```

And we write:

```Java
Parent obj = new Child();
obj.show();
```

2️⃣ What Happens Step by Step?

> new Child() → A Child object is created in the heap.

> Parent obj → The reference variable obj is stored in the stack, but it only holds a reference to the heap object.

Important part:
The actual object in heap is Child, not Parent.

3️⃣ How Method Resolution Happens

At compile time:
Compiler checks: Does Parent have a show() method?
Yes → Compilation succeeds.

At runtime:
- JVM looks at the actual object in heap
- The object’s internal structure contains a pointer to its class metadata.
- The JVM uses a mechanism similar to a virtual method table (vtable) to resolve overridden methods.

Since the object is of type Child, it calls Child.show().
This lookup is called dynamic method dispatch.

4️⃣ Why It Works This Way
- Because Java supports polymorphism.

- The reference type (Parent) ensures:
- What methods are accessible (compile-time safety)

The object type (Child) decides: Which implementation to run (runtime flexibility)

---

## 2. 🔒 Encapsulation – “Protect What Matters”

### 🧠 Intuition

> Think of a **coffee machine**. We push a button to get coffee, but the internal wiring, boiling process, pressure systems - all are hidden.
> We don’t need to know how it works to use it. That’s **encapsulation**.

In Java, **encapsulation is about hiding internal data and exposing only what’s necessary while providing controlled access to the internal state of an object** using:

* `private` variables
* `public` getters/setters

---

#### 📘 Example:

```java
class BankAccount {
    private double balance;  // Hidden from outside world

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }

    public void withdraw(double amount) {
        if (amount <= balance) balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

> Direct access to `balance` is **restricted**, preventing accidental misuse.
> Only allowed ways are `deposit`, `withdraw`, and `getBalance`.

---

## 3. 🧬 Inheritance – “Reuse & Extend Behavior”

### 🧠 3.1 Intuition

> We inherit traits from our parents - like eye color or height.
> Similarly, a Java class can **inherit fields and methods from another**.

Inheritance enables:

* Code reuse
* Logical hierarchy (IS-A relationship)

---

#### 📘 Example:

```java
class Vehicle {
    void start() {
        System.out.println("Vehicle starts");
    }
}

class Car extends Vehicle {
    void honk() {
        System.out.println("Car honks");
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Car c = new Car();
        c.start(); // Inherited from Vehicle
        c.honk();  // Defined in Car
    }
}
```

#### 🧩 IS-A Relationship:

`Car IS-A Vehicle` → hence, Car inherits Vehicle’s functionality.

---

## 4. 🧽 Abstraction – “Focus on What, Hide the How”

### 🧠 Intuition

> When we drive a car, we use the steering wheel and we don’t care **how** the engine works internally.
> We only interact with the **interface**, not the implementation.

Abstraction means:

* Hiding implementation details
* Showing only relevant operations


#### ✅ Achieved in Java using:

* **Abstract classes** (Partial Abstraction) 
* **Interfaces** (100% Abstraction) 


#### 📘 Abstract Class Example:

```java
abstract class Shape {
    abstract double area();  // What to do

    void describe() {
        System.out.println("A shape has dimensions.");
    }
}

class Circle extends Shape {
    double radius;

    Circle(double r) {
        this.radius = r;
    }

    @Override
    double area() {
        return Math.PI * radius * radius;  // How to do
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Shape s = new Circle(5);
        s.describe();             // A shape has dimensions.
        System.out.println(s.area()); // 78.539...
    }
}
```

---

#### 📘 Interface Example:

```java
interface Flyable {
    void fly(); // Abstract by default
}

class Bird implements Flyable {
    public void fly() {
        System.out.println("Bird flaps wings to fly.");
    }
}

class Plane implements Flyable {
    public void fly() {
        System.out.println("Plane uses engines to fly.");
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Flyable f1 = new Bird();
        Flyable f2 = new Plane();
        f1.fly();  // Bird flaps wings
        f2.fly();  // Plane uses engines
    }
}
```

> The caller doesn’t care how flying is done - it just knows each object can `fly()`.

---

### 🔄 Summary Table

| Concept       | Real-Life Analogy                    | Java Mechanism                  | Benefit                    |
| ------------- | ------------------------------------ | ------------------------------- | -------------------------- |
| Polymorphism  | Play button on different devices     | Method overloading/overriding   | Flexibility                |
| Encapsulation | Coffee machine                       | private fields, getters/setters | Security & Maintainability |
| Inheritance   | Children inherit from parents        | `extends` keyword               | Reusability                |
| Abstraction   | Driving a car without knowing engine | abstract classes, interfaces    | Simplicity & Scalability   |

---

## 🧩 5. Interface vs Abstract Class in Java

### 🧠 Intuition

> An **interface** is like a **contract**: “If we implement it, we must do X, Y, and Z.”
> An **abstract class** is like a **partial blueprint**: “It provides some built-in behavior, we complete the rest.”

---

### 📐 Interface

#### ✅ Use When:

* We want to define **pure behavior**
* Multiple unrelated classes share the **same capability**
* We don’t need shared state or base implementation

#### 📘 Example:

```java
interface Flyable {
    void fly();
}

class Bird implements Flyable {
    public void fly() {
        System.out.println("Bird flaps wings");
    }
}

class Plane implements Flyable {
    public void fly() {
        System.out.println("Plane uses jet engines");
    }
}
```

* No state or fields
* Multiple classes can implement it
* No hierarchy enforced

---

### 🧱 Abstract Class

#### ✅ Use When:

* We want to provide **partial implementation**
* We want to share **common fields or logic**
* There’s a strong **IS-A** relationship

#### 📘 Example:

```java
abstract class Animal {
    String name;

    Animal(String name) {
        this.name = name;
    }

    void sleep() {
        System.out.println(name + " is sleeping");
    }

    abstract void makeSound(); // Must be implemented
}

class Dog extends Animal {
    Dog(String name) {
        super(name);
    }

    void makeSound() {
        System.out.println(name + " barks");
    }
}
```

* Abstract class can have:

  * Fields
  * Constructors
  * Concrete and abstract methods

---

### 🤔 Interface vs Abstract Class:

| Feature               | Interface                              | Abstract Class              |
| --------------------- | -------------------------------------- | --------------------------- |
| Inheritance           | Multiple interfaces allowed            | Only one superclass         |
| Fields                | Constants only (`public static final`) | Instance variables allowed  |
| Constructors          | ❌ Not allowed                          | ✅ Allowed                   |
| Method Implementation | Default (Java 8+), but no state        | Full/partial implementation |
| When to Use           | Capability (“can do”)                  | Blueprint with behavior     |
| Example               | Flyable, Runnable                      | Animal, Shape               |


### 🎯 When to Use Interface vs Abstract Class?

| Scenario                                         | Choose    |
| ------------------------------------------------ | --------- |
| We want multiple inheritance of behavior        | Interface |
| We want to share state/data across subclasses   | Abstract  |
| Our class hierarchy is tightly related          | Abstract  |
| We just want to enforce behavior (API contract) | Interface |

---

## ⚔️ 6. Composition vs Inheritance in Java: The Great Design Battle

### 🧠 Intuition

> Inheritance says: “I AM a type of X.”
> Composition says: “I HAVE a X.”

Imagine a **Car**:

* Inheritance: `Car extends Vehicle` → a car *is* a vehicle.
* Composition: `Car has an Engine` → a car *has* an engine.

---

### 🧬 Inheritance

#### ✅ When to Use:

* There's a clear **IS-A** relationship.
* We want to **reuse and override** behavior.
* We want polymorphism between parent and child.

#### 📘 Example:

```java
class Vehicle {
    void start() {
        System.out.println("Vehicle starting");
    }
}

class Car extends Vehicle {
    void honk() {
        System.out.println("Car honking");
    }
}
```

```java
Car c = new Car();
c.start();  // Inherited
c.honk();   // Specific to Car
```

#### ⚠️ Pitfalls:

* **Tight coupling**: Subclass depends on superclass structure.
* **Breaks with change**: A small change in parent might affect all children.
* Inherits **everything**, even what we don’t need.

---

### 🧱 Composition

#### ✅ When to Use:

* We need **flexibility** and **loose coupling**.
* There is a **HAS-A** relationship.
* We want to **change behavior at runtime** or **prefer delegation**.

#### 📘 Example:

```java
interface IEngine {
    void startEngine();
}

class Engine implements IEngine {
    void startEngine() {
        System.out.println("Engine starting...");
    }
}

class Car {
    private IEngine engine = new Engine(); // HAS-A

    void start() {
        engine.startEngine(); // Delegation
    }
}
```

```java
Car car = new Car();
car.start(); // Engine starting...
```

#### ✅ Benefits:

* More flexible and testable
* We can compose behavior from multiple classes
* Encourages code reuse **without tight hierarchy**

---

### 🤔 When to Use What?

| Criteria                | Inheritance                | Composition               |
| ----------------------- | -------------------------- | ------------------------- |
| Relationship            | IS-A                       | HAS-A                     |
| Coupling                | Tight                      | Loose                     |
| Reusability             | Reuse with constraints     | Reuse with freedom        |
| Flexibility             | Less flexible              | Very flexible             |
| Runtime Behavior Change | Hard                       | Easy                      |
| Preferred in Design     | Rarely (favor composition) | ✅ Preferred in modern OOP |

> ☑️ **Rule of Thumb**:
> "Favor composition over inheritance" - Effective Java (Joshua Bloch)

---

## 🔁 7. Object Equality in Java : equals() and hashCode()

When working with Java objects - especially in collections like `HashMap`, `HashSet`, or `Hashtable` - the way Java compares objects under the hood heavily depends on `equals()` and `hashCode()`.

If these two methods aren’t properly understood or implemented, we may face strange bugs like:

* Duplicate values in sets
* Missing values in maps
* Unexpected behavior in object comparisons

Let’s decode it step by step. 🧠🔍

### 🧱 What is `equals()`?

It checks **logical equality** between two objects (i.e., whether two objects are *meaningfully* equal, not necessarily the same memory location).

```java
String a = new String("Hello");
String b = new String("Hello");

System.out.println(a == b);       // false (different memory)
System.out.println(a.equals(b));  // true (same content)
```

So:

* `==` → checks **reference equality**
* `equals()` → checks **content/logical equality**

---

### 🧮 What is `hashCode()`?

Returns an `int` hash value of the object. Used in **hash-based collections** (`HashMap`, `HashSet`, etc.) to bucketize and locate elements quickly.

Java’s contract:

> “If two objects are equal (`equals()` returns `true`), then their hashCodes must be the same.”

BUT:

> “If two objects have the same `hashCode()`, they *might not* be equal.”

### 🤝 The Contract Between `equals()` and `hashCode()`

| Requirement                       | Why it Matters                                   |
| --------------------------------- | ------------------------------------------------ |
| If `a.equals(b)` is `true`        | Then `a.hashCode() == b.hashCode()`              |
| If `a.hashCode() == b.hashCode()` | `a.equals(b)` *may or may not* be true           |
| If `a.equals(b)` is `false`       | `a.hashCode()` *can still be equal or different* |

---

### 💥 What Happens if We Violate This Contract?

#### 🚫 Example:

```java
class User {
    String name;
    User(String name) {
        this.name = name;
    }
}

HashSet<User> set = new HashSet<>();
set.add(new User("Alice"));
System.out.println(set.contains(new User("Alice"))); // false!
```

#### ❗Why false?

Because `equals()` and `hashCode()` are not overridden → they use default `Object` methods (which compare by reference), so logically equal objects are not treated as equal by the Set.

---

### ✅ Correct Way to Override `equals()` and `hashCode()`

#### 📘 Example:

```java
class User {
    String name;

    User(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;

        User user = (User) obj;
        return name.equals(user.name);
    }

    @Override
    public int hashCode() {
        return name.hashCode(); // use Objects.hash(name) for safety
    }
}
```

Now:

```java
User u1 = new User("Alice");
User u2 = new User("Alice");

System.out.println(u1.equals(u2)); // true
System.out.println(u1.hashCode() == u2.hashCode()); // true
```

Now it works properly in `HashSet`, `HashMap`, etc.

### ⚙️ How hashCode() Works in Collections

In a `HashMap<K, V>`:

1. `hashCode()` is used to find the **bucket**.
2. `equals()` is used to find the **exact key** in the bucket.

So both are required for correct lookup!

---

### 🛡️ Best Practices

✅ Always override both `equals()` and `hashCode()` together
✅ Use `@Override` to catch mistakes
✅ Use `Objects.equals()` and `Objects.hash()` (Java 7+)

#### 🧼 Cleaner version:

```java
import java.util.Objects;

class User {
    String name;

    User(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof User)) return false;
        User user = (User) o;
        return Objects.equals(name, user.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name);
    }
}
```

---

#### 🚨 Pro Tip: Don’t Include Mutable Fields in hashCode()

Imagine this:

```java
User user = new User("Alice");
HashSet<User> set = new HashSet<>();
set.add(user);

user.name = "Bob"; // modifies the object

System.out.println(set.contains(user)); // ❌ Might now return false!
```

Because `hashCode()` has changed, and the set can't find the object anymore.

---

#### 🔍 Summary Table for equals() & hashCode()

| Method         | Purpose                         | When Used                            |
| -------------- | ------------------------------- | ------------------------------------ |
| `equals()`     | Logical equality                | `list.contains()`, `map.get()`, etc. |
| `hashCode()`   | Bucketing in hash-based structs | `HashMap`, `HashSet`, etc.           |
| Override both? | ✅ Always if using collections   | Prevent lookup bugs                  |

---

> **Next Post in this series :** [Core Java Part 1 : Object Oriented Principles (Local & Anonymous Classes)](https://deltadynamo.github.io/blog/core-java-1-oops-local-anonymous-classes/)