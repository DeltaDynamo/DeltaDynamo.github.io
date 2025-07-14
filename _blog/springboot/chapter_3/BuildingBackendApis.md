---
layout: post
title: "SpringBoot Chapter 3 : Building REST API's (Key Annotations)"
slug: "building-rest-apis-todo"
date: 2025-07-14
author: Anubhav Srivastava
tags: [software engineering, spring boot, rest apis, annotations]
---

In this post, I will try to cover all the important annotations required to build a fairly simple Backend application.
Hence for this post, I am considering building Backend API's for a Todo Application. The reason for this is that the requirements are fairly simple and we don't have to worry about complexity of the project itself.

---

### 🧠 1. Project Requirements
To build a **TODO backend** connected to a **MySQL database**, following clean **layered architecture (controller, service, repository, model)**, and using **Spring Data JPA**.

#### ✅ API Endpoints

A TODO App Backend with the following REST endpoints:

| HTTP Method | Endpoint      | Description              |
|-------------|---------------|--------------------------|
| GET         | /todos        | Get all todos            |
| GET         | /todos/{id}   | Get todo by ID           |
| POST        | /todos        | Create a new todo        |
| PUT         | /todos        | Update existing todo     |
| DELETE      | /todos/{id}   | Delete todo by ID        |


#### 🗂 Project Structure

```text
src.main.java.com.example.todoapp
├── controller
│   └── TodoController.java
├── service
│   └── TodoService.java
├── repository
│   └── TodoRepository.java
├── model
│   └── Todo.java
└── TodoAppApplication.java
```

#### Dependencies for Web, MySQL and JPA 
```xml
<!-- Spring Boot Starter Web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- Spring Data JPA -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- MySQL Connector -->
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

#### 🛠️ MySQL Configuration

A db named `tododb` needs to be created.
```sql
CREATE DATABASE tododb;
```

Then, in order to connect with db, configuration properties needs to be defined in `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/tododb
spring.datasource.username=<user_name>
spring.datasource.password=<user_pwd>

# The below option tell's Hibernate what to do with the database schema every time the application starts.
#| Value         | Behavior                                                     |
#|---------------|--------------------------------------------------------------|
#| `none`        | Do nothing to schema (safe for prod)                         |
#| `create`      | Drop and create tables every time (data loss)                |
#| `create-drop` | Same as `create`, but drops tables on shutdown               |
#| `validate`    | Only validates schema against entities — errors if mismatch  |
#| `update`      | Automatically creates/updates tables to match entities       |
spring.jpa.hibernate.ddl-auto=update

# This makes Spring print the SQL queries Hibernate generates to the console/log.
spring.jpa.show-sql=true

# This tells Hibernate which SQL dialect to use for generating queries. 
# Each database (MySQL, PostgreSQL, Oracle, etc.) has its own SQL syntax nuances. 
# Dialect is like a "translator" that tells Hibernate how to talk to that specific DB.
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

---

### 🧱 2. Main Class - `TodoAppApplication.java`

```java
@SpringBootApplication
public class TodoAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(TodoAppApplication.class, args);
    }
}
```

This is the entry point of our Spring Boot application — similar to the main() method in any standard Java application.

Let’s break it down:

🔹 @SpringBootApplication
This is a composite annotation — it combines three important annotations:

> **@Configuration**	Marks this class as a source of Spring bean definitions (like beans.xml)

> **@EnableAutoConfiguration**	Tells Spring Boot to auto-configure beans based on classpath dependencies

> **@ComponentScan**	Tells Spring to scan this package and subpackages for components (@Controller, @Service, etc.)

---

### 📄 3. Model (Entity) - `Todo.java`

```java
@Entity
@Table(name = "todos")
public class Todo {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String description;
    private boolean completed;

    // Constructors
    public Todo() {}

    public Todo(Long id, String title, String description, boolean completed) {
        this.id = id;
        this.title = title;
        this.description = description;
        this.completed = completed;
    }

    // Getters and Setters
}
```

| Annotation        | Role                                                |
| ----------------- | --------------------------------------------------- |
| `@Entity`         | Marks the class as a JPA entity                     |
| `@Table`          | Maps to the DB table (optional if name matches)     |
| `@Id`             | Primary key                                         |
| `@GeneratedValue` | [Auto-increment ID using MySQL's `IDENTITY` strategy](https://www.baeldung.com/hibernate-identifiers) |

---

### 📄 4. Repository - `TodoRepository.java`

```java
@Repository
public interface TodoRepository extends JpaRepository<Todo, Long> {
}
```

> No implementation required — Spring Data JPA creates it at runtime.

---

### 📄 5. Service - `TodoService.java`

> The **@Service** is a *stereotype* annotation in Spring, used to mark a Java class as a service component, meaning it's part of your application's business logic layer and should be automatically discovered and managed by Spring.

```java
@Service
public class TodoService {

    @Autowired
    private TodoRepository todoRepository;

    public List<Todo> getAllTodos() {
        return todoRepository.findAll();
    }

    public Todo getTodoById(Long id) {
        return todoRepository.findById(id).orElse(null);
    }

    public Todo createTodo(Todo todo) {
        return todoRepository.save(todo);
    }

    public boolean updateTodo(Todo updatedTodo) {
        if (todoRepository.existsById(updatedTodo.getId())) {
            todoRepository.save(updatedTodo);
            return true;
        }
        return false;
    }

    public boolean deleteTodo(Long id) {
        if (todoRepository.existsById(id)) {
            todoRepository.deleteById(id);
            return true;
        }
        return false;
    }
}
```

---

## 📄 6. Controller - `TodoController.java`

```java
@RestController
@RequestMapping("/todos")
public class TodoController {

    @Autowired
    private TodoService todoService;

    @GetMapping
    public ResponseEntity<List<Todo>> getAll() {
        return ResponseEntity.ok(todoService.getAllTodos());
    }

    @GetMapping("/{id}")
    public ResponseEntity<Todo> getById(@PathVariable Long id) {
        Todo todo = todoService.getTodoById(id);
        return todo != null ? ResponseEntity.ok(todo) : ResponseEntity.notFound().build();
    }

    @PostMapping
    public ResponseEntity<String> create(@RequestBody Todo todo) {
        todoService.createTodo(todo);
        return ResponseEntity.status(HttpStatus.CREATED).body("Todo created");
    }

    @PutMapping
    public ResponseEntity<String> update(@RequestBody Todo todo) {
        return todoService.updateTodo(todo)
            ? ResponseEntity.ok("Todo updated")
            : ResponseEntity.status(HttpStatus.NOT_FOUND).body("Todo not found");
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<String> delete(@PathVariable Long id) {
        return todoService.deleteTodo(id)
            ? ResponseEntity.ok("Todo deleted")
            : ResponseEntity.status(HttpStatus.NOT_FOUND).body("Todo not found");
    }
}
```
| Annotation        | Applied On     | Description                                                            |
| ----------------- | -------------- | ---------------------------------------------------------------------- |
| `@RestController` | Class          | [Combines `@Controller` + `@ResponseBody`. Used for REST APIs](https://www.baeldung.com/spring-controller-vs-restcontroller)          |
| `@RequestMapping` | Class / Method | Maps HTTP requests to methods. Supports all HTTP methods.              |
| `@GetMapping`     | Method         | Shortcut for `@RequestMapping(method = GET)`                           |
| `@PostMapping`    | Method         | Shortcut for `@RequestMapping(method = POST)`                          |
| `@PutMapping`     | Method         | Shortcut for `@RequestMapping(method = PUT)`                           |
| `@DeleteMapping`  | Method         | Shortcut for `@RequestMapping(method = DELETE)`                        |
| `@PatchMapping`   | Method         | Shortcut for `@RequestMapping(method = PATCH)`                         |
| `@PathVariable`   | Method Param   | Binds URL path segments to method parameters                           |
| `@RequestParam`   | Method Param   | Binds query parameters to method parameters                            |
| `@RequestBody`    | Method Param   | Maps JSON payload from request body to a Java object                   |

**What is [ResponseEntity<T>?](https://www.baeldung.com/spring-response-entity)**
```markdown
ResponseEntity<T> is a wrapper in Spring that allows for customization of the full HTTP response — not just the body, but also:

✅ The status code (like 200 OK, 201 Created, 404 Not Found)
✅ The headers
✅ The body (the actual data we’re returning)

| Method                       | Description                      |
| ---------------------------- | -------------------------------- |
| `.status(HttpStatus)`        | Set custom status                |
| `.status(int)`               | Set status by code               |
| `.body(T body)`              | Set response body                |
| `.ok(T body)`                | Return 200 OK with body          |
| `.ok()`                      | Return 200 OK without body       |
| `.noContent()`               | Return 204 No Content            |
| `.badRequest()`              | Return 400 Bad Request           |
| `.headers(HttpHeaders)`      | Set custom headers in bulk       |
| `.header(String, String...)` | Set individual headers           |
| `.build()`                   | Finalize response without a body |
```
---