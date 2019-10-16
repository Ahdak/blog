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

# Item 2 : Consider a builder when faced with many constructor parameters

To create class having many required parameters generally marked as `final`, developer often use __Telescoping constructor pattern__.

```java
public class Person {
  private final int age ;
  private final int weight ;
  private final String name ;
  private final String fistName ;

  public Person(String name, String firstName) {
    this(0,0,name,firstName);
  }

  public Person(int weight, String name, String firstName) {
    this(0,weight,name,firstName);
  }

  public Person(int age, int weight, String name, String firstName) {
    this.age = age ;
    this.weight = weight ;
    this.name = name ;
    this.firstName = firstName ;
  }
}
```

To instantiate this class
```java
  Person person = new Person(30,90,"name","fistName") ;
```

It's hard to write code when there are many parameters, and still harder to read it.

A second alternative, is to create __Java Beans__, creating getters & setters and removing final modifiers. But, in this case, created objects may be inconsistent,  because construction is split across multiple calls.

Luckily, there is a third alternative which is `Builder Pattern`; combines telescoping and JavaBeans readability.

```java
public class Person {

    private final String name ;
    private final String firstName ;
    private final int age ;
    private final int weight ;

    private Person(int age, int weight, String name, String firstName) {
        this.age = age;
        this.weight = weight;
        this.name = name;
        this.firstName = firstName;
    }

    public static class PersonBuilder {
        private int age = 0; // Optional
        private int weight; // Optional
        private final String name;
        private final String firstName;

        public PersonBuilder(String name, String firstName) {
            this.name = name;
            this.firstName = firstName;
        }

        public PersonBuilder age(int age) {
            this.age = age;
            return this;
        }

        public PersonBuilder weight(int weight) {
            this.weight = weight;
            return this;
        }

        public Person build() {
            return new Person(age, weight, name, firstName);
        }

        public String toString() {
            return "Person.PersonBuilder(age=" + this.age + ", weight=" + this.weight + ", name=" + this.name + ", firstName=" + this.firstName + ")";
        }
    }
}
```

You can also use `Lombok` framework with `@Builder` annotation:
```java
import lombok.Builder;

@Builder
public class Person2 {
    private final String name ;
    private final String firstName ;
    private final int age ;
    private final int weight ;
}
```

The builder pattern is well suited to class hierarchies :

```java
public class Person {

    public enum Properties {IS_MARRIED, HAS_CHILDREN, HAS_JOB}
    final EnumSet<Properties> properties;

    abstract static class Builder<T extends Builder<T>> {
        final EnumSet<Properties> properties = EnumSet.noneOf(Properties.class);

        // common method for all subclasses
        public T addProperty(Properties property) {
            properties.add(property) ;
            return self() ;
        }

        abstract Person build() ;

        protected abstract T self() ;

    }

    Person(Builder<?> builder) {
     this.properties = builder.properties.clone();
    }
}
```

Creating then 2 subclasses with sub-builders
```java
public class Person1 extends Person {
    private final int age ;
    public static class Person1Builder extends Person.Builder<Person1Builder> {

        private final int age ;

        public Person1Builder(int age) {
            this.age = age;
        }

        Person1 build() {
            return new Person1(this);
        }

        protected Person1Builder self() {
            return this;
        }
    }

    private Person1(Person1Builder builder) {
        super(builder);
        age=builder.age ;
    }
}
```

`Person1` class has extra-attribute `age` which is handled in the `Person1Builder`.

```java
public static class Person2 extends Person {
    private final String name;

    public static class Person2Builder extends Person.Builder<Person2Builder> {

        private final String name ;

        public Person2Builder(String name) {
            this.name = name;
        }

        Person2 build() {
            return new Person2(this);
        }

        protected Person2Builder self() {
            return this;
        }
    }

    private Person2(Person2Builder builder) {
        super(builder);
        name=builder.name ;
    }
}
```

`Person2` class has extra-attribute `name` which is handled in the `Person2Builder`.

Creating instances of `Person1` and `Person2` classes would be like :
```java
Person1 person1 = new Person1.Person1Builder(30).addProperty(Person.Properties.HAS_CHILDREN).build() ;
Person2 person2 = new Person2.Person2Builder("Ahmed").addProperty(Person.Properties.IS_MARRIED).build() ;
```

The builder pattern is a good choice when designing classes whose constructors or static factories would have more than a handful of parameters.

# Item 3 : Consider the singleton property with a private constructor or an enum type

A __Singleton__ is a class instantiated once, typically represents a stateless object.

There are 2 common methods to create singletons :

### Singleton with public static field
```java
public class Singleton1 {
    private static final Singleton1 INSTANCE = new Singleton1() ;
    private Singleton1() {}
}
```

Advantages :
- The API makes it clear that the class is a singleton
- The private field is `final`, so it will always contain the same object reference.

### Singleton with public static factory
```java
public class Singleton2 {
    private static final Singleton2 INSTANCE = new Singleton2();
    private Singleton2() {}
    public static Singleton2 getInstance() {
        return INSTANCE ;
    }
}
```

Advantages :
- Flexibility in the API to change singleton
- Can write `generic singleton factory`
- The method reference can be used as a supplier, for example `Singleton2::instance` is a `Supplier<Singleton2>`.

#### Generic Singleton factory pattern

```java
public class GenericSingletonFactory<T> {

    // Generic Singleton factory pattern
    private static UnaryOperator<Object> IDENTITY_FUNCTION =(t) -> t ;

    @SuppressWarnings("unchecked")
    public static <T> UnaryOperator<T> identityFunction() {
        return (UnaryOperator<T>) IDENTITY_FUNCTION;
    }
}
```

A simple example with `String` and `Number`:

```java
public static void main(String[] args) {
    String[] values = new String[] {"a","b"} ;
    UnaryOperator<String> sameString = identityFunction();
    for(String value : values)
        System.out.println(sameString.apply(value));

    Number[] numbers = new Number[] {1,2.0,3L} ;
    UnaryOperator<Number> sameNumber = identityFunction();
    for(Number number: numbers)
        System.out.println(sameNumber.apply(number));
}
```

The output of this program would be :
```
a
b
1
2.0
3
```

### Serializable Singleton

It's not sufficient to add `implements Serializable` to the declaration to maintain Singleton guarantee.

You should :
- declare all the field `transient`
- provide `readResolve` method which return the instance, otherwise, each time a serialized instance is deserialized, a new object is created.


### Tips

To defend against attack using `java.lang.reflect.AccessibleObject.setAccessible` method which invokes reflectively `private` constructor using , you can make the private constructor throw an exception if it's asked to create a second instance.

### Enum Singleton

A third way to implements singleton is to create a single-element enum :
```java
public enum EnumSingleton {
    INSTANCE ;

    public void doSomething() {}
}
```

Advantages :
- Concise
- Serializable for free
- Guarantee against multiple instantiations, reflexions ...


__The Enum Singleton is the best way to implements singleton__, but you can't use this approach if the singleton must extend a superclass other than enum.

# Item 4 : Enforce noninstantiability with a private constructor

When writing classes that are just a grouping of `static` method and fields, it is recommended to make them non instantiable.

```java
public class UtilityClass {
  // Suppress default constructor
  private UtilityClass() {
    throw new AssertionError() ;
  }

  // Some static methods ...  
}
```

This pattern guarantees that this class will never be instantiated, and prevents this class of being subclassed.

# Item 5 : Prefer dependency injection to hardwiring resources

Many classes depend on one or more underlying resources, like the following example :

```java
public class Service {
    private static final Helper helper = new Helper() ;
    private Service() {}
    public static Integer compute() {
      // Some Business code ...
    }
}
```

It's not uncommon to have `Singleton` :

```java
public class ServiceSingleton {
    private final Helper helper = new Helper() ;
    private ServiceSingleton() {}
    public static ServiceSingleton instance = new ServiceSingleton() ;
    public Integer compute() {
      // Some Business code ...
    }
}
```

In this case, we are assuming that there is only one `Helper` worth using. What is required is __the ability to support multiple instances__ of the class `Helper` in this example.

A simple patterns that satisfies this requirement is `Dependency Injection` :

```java
public class Service {
    private final Helper helper ;
    private Service(Helper helper) {
      this.helper = helper ;
    }
    public Integer compute() {
      // Some Business code ...
    }
}
```

Dependency Injection provides flexibility, testability and reusability.

A useful variant of this pattern is to pass resource `factory` to the constructor :

```java
public class Service {

    private final Helper helper ;

    public Service(Supplier<? extends Helper> helperFactory) {
        this.helper = helperFactory.get() ;
    }

    private static class Helper {}

    private static class Helper2 extends Helper {}

    private static class HelperFactory {
        static Helper createHelper() {
            return new Helper() ;
        }

        static Helper2 createHelper2() {
            return new Helper2() ;
        }
    }

    public static void main(String[] args) {
        Service service1 = new Service(Helper::new) ;
        Service service2 = new Service(HelperFactory::createHelper) ;
        Service service22 = new Service(HelperFactory::createHelper2) ;
    }

}
```

There are many dependency injection frameworks :
- [Dagger](https://square.github.io/dagger/) for Java & Android
- [Guice](https://github.com/google/guice) (pronounced 'juice') is a lightweight dependency injection framework for Java 6 and above, brought to you by Google.
- [Spring](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-spring-beans-and-dependency-injection.html)


# Item 6 : Avoid creating unnecessary objects

An object can be reused if it is immutable :
```java
    String doNotDoThat = new String("Do not do that");
    String immutableString = "Immutable String" ;
 ```

 When invoking `String` constructor, millions of `String` instances ca be created needlessly.

 We can often avoid creating unnecessary objects by using static factories such as `Boolean.valueOf(String)`, which is preferable than `Boolean(String)`.

 Some objects creation is sometimes expensive, like `Pattern` compilation. It's better to create a `Pattern` and cache it, rather than using `String.match` method. `String.match` is the easiest way to check if a string matches a regular expression, but, it is not suitable for repeated use in performance-critical situations.

 Another way to create unnecessary objects is __autoboxing__. Let's take the following example :
 ```java
 public static void main(String[] args) {

     long start = System.currentTimeMillis();
     Long sum = 0L ;
     for(long i = 0 ; i <=Integer.MAX_VALUE; i++)
         sum+=i ;
     System.out.println((System.currentTimeMillis()-start) + " ms with autoboxing") ;


     start = System.currentTimeMillis();
     long sum2 = 0L ;
     for(long i = 0 ; i <=Integer.MAX_VALUE; i++)
         sum2+=i ;
     System.out.println((System.currentTimeMillis()-start) + " ms without autoboxing") ;

     start = System.currentTimeMillis();
     sum = LongStream.range(0L,Integer.MAX_VALUE).sum() ;
     System.out.println((System.currentTimeMillis()-start) + " ms with stream") ;

 }
 ```

 The output of this program :
 ```
     5474 ms with autoboxing
     689 ms without autoboxing
     844 ms with stream
 ```

 The variable `sum` is declared as `Long` instead of `long`, which means that the program constructs about 2<sup>31</sup> unnecessary `Long` instances.

 The lesson is clear : __Prefer primitives to boxed primitives, and watch out for unintentional autoboxing__.

 The construction of small objects whose constructors do little explicit work, to enhance clarity and simplicity is generally good thing.

 Conversely, avoiding object creation by maintaining your own object pool is a bad idea, unless the objects in the pool are extremely heavyweight, such as database connection; In fact, maintaining your object pool :
 - clutters the code
 - increase memory footprint
 - harms performances

# Item 7 : Eliminate obsolete object references
Memory leaks in garbage-collected languages (known also as __unintentional object retention__) are insidious. If an object reference is unintentionally retained, not only is that object excluded from garbage collection, but so too are any object referenced by this object.

The fix for this sort of problem is simple : null out references once they become obsolete.

__Nulling out object references should be the exception rather than the norm__. The best way to eliminate an obsolete reference is to let the variable that contains the reference fall out of scope. that's occurs naturally when each variable is defined in the narrowest possible scope.

Another common source of memory leaks is __caches__. Once you put an object reference into a cache, it's easy to forget that it's there. To handle that, represent the cache as `WeakHashMap`; entries will be removed automatically after they become obsolete. Remember that the desired lifetime of cache entries is determined by external references.

```java
public class WeakRefCache {

    static class BigData{}
    static class BigDataKey{
        private final String key ;
        BigDataKey(String key) {
            this.key = key;
        }
    }


    public static void main(String[] args) throws InterruptedException {

        WeakHashMap<BigDataKey, BigData> cache = new WeakHashMap<>() ;
        // Put element in cache
        BigDataKey key = new BigDataKey("key");
        cache.put(key,new BigData());
        System.out.println("elements count : " +cache.size());
        // remove key
        key = null ;
        System.gc();
        //check cache is empty
        Thread.sleep(1000);
        System.out.println("elements count : " +cache.size());
    }
}
```

The output of this program :
```
elements count : 1
elements count : 0
```

A third source of memory leaks is listeners and other callbacks. To ensure that callbacks are garbage collected promptly, store weak references to them.
```java
public static void main(String[] args) throws InterruptedException {

    WeakReference<BigData> reference = new WeakReference<>(new BigData()) ;
    System.out.println("Reference is present : " + (reference.get() != null) );
    System.gc();
    Thread.sleep(1000);
    System.out.println("Reference is present : " + (reference.get() != null) );
}
```

The output of this program :
```
Reference is present : true
Reference is present : false
```

To investigate about memory leaks, you can use debugging tools like `Heap profiler`.
