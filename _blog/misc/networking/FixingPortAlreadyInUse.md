---
layout: post
title: "Fixing Port Already in Use"
slug: "fixing-port-already-in-use"
date: 2025-06-09
author: Anubhav Srivastava
tags: [networking, command prompt]
---

Recently, while attempting to launch a Spring Boot application from my VS Code terminal using `mvn spring-boot:run`, I consistently encountered the error: *Port already in use*. 
In this post, I’ll walk through how to determine which process is blocking the port and how to properly shut it down.

---

#### On Windows

1. Find the Process ID which is blocking this port.
By running this command you will get a list of processes using this port, The last number on the line is the PID (Process ID).
```
netstat -aon | findstr 8080
```

2. Run the below command with the PID you find above. This command will kill the process which was using the port. Incase you have got multiple PID's in the previous step, Use the PID of the parent process.
```
taskkill /F /PID <PID>
```

---
#### On Mac/Linux

1. Find the Process ID which is blocking this port.

```
netstat -tulpn | grep :8080
```
Look for the PID in the output.

2. Run the below command with the PID you find above. This command will kill the process which was using the port. Incase you have got multiple PID's in the previous step, Use the PID of the parent process.

```
kill -9 <PID>
```

---