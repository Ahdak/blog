---
layout: post
title: "Effective Java - 3rd Edition - Methods"
date: 2019-12-08
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 49 : Check parameters for validity
You should clearly document all method parameter restriction and enforce them with checks at the beginning of the method body. If an invalid parameter value is passed to the method and the method checks its parameters before execution, it will fail quickly and cleanly with an appropriate exception.

For public and protected methods, use the Javadoc `@throws` tag to document the exception that will be thrown, if a restriction o n a parameter values is violated.

```java
    /**
     *
     *
     * @param numberOfPersons number of person
     * @throws IllegalArgumentException if numberOfPersons < 2
     */
    void compute(int numberOfPersons) {
        if (numberOfPersons < 2) {
            throw new IllegalArgumentException("Number of Persons must be >= 2") ;
        }
        // Do some stuffs
    }
```

The `Objects.requireNonNull` method added in Java 7, is flexible and convenient, so there's no reason to perform `null` checks manually anymore.
```java
    // Inline use of Java's null-checking facility
    Objects.requireNonNull(elementsCount, "Cannot support null elements count") ;
```

If you invoke this method with null parameters
```
    Exception in thread "main" java.lang.NullPointerException: Cannot support null elements count
    	at java.base/java.util.Objects.requireNonNull(Objects.java:246)
```

Non public methods can check their parameters using `assertions`, as shown below
```java
    static private void sort(long[] data, int offset) {
        assert data != null ;
        assert offset< data.length ;
    }
```

Assertions throw `AssertionError` if they fail, and have no effects and essentially no cost unless you enable them by passing the `-ea` or `-enableassertions` flag to the java command.

```
Exception in thread "main" java.lang.AssertionError
	at part8.Item49.sort(Item49.java:32)
	at part8.Item49.main(Item49.java:10)
```

It is critical to check the validity of constructor parameters to prevent the construction of an object that violates its class invariants.

Occasionally, a computation implicitly performs a required check validity check, but throws the wrong exception if the check fails. You should use *exception translation* idiom to translate the natural exception into the correct one.

```java
    try {
        File.createTempFile("prefix","suffix") ;
    } catch (IOException e) {
        throw new CannotCreateFileException(e) ;
    }
```

You should design methods to be as general as it is practical to make them. The fewer restriction that you place on parameters, the better, assuming the method can do something reasonable with all of the parameter values that if accepts.

# Item 50 : Make defensive copies when needed

Java is a safe language : It is possible to write classes and know with certainty that their invariants will hold, no matter what happens in nay other part of the system.

Even in a safe language, you must program defensively, with the assumption that clients of you class will do their best to destroy its invariants.

Let's take the following example :
```java
    class Period{
        private final Date start ;
        private final Date end ;

        public Period(Date start, Date end) {
            this.start = start;
            this.end = end;
        }

        public Date getStart() {
            return start;
        }

        public Date getEnd() {
            return end;
        }
    }
```

## First attack
This class may appear immutable, but, it is easy to violate this invariants :
```java
    Date start = new Date() ;
    Date end = new Date() ;
    Period period = new Period(start,end);
    start.setTime(123123123L); // modifies internal of period
```

To protect the internals of a `Period` from this sort of attacks, it is essential to make defensive copies of each mutable parameter in the constructor. Note that defensive copies should be made before checking the validity of the parameters. It protects the class against changes to the parameters from another thread during the *window of vulnerability* between the parameters are checked and the time they are copied.

```java
    public Period(Date start, Date end) {
        this.start = new Date(start.getTime());
        this.end = new Date(end.getTime());
    }
```

Do not use the `clone` method to make defensive copy of parameter whose type is subclassed by untrusted parties.

## Second attack
It is possible to change internals of `Period` class using `getter` :
```java
    Period period = new Period(start,end);
    period.getEnd().setTime(123123L);
```

To defend against the second attack, merely modify the accessor to return defensive copies of mutable internal fields :
```java
    public Date getStart() {
        return new Date(end.getTime());
    }

    public Date getEnd() {
        return new Date(end.getTime());
    }
```

In the accessor, it whould be permissible to use `clone` method to make defensive copy.

### Solution `Instant`
`Date` is obsolete and should no longer be used in new code. The obvious way to fix that is to use `Instant`.
```java
    public static void main(String[] args) {

        Instant start = Instant.now();
        Instant end = Instant.now() ;
        Period period = new Period(start,end);
        start.plus(123, ChronoUnit.HOURS) ; // Returns a copy of this instant with the specified amount added.
    }

    static class Period{
        private final Instant start ;
        private final Instant end ;

        public Period(Instant start, Instant end) {
            this.start = start ;
            this.end = end ;
        }

        public Instant getStart() {
            return start;
        }

        public Instant getEnd() {
            return end;
        }
    }
```

### Perfs ?
There may be a performance penalty associated with defensive copying and it sin't always justified. If a class trust a classer not to modify an internal component, then it may be appropriate to dispose with defensive copying.

# Item 51 : Design (API) method signatures carefully
- Choose method name carefully (understandable and consistent)
- Don't go overboard in providing convenience method (For each action supported by your class, provide a fully functional method).
- Avoid long parameters list (max 4 parameters). There 3 techniques for shortening overly long parameters list
  - Break method into multiple methods
  - Create helper class to hold group of parameters (typically `static` member classes)
  - Adapt the Builder pattern (Create an object with all the parameters, with setters and `execute` method which does the final validity check on the parameters and perform the actual computation)
- Favor interfaces over classes for parameter types
- Prefer 2-element `enum` type rather than `boolean`.

# Item 52 : Use overloading judiciously
It is important to know that
- the choice of which overloading to invoke is made at compile time.
- the selection among overloaded methods is static, while selection among overridden methods is dynamic

```java
    private static String describe(List<?> list) {
        return "list" ;
    }

    private static String describe(Collection<?> collect) {
        return "Collection : " +collect.getClass().toString() ;
    }

    public static void main(String[] args) {
    Collection<?>[] collection = {Lists.newArrayList(), Sets.newHashSet()} ;
        Arrays.stream(collection).map(Item52::describe).forEach(System.out::println);
    }
```

The following code prints :
```
    Collection : class java.util.ArrayList
    Collection : class java.util.HashSet
```

You should avoid confusing use of overloading. A safe, conservative policy is never to export two overloading with the same number of parameters.

For constructors, you don't have the option of using different names. You do have in many cases the option of exporting static factories instead of constructors.

# Item 53 : Use varargs judiciously
Varargs methods, formally known as *variable arity* methods accept zero or more arguments of a specific type.
```java
    static int sum(int... values) {
        return Arrays.stream(values).sum() ;
    }

    public static void main(String[] args) {
        System.out.println(sum());
        System.out.println(sum(1,2));
    }
```

A wrong way to use varargs is when at least one argument is excepted :
```java
    // WRONG way
    static int sum(int... values) {
        if (values.length == 0) {
            throw new IllegalArgumentException("Specify at least one element !")
        }
        int weight = values[0] ;
        return weight * Arrays.stream(values).sum() ;
    }
```

The right way in this case is
```java
    static int sum(int weight, int... values) {
        return weight * Arrays.stream(values).sum() ;
    }
```

Exercise care when using varargs in performance critical situations. Every invocation of a varargs method causes an array allocation and initialization.

# Item 54 : Return empty collections or arrays not nulls
It is not uncommon to see methods return `null` :
```java
    static List<String>  toList(String... args) {
        if (args.length == 0) {
            return null ;
        }
        return Lists.newArrayList("...") ;
    }
```

Doing so requires extra code in client side :
```java
    List<String> datas = toList() ;
    if (datas!= null && datas.contains("AZE")) {
        // Do some stuff ...
    }
```

It is error-prone because the programmer writing the client code might forget to write the special case code to handle a `null` return.

It is sometimes argued that a `null` return value is preferable to an empty collection or array, but this argument fails :
- It is inadvisable to worry about performance at this level
- It is possible to return empty collection of table without allocating them

```java
    static List<String>  toList(String... args) {
        return Lists.newArrayList(args) ;
    }

    // Using Collection.emptyList()
    static List<String>  toList(String... args) {
        return args.length == 0 ? Collections.emptyList() : Lists.newArrayList(args) ;
    }
```

The situation for arrays is identical to that for collection :
```java

    // Optimization - avoids allocating empty arrays
    private static final String[] EMPTY_DATA = new String[0] ;

    static String[] getData() {
        return data.toArray(EMPTY_DATA) ;
    }
```

Do not preallocate the array passed to `toArray` in hope of improving performances Studies shows that it is contreproductive :

```java
    // DO NOT DO THIS !!
    data.toArray(new String[data.size()]) ;
```

# Item 55 : Return `Optional` judiciously
In Java 8, there is an approach to writing method that may not be able to return a value : `Optional<T>` class represents an immutable container that can hold either a single non-null `T` reference or nothing at all.

Let's take a look at this method
```java
    static <E extends Comparable<E>> E max(Collection<E> collection) {
        if (collection.isEmpty()) {
            throw new IllegalArgumentException("Cannot support Empty collection !") ;
        }
        E max = null ;
        for (E e : collection) {
            if (max == null || e.compareTo(max) > 0) {
                max = e ;
            }
        }
        return max ;
    }  
```

With `Optional`, the method looks like

```java
    static <E extends Comparable<E>> Optional<E> max(Collection<E> collection) {
        if (collection.isEmpty()) {
            return Optional.empty() ;
        }
        E max = null ;
        for (E e : collection) {
            if (max == null || e.compareTo(max) > 0) {
                max = e ;
            }
        }
        return Optional.of(max) ;
    }
```

Using terminal operator `max` which returns an `Optional` :

```java
    static <E extends Comparable<E>> Optional<E> max(Collection<E> collection) {
        return collection.stream().max(Comparator.naturalOrder()) ;
    }
```

> Never return `null` value from an `Optional` returning method. It defeats the entire purpose of the facility.

If a method returns an optional, the client gets to choose what action to take if the method can't return a value :
```java
    // Provide default value
    max = max(words).orElse("No words ... ") ;

    // Throw a choosen exception
    max = max(words).orElseThrow(CannotFindAnyBookException::new) ;
```

Let's take the following example :
```java
    Optional<Process> data = getProcess() ;
    System.out.println(data.isPresent() ? data.get().getId() : "NA");
```

This code can be replaced with :
```java
    System.out.println(getProcess().map(Process::getId).orElse("NA")) ;
```

> Container types, including collections, maps, arrays and optionals should not be wrapped in optionals.
> You should never return an optional of a boxed primitive type. Use `OptionalInt`, `OptionalLong` and `OptionalDouble`.
> It is almost never appropriate to use an optional as a key, value, or element in collection or array

# Item 56 : Write doc comments for all exposed API elements
