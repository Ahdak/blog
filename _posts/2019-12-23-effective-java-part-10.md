---
layout: post
title: "Effective Java - 3rd Edition - Concurrency"
date: 2019-12-23
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 78 : Synchronize access to shared mutable data
The `synchronize` keyword ensures that only a single thread can execute a method or a block at one time. Proper use of synchronization guarantees that no method will ever observe the object in an consistent state. Synchronization ensures that each thread entering a synchronized method or block sees the effects of all previous modifications that were guarded by the same lock.

The language specification guarantees that reading or writing a variable is *atomic* unless the variable is of type __long__ or __double__.

Synchronization is required for reliable communication between threads as well as for mutual exclusion. This is due to a part of the language specification known as the *memory model*, which specifies when and how changes made by one thread become visible to others.

Let's take a look to this code
```java
    static boolean stopRequested = false;

    public static void main(String[] args) throws InterruptedException {

        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested) {
                System.out.println(i++);
            }
        }) ;
        backgroundThread.start();
        TimeUnit.MICROSECONDS.sleep(10);
        stopRequested = true ;
    }
```

Synchronization is not guaranteed to work unless both read and write operations are synchronized.

```java
    static boolean stopRequested = false;

    static synchronized void stop() {
        stopRequested = true ;
    }
    static synchronized boolean isStop() {
        return stopRequested ;
    }

    public static void main(String[] args) throws InterruptedException {

        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!isStop()) {
                System.out.println(i++);
            }
        }) ;
        backgroundThread.start();
        TimeUnit.MICROSECONDS.sleep(10);
        stop();
    }
```

The synchronization in those methods are used solely for its communication effects, and not for mutual exclusion.

In the second version, locking can be omitted if `stopRequested` is declared `volatile`. While the `volatile` modifier performs no mutual exclusion, it guarantees that any thread that reads the field will see the most recently written value :

```java
    static volatile boolean stopRequested;

    public static void main(String[] args) throws InterruptedException {

        Thread backgroundThread = new Thread(() -> {
            int i = 0;
            while (!stopRequested) {
                System.out.println(i++);
            }
        }) ;
        backgroundThread.start();
        TimeUnit.MICROSECONDS.sleep(10);
        stopRequested = true ;
    }
```

You have to be careful when using `volatile`. Consider the following method, which is supposed to generate serial numbers :
```java
    private static volatile int serialNumber = 0;

    public static int generateNext() {
        return serialNumber++ ;
    }
```

No synchronization is necessary to protect its invariants. Still, the method won't work properly without synchronization. The problem is that the increment `++` is not atomic. It performs two operations on the `serialNumber` field : first it reads the value, and then it writes back a new value. This is a safety failure : the program computes the wrong results.

One way to fix this method is to add the `synchronized` modifier on each declaration. This ensures that multiple invocations won't be interleaved and that each invocation of the method will see the effects of all previous invocations.

Better still, use the class `AtomicLong`, in the package `java.util.concurrent.atomic` which provides primitive for lock-free, thread safe programming on single variables. While `volatile` provides only the communication effects of synchronization, this package also provides atomicity.

The best way to avoid problems discussed in this item is not to share mutable data. In other words, confine mutable data to a single thread.

In summary, when multiple threads share mutable data, each thread that reads or writes data must perform synchronization.

# Item 79 : Avoid excessive synchronization
To avoid liveness and safety failure, never cede control to the client within a synchronized method or block. As a rule, you should do as little work as possible inside synchronized regions.

If a method modifies static field and there is any possibility that the method will be called from multiple threads, you must synchronize access to the field internally.

In summary, to avoid deadlock and data corruption, never call an alien method from within a synchronized region. More generally, keep the amount of work that you do from within synchronized to a minimum. When you are designing a mutable class, think about whether it should do its own synchronization. In the multicore era, it is more important that ever not to over-synchronize. Synchronize your class internally only if there is a good reason to do so, and document your decision clearly.

# Item 80 : Prefer executors, tasks, and streams to threads
Executor framework is a flexible interface based task execution facility.
```java
    ExecutorService service = Executors.newSingleThreadExecutor() ;
    service.execute(runnable);
    service.shutdown();
```

You can do many things with an executor :
- wait for particular task to complete
- wait for any or all of collection task to complete
- retrieve the result of task
- schedule tasks to run at a particular time or to run periodically using `ScheduledThreadPoolExecutor`

If you want more than one thread to process requests from the queue, simply call a different static factory that creates a different kind of executor service called a *thread pool* (with fixed or variable number of threads). You can also use `ThreadPoolExecutor` directly.

For a small program, `Executors.newCachedThreadPool` is generally good choice because it demands no configuration generally, but it is not a good choice for heavy loaded production. In cached thread pool, submitted task are  not queued but immediately handed off to a thread for execution.

Therefore, in a heavily loaded production server, you are much better off using `Executors.newFixedThreadPool`, which gives you a pool with a fixed number of threads, or using the `ThreadPoolExecutor` class directly for maximum control.

In the executor framework, the unit of work which is `task`, and and the execution mechanism (executor service) are separated.

There two kinds of task :
- `Runnable`
- `Callable` which return a value and can throw arbitrary exceptions

In java 7, Executor framework was extended to support fork-join tasks, which are run by special kind of executor service known as a fork-join pool.The task `ForkJoinTask` may be split into smaller sub-tasks, and the threads comprising a `ForkJoinPool`.

# Item 81 : Prefer concurrency utilities to `wait` and `notify`
Given difficulty of using `wait` and `notify` correctly, you should use the higher-level concurrency utilities instead.

The concurrent collections are high-performance concurrent implementations of standard collection interfaces. It is impossible to exclude concurrent activity from a concurrent collection; locking it will only slow the program.

Concurrent collections make synchronized collections largely obsolete. For example, use `ConcurrentHashMap` in preference to `Collections.synchronizedMap`.

synchronizers are objets that enable thread to wait for another, allowing them to coordinate their activities. The most powerful synchronizer is `Phaser`.  `CountDownLatch` and `Semaphore` are also commonly used.

For interval timing, use `System.nanoTime()` rather than `System.currentTimeMillis()`. `nanoTime` is more accurate and more precise.

The `wait` method is used to make thread wait for some condition. It must be invoked inside synchronized region :
```java
    synchronized (obj) {
        while (condition) {
            obj.wait(); // Released lock and reacquires on wakeup
        }
    }
```

Always use the `while` idiom to invoke `wait` method. Never invoke it outside of a loop. The loop serves to test the condition before and after waiting. Using `notifyAll` in preference to `notify` is reasonable and conservative. There is seldom, if ever, a reason to use `wait` and `notify` in new code.

# Item 82 : Document thread safety
You cannot know if a method is thread safe by looking if `synchronized` modifier exists in documentation. The presence of `synchronized` modifier is an implementation and not a part of its API.

To enable safe concurrent use, a class must declare what level of safety it supports :
- Immutable
- Unconditionally thread safe : Instances are mutable, but the class has sufficient internal synchronization
- Conditionally thread-safe : Class requires external synchronization for safe current use
- Not thread-safe : clients must surround each method invocation with external synchronization
- Thread hostile : unsafe for concurrent use. Usually results from modifying static data without synchronization.

To prevent from denial service attack, you can use private lock object instead of using `synchronized` methods :
```java
    private final Object lock = new Object();

    private void safeMethod() {
        synchronized (lock) {
            // Do some stuff
        }
    }
```

The lock is :
- `private` : not accessible outside the class
- `final` : prevents from changing the content

> Lock fields should always be declared final.

The private lock idiom ca be used only in Unconditionally thread safe classes. It is well suited for classes designed for inheritance.

# Item 83 : use lazy initialization judiciously
Lazy initialization is the act of delaying the instantiation of field until its value is needed. The only way to know for sure if lazy initialization is needed is to measure the class performance with and without lazy initialization. Under most circumstances, normal initialization is preferable to lazy initialization.

```java
    // normal initialization
    private final FieldType fieldType = new FieldType() ;

    // Lazy
    private FieldType lazyField;

    // Use synchronized to break initialization circularity
    public synchronized FieldType getField() {
        if (lazyField == null) {
            lazyField = new FieldType() ;
        }
        return lazyField ;
    }
```
If you need lazy initialization on `static` fields, use the lazy initialization holder class idiom :
```java
    private static class FieldHolder {
        static final FieldType FIELD =  new FieldType() ;
    }

    private static FieldType getField() {
        return FieldHolder.FIELD;
    }
```

If you need to use lazy initialization for performance on an instance field, use the double-check idiom. This idiom avoids the cost of locking when accessing field after initialization :
```java
    private volatile FieldType field;

    private FieldType getField() {
        FieldType result = field;
        if (result == null) { // first check no locking
            synchronized (this) {
                if (field ==null) { // sedond check with locking
                    field = result = new FieldType() ;
                }
            }
        }
        return result ;
    }
```

You can choose to remove `volatile` modifier. this variant is known as racy single-check idiom. It speeds up field access some architecture, at the expense of additional initialization.

# Item 84 : Don't depend on thread scheduler
When many threads are runnable, the thread scheduler determinates which ones get to run and for how long. Any program that relies on the thread scheduler for correctness or performance is likely to be nonportable.

The best way of writing robust, responsive and portable program is to ensure that the average number of running thread is not significantly greater than the number of processor. The main technique for keeping the number of running thread low is to have each thread do some useful work and then wait for more. It means
- sizing thread pool appropriately.
- keeping task short, but not too short

When you faced with program that works because some threads aren't getting enough CPU time, resist to the temptation of putting `Thread.yield`.

Thread priorities are among the least portable feature in java. It is rarely necessary  and not portable to tweak thread priorities.
