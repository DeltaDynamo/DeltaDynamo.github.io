---
layout: post
title: "Post1"
slug: "1st Post"
date: 2024-12-06
author: Anubhav Srivastava
---
# Java ArrayList: A Dynamic Array

In Java, an `ArrayList` is a resizable array that allows you to store elements dynamically. Unlike arrays, which have a fixed size, `ArrayList` can grow and shrink in size as elements are added or removed.

<img src="https://images.pexels.com/photos/546819/pexels-photo-546819.jpeg" width="35%">

## Creating an ArrayList

You can create an `ArrayList` using the following syntax:

```java
import java.util.ArrayList;

public class Example {
    public static void main(String[] args) {
        ArrayList<String> fruits = new ArrayList<>();
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Orange");

        System.out.println(fruits);
    }
}
```