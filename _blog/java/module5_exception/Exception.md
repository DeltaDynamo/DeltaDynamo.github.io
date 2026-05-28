---
layout: post
title: "Core Java Module 5 : Exception Handling in Java"
slug: "core-java-5-exception-handling"
date: 2025-11-14
author: Anubhav Srivastava
tags: [core java, exception handling, java exceptions]
version: 1.0
---

* TOC
{:toc}

---

### ⚠️ 1. Why Exception Handling Exists

In real-world applications, things can go wrong at any moment:

* Files may not exist
* Database connections can fail
* APIs may return invalid data
* Users may enter incorrect input

If a program crashes abruptly every time an issue occurs, it becomes unreliable.

That’s where **Exception Handling** comes in.

> Exception Handling allows us to gracefully detect, handle, and recover from runtime problems without crashing the entire application.

---

### 🧠 2. What is an Exception?

An **Exception** is an event that disrupts the normal flow of program execution.

In Java, exceptions are represented as **objects**.

#### 📘 Example

```java
int result = 10 / 0;
```

Output:

```text
Exception in thread "main" java.lang.ArithmeticException: / by zero
```

Here:

* `ArithmeticException` is the exception type
* JVM detected an invalid operation
* Program execution stopped

---

### 🛠️ 3. Basic Exception Handling using try-catch

Java provides `try-catch` blocks to handle exceptions safely.

#### 📘 Example

```java
public class Main {
    public static void main(String[] args) {

        try {
            int result = 10 / 0;
            System.out.println(result);
        } catch (ArithmeticException ex) {
            System.out.println("Cannot divide by zero");
        }

        System.out.println("Program continues...");
    }
}
```

#### 🔍 Flow

1. Code inside `try` executes
2. Exception occurs
3. JVM stops normal execution
4. Matching `catch` block executes
5. Program continues safely

---

### 🧩 4. Exception Hierarchy in Java

All exceptions in Java inherit from:

```text
Throwable
 ├── Error
 └── Exception
      ├── RuntimeException
      └── Checked Exceptions
```

---

#### 🔥 Error

Represents serious JVM/system failures.

Examples:

* `OutOfMemoryError`
* `StackOverflowError`

Usually, applications should not try to handle these.

#### ⚠️ Exception

Represents conditions applications may want to handle.

Two major categories:

1. Checked Exceptions
2. Unchecked Exceptions

---

## ✅ 5. Checked vs Unchecked Exceptions

This is one of the most important Java interview topics.

### 📦 Checked Exceptions

Checked exceptions are verified at **compile time**.

If a method throws a checked exception, we must either:

* handle it using `try-catch`
* OR declare it using `throws`

#### 📘 Example

```java
import java.io.*;

public class Main {

    public static void main(String[] args) {

        try {
            FileReader file = new FileReader("data.txt");
        } catch (FileNotFoundException ex) {
            System.out.println("File not found");
        }
    }
}
```

---

#### 🧠 Common Checked Exceptions

| Exception      | Scenario            |
| -------------- | ------------------- |
| IOException    | File operations     |
| SQLException   | Database operations |
| ParseException | Parsing failures    |

---

#### 📌 Why Checked Exceptions Exist

Java forces developers to consciously handle recoverable situations.

Example:

* missing file
* DB connection issue
* network failure

### 🚨 Unchecked Exceptions

Unchecked exceptions occur at runtime.

They are subclasses of:

```java
RuntimeException
```

Compiler does NOT force handling them.

#### 📘 Example

```java
int[] arr = {1, 2, 3};

System.out.println(arr[10]);
```

Output:

```text
ArrayIndexOutOfBoundsException
```

---

#### 🧠 Common Unchecked Exceptions

| Exception                      | Scenario                 |
| ------------------------------ | ------------------------ |
| NullPointerException           | Accessing null reference |
| ArithmeticException            | Divide by zero           |
| IllegalArgumentException       | Invalid method argument  |
| ArrayIndexOutOfBoundsException | Invalid array index      |


### ⚖️ Checked vs Unchecked — Comparison

| Feature                 | Checked Exception      | Unchecked Exception  |
| ----------------------- | ---------------------- | -------------------- |
| Checked at compile time | ✅ Yes                  | ❌ No                 |
| Must handle explicitly  | ✅ Yes                  | ❌ No                 |
| Inherits from           | Exception              | RuntimeException     |
| Represents              | Recoverable conditions | Programming bugs     |
| Example                 | IOException            | NullPointerException |

### 🧠 When to Use Which?

#### ✅ Use Checked Exceptions For

* external system failures
* file/database/network operations
* recoverable business conditions

---

#### ✅ Use Unchecked Exceptions For

* invalid input
* programming mistakes
* contract violations
* impossible states

---

## 🏗️ 6. Designing Custom Exceptions

Real-world applications often need domain-specific exceptions.

Examples:

* `InvalidPolicyException`
* `InsufficientBalanceException`
* `PaymentFailedException`

Custom exceptions improve:

* readability
* debugging
* business clarity
* error handling

### 📘 Basic Custom Exception

```java
class InvalidAgeException extends Exception {

    public InvalidAgeException(String message) {
        super(message);
    }
}
```

Usage:

```java
public class Main {

    static void validateAge(int age) throws InvalidAgeException {

        if (age < 18) {
            throw new InvalidAgeException("Age must be 18 or above");
        }
    }

    public static void main(String[] args) {

        try {
            validateAge(15);
        } catch (InvalidAgeException ex) {
            System.out.println(ex.getMessage());
        }
    }
}
```

---

## ✅ 7. Best Practices for Designing Custom Exceptions

### ✅ 7.1 Choose Checked vs Unchecked Carefully

#### Use Checked Exception When:

Caller can reasonably recover.

Example:

```java
PaymentFailedException
```

#### Use Runtime Exception When:

Problem indicates programming misuse.

Example:

```java
InvalidConfigurationException
```

### ✅ 7.2 Use Meaningful Exception Names

Good:

```java
InsufficientFundsException
```

Bad:

```java
MyException
```

Exception names should clearly communicate the problem.

### ✅ 7.3 Include Useful Context in Messages

Bad:

```java
throw new Exception("Error");
```

Good:

```java
throw new InvalidPolicyException(
    "Policy ID 1023 is inactive"
);
```

Detailed messages simplify debugging.

### ✅ 7.4 Preserve Original Exceptions (Exception Chaining)

Always preserve root cause when wrapping exceptions.

#### 📘 Example

```java
try {
    database.save(policy);
} catch (SQLException ex) {

    throw new PolicySaveException(
        "Failed to save policy",
        ex
    );
}
```

---

#### Why This Matters

Without chaining:

* original stack trace is lost
* debugging becomes difficult

### ✅ 7.5 Avoid Creating Too Many Exception Types

Do NOT create separate exceptions for every tiny case.

Keep hierarchy meaningful and manageable.

### ✅ 7.6 Prefer Specific Exceptions

Bad:

```java
catch (Exception ex)
```

Better:

```java
catch (SQLException ex)
```

Specific exceptions improve reliability and debugging.

## 🔄 8 finally Block

`finally` always executes whether exception occurs or not.

Typically used for cleanup.


#### 📘 Example

```java
try {
    System.out.println("Inside try");
} catch (Exception ex) {
    System.out.println("Inside catch");
} finally {
    System.out.println("Cleanup logic");
}
```

Output:

```text
Inside try
Cleanup logic
```

## 🧼 9. try-with-resources

Managing resources manually is error-prone.

Resources like:

* files
* sockets
* database connections

must be closed properly.

Java introduced `try-with-resources` in Java 7.

---

### 📘 Traditional Resource Handling

```java
BufferedReader reader = null;

try {
    reader = new BufferedReader(
        new FileReader("data.txt")
    );

} finally {

    if (reader != null) {
        reader.close();
    }
}
```

Problems:

* verbose
* error-prone
* resource leaks possible

---

### ✅ Modern Approach: try-with-resources

```java
try (
    BufferedReader reader =
        new BufferedReader(
            new FileReader("data.txt")
        )
) {

    System.out.println(reader.readLine());

} catch (IOException ex) {
    ex.printStackTrace();
}
```

### 🧠 Benefits

* automatic cleanup
* cleaner syntax
* safer resource handling
* fewer memory/resource leaks

### 📌 AutoCloseable Interface

`try-with-resources` works with classes implementing:

```java
AutoCloseable
```

Example:

```java
class MyResource implements AutoCloseable {

    @Override
    public void close() {
        System.out.println("Resource closed");
    }
}
```

---

## ⚠️ 10. Suppressed Exceptions

One hidden problem existed before Java 7.

### 📘 Problem Scenario

Suppose:

1. exception occurs inside `try`
2. another exception occurs during `close()`

The second exception could hide the original exception.

### 📘 Example

```java
class TestResource implements AutoCloseable {

    @Override
    public void close() {
        throw new RuntimeException(
            "Exception during close"
        );
    }
}
```

```java
public class Main {

    public static void main(String[] args) {

        try (TestResource r = new TestResource()) {

            throw new RuntimeException(
                "Main exception"
            );

        } catch (Exception ex) {

            System.out.println(ex.getMessage());

            for (Throwable t :
                    ex.getSuppressed()) {

                System.out.println(
                    "Suppressed: " +
                    t.getMessage()
                );
            }
        }
    }
}
```

#### ✅ Output

```text
Main exception
Suppressed: Exception during close
```

---

### 🧠 Why Suppressed Exceptions Matter

Without suppression:

* original exception could be lost
* debugging becomes difficult

Java preserves secondary exceptions safely.

## 🚫 11. Common Exception Handling Mistakes

### ❌ Swallowing Exceptions

Bad:

```java
catch (Exception ex) {
}
```

Never ignore exceptions silently.

### ❌ Using Exceptions for Control Flow

Bad practice:

```java
try {
    arr[100];
} catch (Exception ex) {
}
```

Use proper validations instead.

### ❌ Catching Generic Exception Everywhere

Bad:

```java
catch (Exception ex)
```

Prefer specific exception types.

### ❌ Excessive Checked Exceptions

Too many checked exceptions can make APIs difficult to use.

Design carefully.

---

## 🧠 12. Summary

| Concept               | Purpose                            |
| --------------------- | ---------------------------------- |
| try-catch             | Handle exceptions safely           |
| Checked Exception     | Recoverable conditions             |
| Unchecked Exception   | Programming errors                 |
| Custom Exception      | Domain-specific errors             |
| finally               | Cleanup logic                      |
| try-with-resources    | Automatic resource cleanup         |
| Suppressed Exceptions | Preserve hidden cleanup exceptions |

---

## ✅ 13. Key Takeaways

1. Exceptions help applications fail gracefully
2. Checked exceptions represent recoverable conditions
3. Runtime exceptions usually indicate programming bugs
4. Design custom exceptions carefully and meaningfully
5. Prefer `try-with-resources` over manual cleanup
6. Preserve root causes using exception chaining
7. Understand suppressed exceptions for proper debugging

---

