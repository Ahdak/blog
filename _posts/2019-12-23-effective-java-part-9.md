---
layout: post
title: "Effective Java - 3rd Edition - Exceptions"
date: 2019-12-23
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 69 : Use exceptions only for exceptional conditions
Someday, if you are unlucky, you may stumble across a piece of code that looks like this :
```java
    // Horrible use of exceptions
    try {
        int i = 0 ;
        while (true) {
            System.out.println(array[i++]);
        }
    } catch (ArrayIndexOutOfBoundsException e) {}
```

There are three things wrong with this code :
- Because exceptions are designed for exceptional circumstances, there is little incentive for JVM implementors to make them as fast as explicit class
- Placing code inside try catch block inhibits certain optimizations that JVM implementations may otherwise perform
- The standard idiom for looping through an array doesn't necessarily results in redundant checks. Many JVM implementations optimize them away

In fact, the exception based idiom is far slower than the standard one (twice as slow as standard one).

Exceptions are, as their name implies, to be used only for exceptional conditions; they should never be used for ordinary control flow. A well designed API must not force its clients to use exceptions for ordinary control flow.

# Item 70 : Use checked exceptions for recoverable conditions and runtime exceptions for programming errors
Java provides three kinds of `Throwable` :
- Checked exceptions
- Runtime exception
- Errors

Use checked exceptions for conditions from which the caller can reasonably be expected to recover. You force the caller to handle the exception in a `catch` block or to propagate it.

Use runtime exceptions to indicate programming errors. The great majority of runtime exceptions indicates *precondition violations*.

If you believe a condition is likely to allow for recovery, use checked exceptions; if not, use runtime exceptions.

There is a strong convention that *errors* are reserved for use bu the JVM to indicate resource deficiencies, invariant failure ... Therefore, all of the unchecked throwables you implement should subclass `RuntimeException` (directly or indirectly).

Because checked exceptions generally indicate recoverable conditions, it's especially important for them to provide methods that furnish information to help the caller recover from the exceptional condition.

# Item 71 : Avoid unnecessary use of checked exceptions
Methods throwing checked exceptions can't be used directly in streams.
An unchecked exception use is appropriate unless both of these conditions are met :
- The exceptional condition cannot be prevented by proper use of the API
- The programmer using the API can take some useful action once confronted with the exception

Ask yourself how the programmer will handle the exception ? If the programmer can't do better than `e.printStackTrace()` or `throw new AssertionError()`, then, an unchecked exception is called for.

The easiest way to eliminate checked exception is to return `Optional` of the desired result type. Instead of throwing checked exception, the method simply returns an empty `Optional`. The disavantage of this technique is that the method can't return additional information detailing the inability to perform the desired computation.

You can also turn a checked exception into an unchecked exception by breaking the method that `throws` the exception into two methods :
```java
    void compute() throws Exception {}

    public static void main(String[] args) {
         try {
             compute();
         } catch (Exception e) {
             // handle exception
         }
     }
```

into this :
```java
    public static void main(String[] args) {
         if (canCompute()) {
             compute();
         } else {
             // Handle exception condition
         }
     }

     private static boolean canCompute() {
         // check
         return false ;
     }

     static void compute()  {}
```

This refactoring is not always appropriate, but, where it is, it can make an API more pleasant to use.

# Item 72 : Favor the use of standard exceptions
Reusing standard exceptions has several benefits :
- makes your API easier to learn and use
- The client code is easier to read because programmers aren't cluttered with unfamiliar exceptions
- Fewer exception classes means a smaller memory footprint and less time spent loading classes

The most commonly used exception type are :
- `IllegalArgumentException` : when the caller passes in an argument whose value is inappropriate (non-null object)
- `IllegalStateException` : if the invocation is illegal because of the state of the receiving object
- `ConcurrentModificationException`: if an object that was designed for single thread use, detects that it is being modified concurrently.
- `UnsupportedOperationException` : if object does not support method

Conventions dictates that
- `NullPointerException` be thrown rather than `IllegalArgumentException` if caller  passes `null` in some parameters for which null values are prohibited
- `IndexOutOfBoundsException` should be thrown rather than `IllegalArgumentException` if the index passed in parameter is out-of-range

Do not reuse `Exception`, `RuntimeException` , `Throwable` or `Error` directly. Treat these classes as if they were abstract.

It would be appropriate to reuse `ArithmeticException` and `NumberFormatException` if you were implementing arithmetic objects.

In the following case, prefer `IllegalStateException` to `IllegalArgumentException` :
```java
    static class Account {
        private int positiveAmount ;

        void pay(int amountToPay) {
            if (amountToPay> positiveAmount) {
                throw new IllegalStateException("The current amount is not sufficient") ;
            }
        }

        // Otherwise
        if (amountToPay> positiveAmount) {
          //throw new IllegalStateException("The current amount is not sufficient") ;
          throw new IllegalArgumentException("The amount to pay is greater than the current amount") ;
        }
    }
```

# Item 73 : Throw exceptions appropriate to the abstraction
Higher level should always catch lower-level exceptions and in their place, throw exceptions that can be explained in terms of the higher-level abstraction. This idiom is known as __exception translation__.

```java
    static class LowerLevelException extends Exception {}

    static class HigherLevelException extends RuntimeException{

        public HigherLevelException(String message) {
            super(message) ;
        }
    }

    static void compute() throws LowerLevelException{}

    public static void main(String[] args) {
        try {
            compute();
        } catch (LowerLevelException e) {
            // Exception translation
            throw new HigherLevelException("An exception occurs") ;
        }

    }
```

A special form of exception translation called __exception chaining__ is called where the lower-level exception might be helpful to someone debugging the problem that caused by the higher-level exception.

```java
    static class HigherLevelException extends RuntimeException{

        // Exception with chaining-aware constructor
        public HigherLevelException(LowerLevelException e) {
            super(e) ;
        }
    }

    public static void main(String[] args) {
        try {
            compute();
        } catch (LowerLevelException e) {
            // Exception chaining
            throw new HigherLevelException(e) ;
        }

    }
```

While exception translation is superior to mindless propagation of exceptions from layers, it should not be overused. Where possible, the best way to deal with exceptions from layer to layer is to avoid them by ensuring that lower-level methods succeeds (validity checks).

# Item 74 : Document all exceptions thrown by each method
A description of the exceptions thrown by a method is an important part of the documentation required to use method properly.

Always declare checked exceptions individually, and document precisely the conditions under which each one is thrown using the javdoc `@throws` tag, but do not use `@throws` keyword on unchecked exceptions.

Unchecked exceptions generally represent programming errors, and familiarizing programmers with all of the errors they can make helps them avoid making these errors. It's particularly important to document the unchecked exceptions, but, not always achievable in the real world.

A well documented list of unchecked exceptions that a method can throw effectively describes the *preconditions* for its successful execution.

If an exception is thrown by many methods in a class for the same reason, you can document the exception in the class's documentation comment tather than documentation it individually for each method.

# Item 75 : Include failure-capture information in detail messages
When a program fails due to an uncaught exception, the system automatically prints out the exception's stack trace. Therefore, it is critically important that the exception's `toString` method return as much information as possible concerning the cause of the failure.

To capture a failure, the detail message of an exception should contains the values of all parameters and fields that contributed to the exception.

Because stack traces may been seen by many people in the process, do not include passwords, encrypting keys, and the like in detail messages.

For example, `IndexOutOfBoundsException` in java 9, acquired a constructor that takes an int valued index parameter.

# Item 76 : Strive for failure atomicity
Generally speaking, a failed method invocation should leave the object in the state that it was in prior to the invocation. A method with this property is said to be *failure-atomic*.

There are several ways to achieve this :
- The simplest way to do that is to design immutable objects.
- Check parameters validity before performing the operation

A closely related approach to achieving failure atomicity is to order the computation so that, any part that may fail, takes place before any part that modifies the object. This approach is natural extension of the previous one when arguments cannot be checked without performing a part of the computation.

A third approach to achieving failure atomicity is to perform the operation on a temporary copy of the object and to replace the content of the object with the temporary copy once the operation is complete.

A last and far less common approach to achieve failure atomicity is to write *recovery code* that intercepts a failure that occurs and causes the object to roll back its state to the point before the operation began. This approach is used mainly for durable (disk-based) data structures.

While failure atomicity is generally desirable, it is not always achievable. Even where failure atomicity is possible, it is not always desirable. For some operations, it would significantly increase the cost or complexity.

# Item 77 : Don't ignore exceptions
When the designer of an API declare a method to throw an exception, they are trying to tell you something. Don't ignore it !

An empty `catch` block defeats the purpose of exceptions, which is to force you to handle exceptional conditions. If you choose to ignore an exception, the `catch` block should contain a comment explaining why it is appropriate to do so, and the variable should be named `ignored`.
