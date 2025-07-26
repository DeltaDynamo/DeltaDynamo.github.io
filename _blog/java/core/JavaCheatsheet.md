---
layout: post
title: "Java Developer Cheatsheet : Key Terminologies & Concepts"
slug: "core-java-cheatsheet"
date: 2025-07-25
author: Anubhav Srivastava
tags: [core java, terminologies]
---

## Key Java Terms and Concepts

> **Official Docs** : [Oracle Docs for Java 8](https://docs.oracle.com/javase/tutorial/java/index.html)

---

#### ✅ 1. [Interface](https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html)

> An `interface` in Java defines a **contract** — a set of method signatures — that implementing classes must follow. Unlike abstract classes, interfaces do **not have instance fields or constructors**, and they are used to achieve **100% abstraction** (in older Java) and **multiple inheritance of type**.
>
> From **Java 8**, interfaces can also include **default** and **static** methods with implementations.
> From **Java 9**, they can include **private methods**.

---

**Syntax & Example**:

```java
interface Vehicle {
    void drive(); // abstract method
}

class Car implements Vehicle {
    @Override
    public void drive() {
        System.out.println("Driving a car...");
    }
}
```

**Java 8+ features**:

```java
interface Calculator {
    default int add(int a, int b) {
        return a + b;
    }

    static int multiply(int a, int b) {
        return a * b;
    }

    private void log() {
        System.out.println("Private helper");
    }
}
```

**Usage & Caveats**:

* Use to **enforce contracts** across unrelated classes (e.g., `Car`, `Boat`, `Drone` can all implement `Vehicle`).
* Useful when we want to **separate what a class should do** from **how it does it**.
* Common in **API design**, **plug-in systems**, and **event handling**.
* Interfaces enable **multiple inheritance of behavior** (Java classes can implement multiple interfaces).
* All methods are `public abstract` by default unless marked `default`, `static`, or `private`.
* A class must implement **all abstract methods** of the interface **if it's a concrete class**.
* **Cannot create objects** from interfaces.
* Interfaces **cannot have constructors** or instance fields (can have static `final` constants).

**Since**: Java 1.0

**Memory/Performance**:

* No memory overhead unless methods are used.
* Default methods can be overridden, static methods cannot.

---

#### ✅ 2. [Abstract Class and Abstract Method](https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html)

> An abstract class in Java is a class that cannot be instantiated directly. It acts as a blueprint or foundation for other classes to build upon. We should think of it as an incomplete class meant to be extended by subclasses that will provide the full implementation.
>
> Abstract classes are used when we want to define some common behavior (shared logic) while leaving other behavior to be customized by individual subclasses.
>
> Within an abstract class, we can declare abstract methods — these are method declarations without any body. In other words, they only specify the method signature, and it’s the responsibility of the *first concrete (non-abstract)* subclasses to implement the actual method logic.

**Syntax & Example**:

```java
abstract class Animal {
    abstract void makeSound(); // Abstract method

    void breathe() {
        System.out.println("Breathing...");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Bark");
    }
}
```

**Usage & Caveats**:

* Use when multiple related classes **share some code** but must implement specific behaviors differently.
* Abstract classes help enforce a **common base contract** while allowing **partial implementation**.
* Ideal when behavior is shared but object creation should only be allowed via concrete subclasses.
* Can contain **constructors**, **fields**, and **non-abstract methods**.
* Abstract methods **must be implemented** by the first non-abstract subclass.
* Cannot be marked `final` (must allow inheritance).
* A class becomes abstract if it contains at least one abstract method.


**Since**: Java 1.0

**Memory/Performance**:

* No special memory cost compared to regular classes.
* Abstract methods incur **polymorphic dispatch (runtime overriding)**, like regular overridden methods.

---

#### ✅ 3. **Functional Interface**

> A functional interface is an interface that contains **exactly one abstract method**. It is used as the **target type for lambda expressions** and method references. Functional interfaces may also contain **default**, **static**, and **private methods**, but only one abstract method is allowed.

**Syntax & Example**:

```java
@FunctionalInterface
interface Converter<T, R> {
    R convert(T input);
}

Converter<String, Integer> toInteger = str -> Integer.parseInt(str);
System.out.println(toInteger.convert("42")); // Output: 42
```

**Usage & Caveats**:

* Used heavily in **Java 8+ features** like **Lambdas**, **Streams**, and **method references**
* The `@FunctionalInterface` annotation is **optional** but helps catch violations at compile-time
* Can extend another functional interface if it doesn't introduce additional abstract methods
* If more than one abstract method is present, it **fails to compile**
* Common built-in functional interfaces: `Runnable`, `Callable`, `Function`, `Consumer`, `Predicate`, `Supplier`

**Since**: Java 8

**Memory/Performance**:

* Lambda expressions using functional interfaces are compiled to **invokedynamic** bytecode
* Efficient and faster than anonymous inner classes due to reduced object allocation and bytecode size

---

#### ✅ 4. [Generics](https://docs.oracle.com/javase/tutorial/java/generics/index.html)

> Generics provide **compile-time type safety** and eliminate the need for casting by allowing classes, interfaces, and methods to operate on **typed parameters** (e.g., `List<String>`). They enable **code reusability** with different data types while preserving strong type checking.

**Syntax & Example**:

```java
class Box<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String msg = stringBox.get(); // No casting needed
```

**Usage & Caveats**:


* Commonly used in **collections** like `List<T>`, `Map<K, V>`, and utility methods
* **Eliminates ClassCastException** at runtime by enforcing types at compile time
* Supports **bounded types** using `extends` (upper bound) or `super` (lower bound) (more about this in box below)
* **Type erasure** removes generic type info at runtime — no access to type parameter with reflection
* Cannot create instances of generic types (`new T()` not allowed)
* Cannot use primitives directly (`List<int>` is invalid; use `List<Integer>`)
* Supports **wildcards** (`?`, `? extends`, `? super`) for flexible APIs

**Since**: Java 5

**Memory/Performance**:

* No extra runtime cost — generics use **type erasure**, so compiled bytecode is similar to raw types
* Improves performance by avoiding **unnecessary casting and boxing** in collections

```markdown

In Java Generics, we can **restrict the types** that can be used as type parameters using **bounded types** — this is done with `extends` (for upper bounds) and `super` (for lower bounds). These are mainly used in **wildcards**, but also in generic class or method declarations.

**Upper Bound (`extends`)**

Use when wanting to **accept a type or its subclasses**.
---
class Box<T extends Number> {  // T must be Number or a subclass
    T value;
}
---

With wildcards:
---
void printNumbers(List<? extends Number> list) {
    for (Number n : list) {
        System.out.println(n);
    }
}
---
* Accepts `List<Integer>`, `List<Double>`, `List<Number>`
* **Can't add** to the list (except `null`), but **can read** safely as `Number`

**Lower Bound (`super`)**

Use when wanting to **accept a type or its superclasses**.
---
void addIntegers(List<? super Integer> list) {
    list.add(10);  // Valid
}
---
* Accepts `List<Integer>`, `List<Number>`, `List<Object>`
* **Can add** `Integer` values, but **can't read** specific types (only as `Object`)


**Mnemonic**:
* `extends` → read-only
* `super` → write-only
  (Think: **PECS** – Producer Extends, Consumer Super)
```

---

#### ✅ 5. `static` keyword

> The `static` keyword marks members (fields, methods, blocks, or nested classes) as belonging to the **class itself** rather than to any instance. Static members are shared across all instances of the class.

**Syntax & Example**:

```java
class Utility {
    static int count = 0;
    static void increment() { count++; }
}

Utility.increment();
System.out.println(Utility.count); // Output: 1
```

**Usage & Caveats**:

* Used for **utility methods**, **shared constants**, and **counters**
* Static methods **cannot access instance members** directly (`this` and `super` are not allowed)
* Static blocks are used for **class-level initialization**
* A `static` nested class does not have a reference to its outer class instance
* Static fields are initialized **once per classloader**, not per instance
* Overriding static methods is **method hiding**, not polymorphism

---

#### ✅ 6. `final` keyword

> The `final` keyword is used to indicate that something **cannot be changed**. It can apply to variables (cannot be reassigned), methods (cannot be overridden), and classes (cannot be extended).

**Syntax & Example**:

```java
final class Constants {
    public static final double PI = 3.14159;
}

final int x = 10;
```

**Usage & Caveats**:

* For variables: ensures **immutability of reference**, not necessarily object contents
* For methods: prevents overriding in subclasses, used for **security and stability**
* For classes: prevents inheritance (e.g., `String`, `Math` are final)
* Final fields must be **initialized once**, either at declaration or in the constructor
* Final does **not mean immutable** for object references; it just means the reference can't change

---

#### ✅ 7. `super` keyword

> Refers to the parent class of the current object and is used to call superclass methods or constructors.

**Syntax & Example**:

```java
class Animal {
    void speak() { System.out.println("Animal"); }
}

class Dog extends Animal {
    void speak() {
        super.speak();  // Calls Animal's speak()
        System.out.println("Dog");
    }
}
```

**Usage & Caveats**:

* Used to access overridden methods, parent constructors, or hidden fields
* Must be the first statement in a constructor if calling a superclass constructor

---

#### ✅ 8. Default Methods in Interface

> A `default` method in an interface allows us to provide a **concrete method implementation** inside the interface itself. It was introduced in Java 8 to support **interface evolution** without breaking existing implementations.

**Syntax & Example**:

```java
interface Vehicle {
    default void start() {
        System.out.println("Starting vehicle...");
    }
}
```

**Usage & Caveats**:

* Lets interfaces have **method bodies**, unlike in earlier Java versions.
* Allows **adding new functionality** to interfaces without breaking existing classes.
* Can be **overridden** by implementing classes.
* A class implementing multiple interfaces with conflicting default methods must **override the method** to resolve the conflict.
* Not allowed in classes — `default` is only for interface methods.
* Cannot be `abstract`, `static`, or `final`.

**Memory/Performance**:

* No runtime overhead — compiled just like regular instance methods.
* Enables backward-compatible evolution of APIs (like Java Collections).

---

#### ✅ 9. Optional

> `Optional<T>` is a **container object** introduced in Java 8 to represent the **presence or absence of a non-null value**. It helps eliminate explicit `null` checks and reduces the chances of `NullPointerException`.

**Syntax & Example**:

```java
Optional<String> name = Optional.of("Hello!");
name.ifPresent(n -> System.out.println(n));  // prints "Hello!"

Optional<String> empty = Optional.empty();
System.out.println(empty.orElse("Default")); // prints "Default"
```

**Usage & Caveats**:

* Use `Optional` as a **return type**, not for fields or method parameters.
* Avoids verbose `null` checks with methods like `isPresent()`, `ifPresent()`, `orElse()`, `map()`, `flatMap()`, `filter()`.
* `Optional.of()` throws if value is null; use `Optional.ofNullable()` for null-safe wrapping.
* Not meant to replace all nulls — use where it **adds semantic meaning**.
* Do **not use in collections or serialization-heavy code** (adds overhead).
* Helps encourage **functional-style programming** and better **null safety**.

**Memory/Performance**:

* Slightly more memory than plain reference due to wrapper object.
* Eliminates many `NullPointerException`s at runtime by making **null handling explicit**.

---

#### ✅ 10. Annotations

> Annotations are **metadata markers** that provide information to the compiler, tools, or runtime. They do not change the actual logic but can influence behavior via frameworks, tools, or the Java compiler.

**Syntax & Example**:

```java
@Override
public String toString() {
    return "Example";
}

@SuppressWarnings("unchecked")
public void method() { }

@Deprecated
void oldMethod() {}
```

**Usage & Caveats**:

* Used for **compiler checks**, **code generation**, **dependency injection**, **configuration**, etc.
* Built-in annotations: `@Override`, `@Deprecated`, `@SuppressWarnings`, `@FunctionalInterface`, `@SafeVarargs`, etc.
* Custom annotations can be defined using `@interface` and may include elements (fields).
* Often used with frameworks (e.g., Spring, JUnit, Hibernate) for **declarative programming**.
* Can be retained at **source**, **class**, or **runtime** levels using `@Retention`.
* `@Target` controls where annotation can be applied (e.g., method, field, class).
* Reflection (`AnnotatedElement`) is required to read runtime annotations.
* Overuse can make code harder to read; best used where **declarative config** is clearer than imperative code.

**Since**: Java 5 (enhanced usage in Java 8+ with repeated and type annotations)

**Memory/Performance**:

* Negligible memory impact.
* Runtime annotations may slightly affect performance if used heavily with reflection.

---
