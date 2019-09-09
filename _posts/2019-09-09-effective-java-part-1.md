---
layout: post
title: "Effective Java - 3rd Edition - Part 1"
date: 2019-09-09
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 1 : Consider static factory methods instead of constructors

It's a technique of creating instances; A class can provide a public static factory method which returns an instance of this class.

Here is a simple example of `Boolean`  class :
```java
public static Boolean valueOf(boolean b) {
  return b ? Boolean.TRUE : Boolean.FALSE ;
}
```

The static factory method can be provided in addition to public constructors.

Advantages of Static factory :
- Static factory methods are hard to find for developers. have names which describes better the returned object.
- Static factory methods are not required to create a new object each time they are invoked, which allows immutable classes to use preconstructed instances, or to cache instances.
- Static factory methods can return any object of the subtype of their return class.
- The output of Static factory methods can vary from call to call depending of the input parameters (eg EnumSet).
- The class of the returned object need not to exist when the class containing the method is written (eg JDBC API).

Limitations of Static factory :
- Classes with only static factory method and without public or protected constructor __cannot be subclassed__.  
- Static factory methods are hard to find for developers.

Here are some examples of method names :
- from (eg `Date.from(Instant)`)
- of (eg `EnumSet.of(....)`)
- valueOf (eg `BigInteger.valueOf(...)`)
- instance or getInstance (eg `StackWalker.getInstance()`)
- create or newInstance (eg `Arrays.newInstance(...)`)
- getType (`Files.getFileStore(...)`)
- newType (eg `Files.newBufferedReader(...)`)
- type (eg `Collections.list(...)`)
