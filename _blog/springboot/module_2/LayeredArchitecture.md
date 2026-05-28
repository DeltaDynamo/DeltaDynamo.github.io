---
layout: post
title: "SpringBoot Module 2 : Layered Architecture Design Pattern in SpringBoot"
slug: "layered-architecture-pattern"
date: 2025-06-29
author: Anubhav Srivastava
tags: [software engineering, design pattern]
version: 1.0
---

* TOC
{:toc}

---

## ✅ What is Layered Architecture?

Layered Architecture (also called n-tier architecture) is a software design pattern that organizes an application into logical layers — each with a specific responsibility.

> **Common layers in this architecture**

| Layer                       | Description                                               |
| --------------------------- | --------------------------------------------------------- |
| **Controller**              | Handles HTTP requests and returns responses               |
| **Service**                 | Contains business rules / app logic                       |
| **Repository**              | Talks to the database (CRUD operations)                   |
| **Model (Entity)**          | Defines data structure (Java classes mapped to DB tables) |
| **Security (optional)**     | Manages login, roles, authentication                      |
| **Utils/Helper (optional)** | Common functionality like converters, validators          |


Let's consider package structure of a todo-application built using layered architecture approach:

```
com.example.todoapp
│
├── /controller
│ └── TodoController.java
│ └── AuthController.java
│
├── /service
│ └── TodoService.java
│ └── UserService.java
│
├── /repository
│ └── TodoRepository.java
│ └── UserRepository.java
│
├── /model
│ └── Todo.java
│ └── User.java
│
├── /security
│ └── JwtFilter.java
│ └── SecurityConfig.java
│
├── /utils
│ └── Validator.java
│
└── TodoApplication.java
```

> **The benefits of using layered architecture approach are...**

| Benefit                | Description                                                                   |
| ---------------------- | ----------------------------------------------------------------------------- |
| 🔍 **Readability**     | We can quickly locate code based on its function                             |
| 🔄 **Reusability**     | Services & repositories can be reused in multiple controllers                 |
| 🧪 **Testability**     | We can test each layer in isolation (e.g., mock service in controller tests) |
| 🧼 **Maintainability** | Makes it easier to refactor or scale features                                 |

---

### 🧱 What Each Layer Does

#### 1. Controller Layer (`controller`)

> Talks to the outside world — handles HTTP requests and responses.

* Maps routes like `/api/todos`
* Accepts user input (usually in DTO form)
* Sends it to the **Service** layer
* Returns the result to the client

```java
@RestController
@RequestMapping("/api/todos")
public class TodoController {
    @Autowired
    private TodoService todoService;
    
    @GetMapping
    public List<Todo> getTodos() {
        return todoService.getAllTodos();
    }
}
```

---

#### 2. Service Layer (`service`)

> Contains **business logic** — rules for how the app behaves.

* Orchestrates data between controller and repository
* Contains logic like filtering tasks by user, validating input, or sorting
* Keeps controllers thin and focused

```java
@Service
public class TodoService {
    @Autowired
    private TodoRepository todoRepository;

    public List<Todo> getAllTodos() {
        return todoRepository.findAll();
    }
}
```

---

#### 3. Repository Layer (`repository`)

> Talks directly to the **database** (or in-memory data source).

* Interface that extends `JpaRepository` or `CrudRepository`
* Automatically gives we methods like `findAll()`, `save()`, `deleteById()`
* Uses Spring Data JPA to reduce boilerplate

```java
public interface TodoRepository extends JpaRepository<Todo, Long> {
    List<Todo> findByUserId(Long userId);
}
```

---

#### 4. Model Layer (`model`)

> Defines the **data structure** (aka Entity).

* `@Entity` classes like `Todo`, `User`
* Maps to tables in the database
* Has annotations like `@Id`, `@Column`, `@ManyToOne`

---

#### 5. Security Layer (`security`)

> Handles **authentication and authorization**

* JWT token filters
* Custom `UserDetailsService`
* Spring Security configuration class

---

#### 6. Application Entry Point

```java
@SpringBootApplication
public class TodoAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(TodoAppApplication.class, args);
    }
}
```

* Boots the Spring context
* Scans components (`@ComponentScan`) to wire everything together

---

## 🔁 Why should we NOT Combine Service & Repository?

We *can* technically skip the service layer and call the repository from the controller in **small prototypes**, but here's why that's discouraged in real-world apps:

| Without Service             | With Service                 |
| --------------------------- | ---------------------------- |
| Controller gets bloated     | Controller is clean          |
| Business logic is scattered | Logic is centralized         |
| Hard to test in isolation   | Easier to mock in unit tests |
| Tight coupling              | Loose coupling               |

So, *Service Layer = Flexibility + Clean Code + Better Design*

---

## 🔁 Request Lifecycle
```
    Client ->> Controller: GET /api/todos
    Controller ->> Service: getAllTodos()
    Service ->> Repository: findAll()
    Repository -->> Service: List<Todo>
    Service -->> Controller: List<Todo>
    Controller -->> Client: 200 OK (JSON)
```
---

## ✅ Summary

| Layer      | Responsibility                           | Why It's Needed                     |
| ---------- | ---------------------------------------- | ----------------------------------- |
| Controller | Handles HTTP requests/responses          | Keeps web concerns separate         |
| Service    | Business logic & orchestration           | Clean, reusable logic layer         |
| Repository | DB operations using Spring Data JPA      | Minimal boilerplate for data access |
| Model      | Defines database schema (Entity classes) | Maps Java to DB tables              |
| Security   | Auth with JWT / Spring Security          | Adds protected access               |
| App class  | Bootstraps the app                       | Starts the Spring context           |

---
