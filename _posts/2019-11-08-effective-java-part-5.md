---
layout: post
title: "Effective Java - 3rd Edition - Enums and Annotations"
date: 2019-11-12
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 34 : Use Enums instead of `int` constants
An enumerated type is a type whose legal values consist on fixed set of constants.

The following pattern (int enum pattern) has many shortcomings. It provides noting in the way of type safety and little in the way of expressive power.
```java
    private static final int APPLE = 755;
    private static final int ORANGE = 673;
```

The `enum` type helps to avoid those shortcomings. To associate data with `enum` constants, declare instance fields and write a constructor that takes the data and stores it in the fields.
```java
enum Fruits{

    APPLE(755),ORANGE(673) ;

    private final int value ;

    Fruits(int value) {
        this.value = value;
    }
}
```

## Enum properties
- Enum are classes that export one instance of each enumeration constant via `public static final` field, They are a generalization of singletons which are essentially single-element enums.
- It is possible to use `==` operator to compare `enum`.
- Enum types with identically named constants coexist peacefully because each type has its own namespace
- You can add or reorder constants in `enum` without recompiling its client code
- You can translate enums into printable strings by calling their `toString` method.
- You can add arbitrary methods and fields and implements Interfaces
- Provides high quality implementation of `Object` method
- Implements `Comparable` and `Serializable`

## Constant specific method
Suppose you are writing an `enum` that represents the basic operations :
```java
public enum Operation {
    PLUS, MINUS, TIMES, DIVIDE;

    public double apply(double a, double b) {
        switch (this) {
            case PLUS:
                return a + b;
            case MINUS:
                return a - b;
            case TIMES:
                return a * b;
            case DIVIDE:
                return a / b;
        }
        throw new AssertionError("Not handled operation : " + this);
    }
}
```

The code works but it is not so pretty. Likely, there's a better way to associate different behavior with each `enum` constant.
```java
public enum Operation {
    PLUS {
        @Override
        public double apply(double a, double b) {
            return a + b;
        }
    },

    MINUS {
        @Override
        public double apply(double a, double b) {
            return a - b;
        }
    },
    TIMES {
        @Override
        public double apply(double a, double b) {
            return a * b;
        }
    },

    DIVIDE {
        @Override
        public double apply(double a, double b) {
            return a / b;
        }
    };


    public abstract double apply(double a, double b);
}
```

if you add a new behavior, you will provide an apply method.

An other solution is to use `Function`.
```java
    public enum Operation {

        PLUS ((a,b) -> a + b),
        MINUS ((a,b) -> a - b),
        TIMES ((a,b) -> a * b),
        DIVIDE ((a,b) -> a / b);

        private final BiFunction<Double,Double,Double> operation ;

        Operation(BiFunction<Double,Double,Double> operation) {
            this.operation = operation;
        }

        public Double compute(Double x, Double y) {
            return operation.apply(x,y);
        }
    }
```

Enums are generally speaking, comparable in performance to `int` constants. A minor performance disadvantage of enums is that there is a space and time cost to load and initialize enum types, but it is unlikely to be noticeable in practice.

Use enums any time you need a set of constants whose members are known at compile time.

# Item 35 : Use instance fields instead of ordinals

Many enums are naturally associated with a single `int` value. All `enum` have `ordinal` method which returns the numerical position of each enum constant in its type.
```java
public enum Numbers {

    ONE, TWO, THREE ;

    public int value() {
        // Do not do this !!
        return ordinal() ;
    }
}
```

Let's print values
```java
    public static void main(String[] args) {
        Arrays.stream(Numbers.values()).map(n -> n+" -> "+n.value()).forEach(System.out::println);
    }
```

The output is
```
ONE -> 0
TWO -> 1
THREE -> 2
```

This enum works, but, it is a maintenance nightmare. If constants are reordered, the `value` method will break. The solution is to store instance field.
```java
public enum Numbers {

    FOUR(4),ONE(1), TWO(2), THREE(3);

    private final int value ;

    Numbers(int value) {
        this.value = value;
    }

    public int valueOrdinal() {
        return ordinal() ;
    }

    public int valueStored() {
        return value ;
    }
}
```

The output :
```
Ordinal
FOUR -> 0
ONE -> 1
TWO -> 2
THREE -> 3

Stored
FOUR -> 4
ONE -> 1
TWO -> 2
THREE -> 3
```

Never derive a value associated with enum from its ordinal.

# Item 36 : Use `EnumSet` instead of bit fields

A bit field representation lets you use bitwise `OR` operation to combine several constants into a set, know as `bit field`.
```java
public class Item36 {

    private static final int UPPER_CASE = 1 << 0; // 1
    private static final int LOWER_CASE = 1 << 1; // 2
    private static final int TRIM = 1 << 2; // 4


    public static String format(String value, int flags) {
        String result = value;
        if ((flags & UPPER_CASE) == UPPER_CASE) result = result.toUpperCase();
        if ((flags & LOWER_CASE) == LOWER_CASE) result = result.toLowerCase();
        if ((flags & TRIM) == TRIM) result = result.trim();
        return result;
    }

    public static void main(String[] args) {
        System.out.println(format("to upper case ", UPPER_CASE)+"--");
        System.out.println(format("to upper case ", UPPER_CASE | TRIM)+"--");
        System.out.println(format("to Lower case ", LOWER_CASE)+"--");

    }
}
```

Output of this code
```
TO UPPER CASE --
TO UPPER CASE--
to lower case --
```

Use `EnumSet` to efficiently represent set of values drawn from a single enum type. Here is how the previous example looks when modified to use enums and `EnumSet` :
```java
public class Item36 {

    enum Operation {
        UPPER_CASE,LOWER_CASE,TRIM;
    }

    public static String format(String value, Set<Operation> flags) {
        String result = value;
        if (flags.contains(Operation.UPPER_CASE)) result = result.toUpperCase();
        if (flags.contains(Operation.LOWER_CASE)) result = result.toLowerCase();
        if (flags.contains(Operation.TRIM)) result = result.trim();
        return result;
    }

    public static void main(String[] args) {
        System.out.println(format("to upper case ", EnumSet.of(Operation.UPPER_CASE))+"--");
        System.out.println(format("to upper case ", EnumSet.of(Operation.UPPER_CASE,Operation.TRIM))+"--");
        System.out.println(format("to Lower case ", EnumSet.of(Operation.LOWER_CASE))+"--");
    }
}
```

Note that the method `format` takes `Set<Operation>` parameter rather than `EnumSet`. The `main` mehtod is calling `format` with `EnumSet.of(...)`.

The main disadvantage of `EnumSet` is that it is not possible to create immutable `EnumSet`. But, it will be remedied in an upcoming java release. Otherwise, you can wrap an `EnumSet` in `Collections.unmodifiableSet`.

# Item 37 : Use `EnumMap` instead of ordinal indexing
Given the following class :
```java
    enum Type {TUNISIAN, FRENCH}

    static class Restaurant {
        String name;
        Type type;

        Restaurant(String name, Type type) {
            this.name = name;
            this.type = type;
        }
    }
```

Suppose you have a `List<Restaurant>` and you want to list restaurants organized by type. There is a very fast `Map` implementation designed for use with enum keys which is `EnumMap`.
```java
    List<Restaurant> restaurants = Lists.newArrayList(new Restaurant("A", Type.TUNISIAN), new Restaurant("B", Type.TUNISIAN), new Restaurant("C", Type.FRENCH));
    // Use enum map
    EnumMap<Type, Set<Restaurant>> restaurantByType = Maps.newEnumMap(Type.class);
    // Fill all possible keys
    for (Type type : Type.values()) restaurantByType.put(type, Sets.newHashSet());
    // add objects
    for (Restaurant restaurant : restaurants) restaurantByType.get(restaurant.type).add(restaurant);
```

The previous program can be further shortened by using `stream` to manage the map :
```java
    Map<Type, List<Restaurant>> byType = restaurants.stream().collect(Collectors.groupingBy(Restaurant::getType));
```

In this case, the code chooses it own map implementation (`class java.util.HashMap` in my case), and in practice, it won't be an `EnumMap`. to rectify this problem, use :
```java
    EnumMap<Type, Set<Restaurant>> collectToEnumMap = restaurants.stream().collect(Collectors.groupingBy(Restaurant::getType, () -> new EnumMap<>(Type.class), Collectors.toSet()));
```

Note that the `EnumMap` makes a nested map for each `Type` in the first case (`restaurantByType`), while the stream-based versions only make a nested map of existing types.


## Array of arrays
You may have seen an array indexed (twice!) by ordinals sued to represent a mapping from two enums.

Let's take the example of transitions

| Phase       |     SOLID    |        LIQUID |
| :------------ | :------------- | :------------- |
| SOLID       |     null     |        MELT |
| LIQUID     |   FREEZE    |      null |

An Array of arrays code will be like that :
```java
    public enum Phase {
        SOLID, LIQUID ;

        enum Transition {
            MELT, FREEZE ;

            private static final Transition[][] TRANSITIONS = {
                    {null,MELT},
                    {FREEZE,null}
            } ;

            public static final Transition from (Phase from, Phase to) {
                return TRANSITIONS[from.ordinal()][to.ordinal()] ;

            }
        }


        public static void main(String[] args) {
            System.out.println(Transition.from(Phase.LIQUID,Phase.SOLID)); // prints FREEZE
            System.out.println(Transition.from(Phase.SOLID,Phase.LIQUID)); // prints MELT
        }

    }
```

This program works, but the compiler have no way of knowing the relationship between ordinals and array indices. If you make a mistake in the transition table or forget to update it, your program will fail in runtime.

You can do much better with `EnumMap` :
```java
    enum TransitionWithMap {
        MELT(SOLID, LIQUID), FREEZE(LIQUID, SOLID);

        static {
            init() ;
        }

        private final Phase from;
        private final Phase to;
        private static EnumMap<Phase, EnumMap<Phase, TransitionWithMap>> transitions;



        TransitionWithMap(Phase from, Phase to) {
            this.from = from;
            this.to = to;
        }

        public Phase getFrom() {
            return from;
        }

        public Phase getTo() {
            return to;
        }

        private static void init() {
            transitions = Arrays.stream(TransitionWithMap.values()).collect(
                    groupingBy(
                            t -> t.from,
                            () -> new EnumMap<>(Phase.class),
                            toMap(t -> t.to, v -> v, (x, y) -> y, () -> new EnumMap<>(Phase.class)))
            );
        }

        public static TransitionWithMap from(Phase from, Phase to) {
            return transitions.get(from).get(to) ;
        }

    }
```

The code initialize `EnumMap<Phase, EnumMap<Phase, TransitionWithMap>>` which contains transition by Phase by Phase.

Suppose now that you want to add a new `Phase` to the system. Updating array-based program is risky. Ti update enum-based version, all you have to do is add elements in `TransitionWithMap`, and the program takes care of everything.

# Item 38 : Emulate extensible enums with interfaces
It is not possible to have on e enumerate type extend another. There is at least one compelling use case for extensible enumerated types, which is __operation codes__, also known as __opcodes__. An opcode is an enumerated type whose elements represent operations on some machine, such as `Operation` in the following example :
```java
    interface Operation {
        public Double compute(Double x, Double y);
    }
    public enum BasicOperation implements Operation {

        // Some code ... see Item 34

        public Double compute(Double x, Double y) {
            return operation.apply(x,y);
        }
    }
```

While enum type `BasicOperation` is not extensible, the interface type `Operation` is, and it is the interface type that is used to represent operations in the APIs :
```java
    Operation plus = BasicOperation.PLUS ;
    System.out.println(plus.compute(1d,2d));
```

Not only is it possible to pass a single instance of an *extension enum* anywhere a *base enum* is expected, but it is possible to pass in an entire extension enum type and use its elements in addition to or instead of those of the base type:
```java
    static <T extends Enum<T> & Operation> void genericCompute (Class<T> opEnumtype, double x, double y) {
        for (Operation op : opEnumtype.getEnumConstants()) {
            System.out.println(op + " : " + op.compute(x,y));
        }
    }

    public static void main(String[] args) {
        genericCompute(BasicOperation.class,1,2);
    }
```

The output is
```
PLUS : 3.0
MINUS : -1.0
TIMES : 2.0
DIVIDE : 0.5
```

The `T extends Enum<T> & Operation` ensures that the type `T` represent both `enum` and `Operation`.

A second alternative is to use bounded wildcard type :
```java
    static void wildCardCompute (Collection<? extends Operation> ops, double x, double y) {
        for (Operation op : ops) {
            System.out.println(op + " : " + op.compute(x,y));
        }
    }

    public static void main(String[] args) {
        wildCardCompute(Lists.newArrayList(BasicOperation.values()),1,2);
    }
```

A minor disadvantage of the use of interface to emulate extensible enums is that implementations cannot be inherited from one enum type to another.

# Item 39 : Prefer annotations to naming patterns
Historically, it was common to use *naming pattern* to indicate that a some program elements demanded special treatment by a tool or a framework. For example, Junit 4 required test method beginning with `test`.

this technique has many disadvantages :
- Typographical error result in silent failures
- No way to ensure that they are used only on appropriate program elements
- Do not provide a good way to associate parameter values with program elements

Annotations solve all this problems nicely. Generally, annotations don't change the semantics of the annotated code that enable it for special treatment by tools such as this simple test runner :
```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    @interface MyAnnotation{}

    public class Item39 {

        @MyAnnotation
        static void methodWithAnnotation() {
            System.out.println("With Annotation");
        }

       static void methodWithoutAnnotation() {
            System.out.println("No Annotation");
        }

        public static void main(String[] args) throws InvocationTargetException, IllegalAccessException {
            for (Method m : Item39.class.getDeclaredMethods()) {
                System.out.println(m.getName() + " " +m.isAnnotationPresent(MyAnnotation.class) );
                if (m.isAnnotationPresent(MyAnnotation.class)) {
                    m.invoke(null) ;
                }
            }
        }
    }

```

the output
```
main false
methodWithoutAnnotation false
methodWithAnnotation true
With Annotation
```

The `@Retention(RetentionPolicy.RUNTIME)` indicates that the annotation should be retained at runtime. The `@Target(ElementType.METHOD)` indicates that the annotation is legal only on method declaration.

## Annotations with parameter
Annotation type can have parameters :
```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    @interface MyAnnotation{
        Class<? extends Exception> value() ;

    }

    public class Item39 {

        @MyAnnotation(ArithmeticException.class)
        static void methodWithAnnotation() {
            System.out.println("");
            int a = 1/0 ;
        }

        @MyAnnotation(IllegalAccessException.class)
        static void methodWithoutAnnotation() {
            throw new ArrayIndexOutOfBoundsException("Error");
        }

        public static void main(String[] args) throws InvocationTargetException, IllegalAccessException {
            for (Method m : Item39.class.getDeclaredMethods()) {
                if (m.isAnnotationPresent(MyAnnotation.class)) {
                    Class<? extends Exception> expectedException = m.getAnnotation(MyAnnotation.class).value();
                    try {
                        m.invoke(null);
                    } catch (Throwable e) {
                        if (expectedException.isInstance(e.getCause())) {
                            System.out.println("Expected exception " + e.getCause().getClass());
                        } else {
                            System.out.println("Not expected exception " + e.getCause().getClass());
                        }
                    }

                }
            }
        }
    }
```

## Muti valued annotations
It is possible to have annotation with an array of parameters
```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    @interface MyAnnotation {
        Class<? extends Exception>[] values();
    }
```

Another way to do that is to annotate the annotation with `@Repeatable` to indicate that the annotation my be applied repeatedly to a single element.
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Repeatable(AnnotationContainer.class)
@interface MyAnnotation {
    Class<? extends Exception> value();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface AnnotationContainer {
    MyAnnotation[] value() ;
}
```

# Item 40 : Consistently use the `Override` annotation
The most important annotation of Java libraries is `@Override`. If you consistently use this annotation, it will protect you from a large class of nefarious bugs.

Let's take the following example :
```java
public class Person {

    private String name ;

    public Person(String name) {
        this.name = name;
    }

    public boolean equals(Person person) {
        return Objects.equals(name, person.name);
    }

    public int hashCode() {
        return Objects.hash(name);
    }

    public static void main(String[] args) {
        HashSet<Person> people = Sets.newHashSet(new Person("A"), new Person("A"), new Person("A"));
        System.out.println(people.size());
    }
}
```

The program print `3` as size of the people `set`. Why ?

It is because the `equals` method of `Object` has signature `equals(Object o)`. In this case, adding `@Override` annotation will help to detect this bug.

Therefore, you should use the operator `@Override` annotation on every method declaration that you believe you override a superclass declaration.

If you enable the appropriate check, your IDE can generate a warning if you have a method that doesn't have an `@Override` annotation but does override a super class method.

# Item 41 : Use marker interface to define types
A __Marker interface__ is an interface that contains no method declaration but merely designates (or marks a class that implements the interface as having some property; For example `Serializable`.

Marker interfaces have 2 advantages over marker annotation :
- Marker Interface define a type that is implemented by instances of the market class; marker annotations do not. You can then catch error at compile time.
- Marker Interface can be targeted more precisely

The chief advantage of __marker annotations__ is that they are part of the larger annotation facility.

You must use an annotation if the marker applies to any program element other than a class or interface. If the marker applies only class or interfaces, ask yourself : *"Might I want to write one or more methods that accept only objects that have this marking ?"*. If so, you should use marker interface.
