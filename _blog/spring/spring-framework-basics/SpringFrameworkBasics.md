---
layout: post
title: "Basics of the Spring Framework"
slug: "spring-framework-basics"
date: 2025-03-15
author: Anubhav Srivastava
tags: [Spring, Java, Dependency Injection, IoC, Spring Boot]
---

<div align="center">
    <img src="https://raw.githubusercontent.com/DeltaDynamo/DeltaDynamo.github.io/refs/heads/main/_blog/spring/spring-framework-basics/assets/banner.jpg" alt="Banner" style="width:95%">
</div>

Spring Framework is one of the most popular frameworks for building Java applications. It simplifies enterprise Java development by providing a comprehensive programming and configuration model. From building a simple web application to a complex microservices architecture, Spring has got everything needed to build a good engineering solution.

---

## Why Spring?

Before Spring, enterprise Java development often involved a lot of boilerplate code, complex configurations, and hard-to-test components. Traditional Java EE applications required cumbersome XML configurations, making development and testing frustrating. Spring was created to solve these problems.

Here's why developers love Spring:

- **Inversion of Control (IoC):** Spring takes care of managing dependencies so we don’t have to create objects manually.
- **Dependency Injection (DI):** Makes our code loosely coupled and easy to test.
- **Aspect-Oriented Programming (AOP):** Helps separate cross-cutting concerns like logging and security.
- **Simplified Data Access:** Provides seamless integration with JDBC, JPA, Hibernate, and other persistence frameworks.
- **Integration with Other Frameworks:** Works well with popular technologies like Hibernate, MyBatis, Kafka, RabbitMQ, and more.
- **Spring Boot:** Makes development faster by providing auto-configuration and production-ready features.

---

## Understanding Inversion of Control (IoC)

A fundamental principle of Spring is *Inversion of Control (IoC)*, which means the framework controls object creation and dependency management instead of us manually instantiating objects in our code. This is achieved through **Dependency Injection (DI)**.

### Example: Traditional vs Spring-based Dependency Management

#### Without Spring (Traditional Approach)
```java
public class OrderService {
    private PaymentService paymentService = new PaymentService();
    
    public void processOrder() {
        paymentService.processPayment();
    }
}
```
Here, `OrderService` is tightly coupled to `PaymentService`, making it hard to test and maintain.

#### With Spring (Dependency Injection)
```java
public class OrderService {
    private final PaymentService paymentService;
    
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```
Now, `OrderService` receives `PaymentService` as a dependency, making it easier to replace, mock, or modify it.

#### Inversion of Control vs Dependency Injection

| Feature                      | Inversion of Control (IoC)                                | Dependency Injection (DI)                          |
|------------------------------|----------------------------------------------------------|--------------------------------------------------|
| **Definition**                | A broader design principle where the framework manages object creation and lifecycle. | A specific technique to implement IoC by injecting dependencies instead of creating them manually. |
| **Control Mechanism**         | The flow of control is reversed – instead of the application controlling object creation, the framework does. | Dependencies are provided from an external source (e.g., Spring container) rather than being instantiated inside the class. |
| **Implementation**            | Achieved using various techniques like Dependency Injection, Service Locator, Factory Pattern, etc. | Specifically implemented through constructor injection, setter injection, or field injection. |
| **Example**                   | IoC ensures that Spring manages the lifecycle of beans in the ApplicationContext. | When a class receives its dependencies via constructor injection or setter injection rather than creating them inside the class. |

---

## Configuring Spring Beans

Spring manages objects as **beans**, which are created and maintained by the Spring **ApplicationContext**. There are three ways to configure beans in Spring:

1. **XML Configuration** (Old approach, rarely used now)
2. **Java-based Configuration** (Recommended approach using `@Configuration` and `@Bean` annotations)
3. **Annotation-based Configuration** (Using `@Component`, `@Service`, `@Repository`, etc.)

### Example: Java-based Configuration
```java
@Configuration
public class AppConfig {
    @Bean
    public PaymentService paymentService() {
        return new PaymentService();
    }
}
```
This `@Configuration` class defines a Spring bean that can be used throughout the application.

---

## What is `@SpringBootApplication`?

Spring Boot is an extension of Spring that simplifies application development by providing defaults, auto-configuration, and embedded web servers.

The `@SpringBootApplication` annotation is a shortcut that combines three important Spring annotations:

```java
@SpringBootApplication = @Configuration + @EnableAutoConfiguration + @ComponentScan
```

#### What These Annotations Do:

- `@Configuration` - Defines the class as a source of bean definitions for the Spring application context.
- `@EnableAutoConfiguration` - Enables Spring Boot to automatically configure the application based on dependencies present in the classpath.
- `@ComponentScan` - Instructs Spring to scan the package for components (beans, services, controllers, etc.) and register them automatically.


### Example: Creating a Simple Spring Boot Application

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MySpringBootApp {
    public static void main(String[] args) {
        SpringApplication.run(MySpringBootApp.class, args);
    }
}
```
With just a few lines of code, we get a fully functional Spring Boot application without any additional setup!

## Why Use Spring Boot?

Spring Boot makes working with Spring even easier by offering:

- **Auto-configuration**: Reduces manual setup by configuring beans automatically.
- **Embedded Servers**: Comes with Tomcat, Jetty, or Undertow out of the box, so we don’t need an external web server.
- **Production-ready Features**: Includes monitoring, metrics, and security features like Actuator.
- **Simplified Dependency Management**: Uses `spring-boot-starter` dependencies to include necessary libraries quickly.

---

## Conclusion

Spring Framework revolutionized Java development by promoting dependency injection, modularization, and testability. Spring Boot further simplifies this by reducing boilerplate code and providing a streamlined development experience.

---

## Interview Questions on Spring Basics

Here are some common interview questions based on the topics covered in this post:

1. **What is the Spring Framework, and why is it used?**
   - Spring is a framework that simplifies Java development by providing dependency injection, aspect-oriented programming, and data access features.

2. **What is Inversion of Control (IoC), and how does Spring implement it?**
   - IoC is a design principle where the control of object creation and dependency management is handled by the Spring container using Dependency Injection.

3. **Explain Dependency Injection (DI) with an example.**
   - DI is a technique where the dependencies of a class are injected externally rather than being created within the class itself. (Example provided above.)

4. **What are the different ways to configure beans in Spring?**
   - XML configuration, Java-based configuration, and annotation-based configuration.

5. **What is `@SpringBootApplication`, and how does it simplify configuration?**
   - It is a meta-annotation that combines `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` to simplify setup.

6. **How does Spring Boot handle embedded servers?**
   - Spring Boot provides embedded servers like Tomcat and Jetty, so no external server setup is needed.

7. **What is `@ComponentScan`, and why is it useful?**
   - It tells Spring to scan the package and register beans automatically, reducing manual configuration.

---
