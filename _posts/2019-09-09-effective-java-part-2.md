---
layout: post
title: "Effective Java - 3rd Edition - Methods Common to All Objects"
date: 2019-10-22
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 10 : Obey the general contract when overriding equals

Overriding `equals` method seems simple, but, there are many ways to get it wrong.

The easiest way to avoid problems is not to override the `equals` method, in which case each instance of the class is equals only to itself. Use this when
- Each instance of the class is unique (Eg `Thread`)
- There's no need to provide logical equality test
- A super class has already overridden equals, and the super class behavior is appropriate for this class
- You are certain that the equals method is never invoked (case of private class)

It's then appropriate to override equals when the class has a notion of `logical equality`.

One kind of class does not require the `equals` method to be overridden is singleton class. `Enum` types fall into this category.

## Equals method contact
- Reflexive : `x.equal(x)` return `true`
- Symmetric : if `x.equals(y)` return `true` then `y.equals(x)` return `true`
- Transitive : if `x.equals(y)` return `true` and `y.equals(z)` return `true` then `x.equals(z)` return `true`
- Consistent : Multiple invocation of `x.equals(y)` return the same boolean
- Non-nullity : For any non null x, `x.equals(null)` must return `false`

## A recipe of high quality equals
- Use the `==` operator to check if the argument is a reference of this object
- Use the `instanceOf` operator to check if the argument has the correct type
- Cast the argument to the correct type
- Check the __significant__ fields of argument and object match. To avoid `NullPointerException`, use `Objects.equals(Object,Object)` method.

The performance of equals method can be affected by the order in which the fields are compared. For best performance, you should first compare fields that are more likely to differ.

## Final caveats
- Always override `hashCode` when you override `equals`
- Don't try to be too clever
- Do not substitute to another type for Object in the `equals` declaration.

IDE have facilities to generate `equals` and `hashCode` methods, but the resulting source code is verbose and hardly readable. It's generally a preferable solution.

# Item 11 : Always override hashCode when you override equals
You must override `hashCode` in every class that overrides `equals`. Equals object must have the same hash code.

The `hashCode` contract is :
- The hash code is consistent
- If two objects are equals, then their hash codes must be the same.
- if two objects are not equals, then, it's not required that their hash code are same.

## `hashCode` recipe :
- A good hash function tends to produce different hash code for unequals instances.
  - declare int variable named `result`, and init the initialize it to the hashCode of the most significant field of your object
  - for every remaining field
    - if primitive type, call `hashCode()` method of Boxed type.
    - if the field is an object reference, invoke `hashCode`
    - if null, use `0`
    - if the field is an `array`, treat it as if each significant element were a separate field, and then combine all the values. if all elements are significant, use `Arrays.hashCode`.
  - Combine the hash code `result = 31 * result + c`
- You must exclude fields that are not used in `equals`
- You can ignore __derivative fields__, that are computed from fields already included in computing `hashCode`.
- Do not be tempted to exclude significant fields to improve performance.

You can use `com.google.common.hash.Hashing` class to compute hash code. This method lets you write one line `hashCode` method. Unfortunately, they run more slowly.

If the object is immutable and the cost of computing hash code is significant, you may consider caching the hash code in the object. You might also choose to __lazy initialize__ the hash code the first time `hashCode` is invoked.

## Item 12 : Always override `toString`
Providing a good `toString` implementation makes your class much more pleasant to use and makes the debug easier.

The `toString` method should return all the interesting information contained in the object.

One important decision you will have to make is whether to specify the format of the return value :
- it's recommended for __value class__ : specifying the format gives a better human-readable object representation.
- if you specify :
  - you are stuck with it for life; Programmers will write code to parse the representation.
  - you should clearly document you intention.

Whether or not you specify the format, provide programmatic access to the information contained in the value returned by `toString`.

## `toString` recommendations
- It makes no sense to write `toString` in utility Classes
- No need to write `toString` in `enum`. Java already provides a perfect one.
- Write `toString` in `abstract` class whose subclasses share a common string representation.

## Alternatives
- Google's open source [AutoValue](https://github.com/google/auto) facility, will generate a `toString` method.
- [Lombok](https://projectlombok.org)

# AutoValue
[AutoValue](https://github.com/google/auto) is a source code generator for Java, and more specifically it's a library for generating source code for value objects or value-typed objects.

In order to generate a value-type object all you have to do is to annotate an abstract class with the `@AutoValue` annotation and compile your class. What is generated is a value object with accessor methods, parameterized constructor, properly overridden `toString()`, `equals(Object)` and `hashCode()` methods.

[Source](https://www.baeldung.com/introduction-to-autovalue).

Before `AutoValue`
```java
    import java.util.Objects;

    public class Person {
        private String name ;
        private int age ;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Person person = (Person) o;
            return age == person.age &&
                    Objects.equals(name, person.name);
        }

        @Override
        public int hashCode() {
            return Objects.hash(name, age);
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }
    }
```

After :
```java
    import com.google.auto.value.AutoValue;

    @AutoValue
    public abstract class AVPerson {

        public static AVPerson of(String name, int age) {
            return new AutoValue_AVPerson(name,age) ;
        }

        public abstract String name() ;
        public abstract int age() ;
    }
```

The `AutoValue` API generates a class `AutoValue_AVPerson` in the generated source. You should build your code to have the generated class :
```java
    // Generated by com.google.auto.value.processor.AutoValueProcessor
    final class AutoValue_AVPerson extends AVPerson {

      private final String name;

      private final int age;

      AutoValue_AVPerson(
          String name,
          int age) {
        if (name == null) {
          throw new NullPointerException("Null name");
        }
        this.name = name;
        this.age = age;
      }

      @Override
      public String name() {
        return name;
      }

      @Override
      public int age() {
        return age;
      }

      @Override
      public String toString() {
        return "AVPerson{"
             + "name=" + name + ", "
             + "age=" + age
            + "}";
      }

      @Override
      public boolean equals(Object o) {
        if (o == this) {
          return true;
        }
        if (o instanceof AVPerson) {
          AVPerson that = (AVPerson) o;
          return (this.name.equals(that.name()))
               && (this.age == that.age());
        }
        return false;
      }

      @Override
      public int hashCode() {
        int h$ = 1;
        h$ *= 1000003;
        h$ ^= name.hashCode();
        h$ *= 1000003;
        h$ ^= age;
        return h$;
      }

    }
```

# Item 13 : Override `clone` judiciously
`Cloneable` iterface has no method; what does it do ?
```java
    package java.lang;
    public interface Cloneable {
    }
```
If a class implements `Cloneable`, `Object`'s `clone` method returns a field-by-field copy of the object. In practive, a class implementing `Cloneable` is expected to provide a properly functioning public `clone` method.

The contract of `clone` in `Object` specification is weak :
- `x.clone() != x` return `true`
- `x.getClass() == x.clone().getClass()` return `true`
- Invoking Object's clone method on an instance that does not implement the `Cloneable` interface results in the exception `CloneNotSupportedException` being thrown.
- `x.clone().equals(x)` return `true` __is not an absolute requirement__.

Let's take an example :
```java
    public class Person implements Cloneable {
        private String name ;
        private int age ;

        public Person clone() {
            try {
                return (Person) super.clone();
            }catch (CloneNotSupportedException e) {
                throw new AssertionError() ;
            }
        }
    }
```

`clone` method :
- override `protected` method, and make it `public`
- Java supports __covariant return types__, then, return `Person` and not `Object` in the overriden mtheod
- `Object` method declared throwing checked exception `CloneNotSupportedException`, so use `try`-`catch`.

When having mutable objects, the easiest way is to call `clone` recursively of list's elements.

With bad clone
```java
    public class Population implements Cloneable{

        private List<Person> persons ;

        @Override
        public Population clone() {
            try {
                return (Population) super.clone();
            } catch (CloneNotSupportedException e) {
                throw new AssertionError() ;
            }
        }

        public static void main(String[] args) {
          Population population = new Population();
          population.persons = new Person[] {Person.builder().age(5).name("A").build(), Person.builder().age(15).name("B").build()};
          System.out.println("Original " + population + " --> " + population.persons);
          Arrays.stream(population.persons).forEach(System.out::println);
          // Clone
          Population clone = population.clone();
          System.out.println("clone " + clone +" --> " + clone.persons);
          Arrays.stream(clone.persons).forEach(System.out::println);
        }
      }
    }
```

In the output, its the same arrays instances used in the `population` clone.
```
    Original part2.Population@340f438e --> [Lpart2.Person;@30c7da1e
    part2.Person@ba5
    part2.Person@bce
    clone part2.Population@19dfb72a --> [Lpart2.Person;@30c7da1e
    part2.Person@ba5
    part2.Person@bce
```

With recursive clone :
```java
    @Override
    public Population clone() {
        try {
            Population result = (Population) super.clone();
            result.persons = this.persons.clone();
            return result ;
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
```

So that, a new arrays is created :
```
    Original part2.Population@340f438e --> [Lpart2.Person;@30c7da1e
    part2.Person@ba5
    part2.Person@bce
    clone part2.Population@19dfb72a --> [Lpart2.Person;@17c68925
    part2.Person@ba5
    part2.Person@bce
```

A better approach of object copying is to provide a __copy constructor__ or __copy factory__ :
- Don't rely on object creation mechanism
- No conflict with final fields
- Do not throw unnecessary unchecked exception
- A copy factory can have interface as parameter. Interface-based copy constructor whose argument if of type `Collection` or `Map` are known as __conversion constructors__ or __conversion factories__, allow the client to choose implementation type of the copy, rather than forcing the client to accept the implementation of the original.

```java
    public class Population implements Cloneable {

        private Person[] persons;

        public Population(){}

        public static Population copy(Population other) {
            Population copy = new Population() ;
            copy.persons = Arrays.copyOf(other.persons,other.persons.length) ;
            return copy ;
        }

        public Population(Population other) {
            this.persons = Arrays.copyOf(other.persons,other.persons.length) ;
        }

        @Override
        public Population clone() {
            try {
                Population result = (Population) super.clone();
                result.persons = this.persons.clone();
                return result ;
            } catch (CloneNotSupportedException e) {
                throw new AssertionError();
            }
        }

        public static void main(String[] args) {
            Population population = new Population();
            population.persons = new Person[] {Person.builder().age(5).name("A").build(), Person.builder().age(15).name("B").build()};
            System.out.println("Original " + population + " --> " + population.persons);
            Arrays.stream(population.persons).forEach(System.out::println);
            // Clone
            Population clone = population.clone();
            System.out.println("clone " + clone +" --> " + clone.persons);
            Arrays.stream(clone.persons).forEach(System.out::println);
            // copy constructor
            Population copyConst = new Population(population);
            System.out.println("Copy Constructor " + clone +" --> " + copyConst.persons);
            Arrays.stream(copyConst.persons).forEach(System.out::println);
            // static factory
            Population factoCopy = Population.copy(population) ;
            System.out.println("Copy Constructor " + clone +" --> " + factoCopy.persons);
            Arrays.stream(factoCopy.persons).forEach(System.out::println);
        }
      }
    }
```

The output :
```
    Original part2.Population@340f438e --> [Lpart2.Person;@30c7da1e
    part2.Person@ba5
    part2.Person@bce
    clone part2.Population@19dfb72a --> [Lpart2.Person;@17c68925
    part2.Person@ba5
    part2.Person@bce
    Copy Constructor part2.Population@19dfb72a --> [Lpart2.Person;@3d24753a
    part2.Person@ba5
    part2.Person@bce
    Copy Constructor part2.Population@19dfb72a --> [Lpart2.Person;@7a0ac6e3
    part2.Person@ba5
    part2.Person@bce
```

## Take away
- `Cloneable` architecture is incompatible with normal use of `final` fields referring to mutable objects.
- No need to create `clone` method for immutable objects.
- Public `clone` method should omit the throws clause
- All classes that implements `Cloneable` should override `clone` method with a public method whose return type is the class itself.
- Prefer copy constructor or copy factory
