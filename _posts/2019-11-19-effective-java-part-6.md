---
layout: post
title: "Effective Java - 3rd Edition - Lambdas and Streams"
date: 2019-11-12
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 42 : Prefer lambdas to anonymous classes
Historically, interfaces with single abstract method were used as *functional types*.

Here is a code snippet using an anonymous class :
```java
    List<String> elements = List.of("A","B");
    Collections.sort(elements, new Comparator<>() {
        @Override
        public int compare(String o1, String o2) {
            return Integer.compare(o1.length(),o2.length()) ;
        }
    }) ;
```

Lambda expression as function replace anonymous classes :
```java
    Collections.sort(elements, (o1, o2) -> Integer.compare(o1.length(),o2.length())) ;
```

The comparator can be made even more succinct :
```java
    // using Comparator.comparingInt
    Collections.sort(elements, Comparator.comparingInt(String::length)) ;
    // using list sort method
    elements.sort(Comparator.comparingInt(String::length));
```

For the item 34, we can use `DoubleBinaryOperator` rather than `Function`.
```java
    public enum Operation {

        PLUS ((a,b) -> a + b),
        MINUS ((a,b) -> a - b),
        TIMES ((a,b) -> a * b),
        DIVIDE ((a,b) -> a / b);

        private final DoubleBinaryOperator operation ;

        Operation(DoubleBinaryOperator operation) {
            this.operation = operation;
        }

        public Double compute(Double x, Double y) {
            return operation.applyAsDouble(x,y);
        }
    }
```

## Lambda properties
- If a computation isn't self-explanatory, or exceeds a few lines (3 lines), don't put it in a lambda. One line is ideal for lambdas.
- Lambdas are limited to functional interfaces. If you want to create an abstract class, you can do it with anonymous class, but not in lambda.
- A lambda cannot obtain a reference to itself. the keyword `this` refers to the enclosing instance.
- Lambda share with anonymous classes the property that you can't reliably serialize and deserialize them across implementations. If you have a function object, use an instance of a private static nested class.


# Item 43 : Prefer method reference to lambdas
Java provides a way to generate objects even more succinct than lambdas : __method references__.

Let's take the following example that increments each map value with `1`. The output is `{1=2, 2=3, 3=4}`
```java
    Map<Integer,Integer> map = Maps.newHashMap();
    // Init map
    IntStream.rangeClosed(1,3).forEach(i -> map.put(i,i));
    // Increment each map value with 1
    IntStream.rangeClosed(1,3).forEach(i -> map.merge(i,1, (initialValue,incrementToAdd) -> initialValue + incrementToAdd));
    System.out.println(map);
```

We can simple pass a method reference to the `map.merge` using :
```java
    map.merge(i,1, Integer::sum);
```

The code is shorter and cleaner. However, in lambda, the parameter names you choose can provide useful documentation.

If you are programming with IDE, it will offer to replace a lambda with a method reference wherever it can.

The `Function` interface provides a static factory for identity method `Function.identity()`, which is equivalent to `x -> x`.

## Method type reference
- Static type : `Integer::parseInt` == `str -> Integer.parseInt(str)`
- In bound reference, the receiving object is specified in the method reference : `Instant.now()::isAfter` == `t -> Instant.now().isAfter(t)`.
- In unbound references, the receiving object is specified when the function is applied : `String::toLowerCase` == `s -> s.toLowerCase()`
- Class Constructor : `TreeMap<K,V>::new` == `() -> new TreeMap<K,V>`
- Array Constructor : `int[]::new` == `len -> new int[len]`.

# Item 44 : Favor the use of standard functional interfaces
