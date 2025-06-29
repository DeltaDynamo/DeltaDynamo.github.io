---
layout: post
title: "SpringBoot Chapter 1 : Folder Structure, POM & Hello World Application"
slug: "springboot-chapter-1"
date: 2025-06-09
author: Anubhav Srivastava
tags: [springboot, pom.xml, maven]
---

*In this post, we consider maven to be our choice of build and dependency management tool.*

### ✨ 1. Spring Boot Project Folder Structure

```
my-springboot-app/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/demo/
│   │   │       └── DemoApplication.java
│   │   └── resources/
│   │       ├── application.properties
│   │       └── static/
│   │       └── templates/
│   └── test/
│       └── java/
│           └── com/example/demo/
│               └── DemoApplicationTests.java
├── .mvn/
├── mvnw / mvnw.cmd
├── pom.xml
└── README.md
```

#### Explanation of Key Files and Directories:

* **`src/main/java/`**: Our Java source code goes here. By default this is the path SpringBoot expects the production code to be in.
* **`src/main/resources/`**: Configuration files, templates, and static assets.

  * `application.properties`: App configuration (port, database, etc.)
  * `static/`: Public static files (e.g., HTML, CSS, JS)
  * `templates/`: Thymeleaf or other template engines
* **`src/test/java/`**: Unit and integration tests
* **`.mvn/`** and `mvnw`, `mvnw.cmd`: Maven wrapper files (optional)
* **`pom.xml`**: Maven's Project Object Model file. Central to the build and dependency system. This must be present at the root of the project, same level as src.
* **`README.md`**: Optional documentation.

> Note: While `src/main/java` is conventional, Maven allows us to change it, but it's highly recommended to follow this convention for compatibility and tooling support.

More about Maven standard directory layout : [Introduction to the Standard Directory Layout
](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)

---

### 📂 2. Where Are All the Dependencies Stored?

When we build our project with Maven, it downloads all required dependencies (JAR files) into our system's **local Maven repository**, not our project folder.

#### Default Location:

* **Windows**: `C:\Users\<username>\.m2\repository`
* **Linux/macOS**: `/home/<username>/.m2/repository`

Maven caches dependencies so they don't need to be re-downloaded for every project. This makes builds faster and projects cleanr.

> We can change the local repository location by editing the `settings.xml` file in `.m2`.

---

### 🔧 3. Understanding `pom.xml` in Depth

`pom.xml` is the backbone of a Maven-based Spring Boot project. More about [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)

#### Sample `pom.xml`:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>hello-spring</artifactId>
    <version>1.0.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
    </parent>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

#### Key Sections:

* **`groupId` / `artifactId` / `version`**: Define's the project coordinates.
* **`parent`**: Inherits Spring Boot’s default setup (dependency versions, plugins).
* **`properties`**: Configures build-level settings like Java version.
* **`dependencies`**: Lists all needed libraries (e.g., web starter).
* **`build`**: Adds plugins; `spring-boot-maven-plugin` enables building executable JAR's.

---

### 🚀 4. Let's Build the Hello World Spring Boot Application

#### Step-by-step without using Spring Initializer:

1. **Creating folder structre manually as shown above**
2. **Writing the main class**:

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @GetMapping("/")
    public String hello() {
        return "Hello, Spring Boot!";
    }
}
```

3. **Adding `pom.xml` to the root of the project** (as above)
4. **Compile and run**:

```bash
mvn clean install
java -jar target/<filename>.jar
```
- `mvn clean install`: Cleans old builds and compiles, tests, and packages the application into a JAR. The output is stored in `/target` folder

We can now hit `http://localhost:8080` in the browser to see the message: `Hello, Spring Boot!`

---
