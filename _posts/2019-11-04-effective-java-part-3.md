---
layout: post
title: "Effective Java - 3rd Edition - Classes and interfaces"
date: 2019-11-04
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 15 : Minimize the accessibility of classes and members
A well designed component hides all its implementation details, cleanly separating its API from its implementation. this concept know as __information hiding__ or __encapsulation__ is a fundamental tenet of software design.

## Why is it important ?
- Decouples the components, so better isolation
- Eases the burden of maintenance
- Better profiling for performance problems
- Decreases the risk of building huge systems

## Rules
- Make each class or member as inaccessible as possible.
- Make all the members private
- If a method overrides a superclass method, it cannot have more restrictive access level in the subclass than in the superclass.
- It is accessible to make a member `package-private` in order to test it
- Instance fields of public classes should rarely be `public`
- You can expose constants via `public static final` fields. Note that's wrong to have non zero length array as a constant, because it's always mutable. Prefer then using `Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES))`.

As of Java 9, there are two additional levels as part of the __module system__.

A module may export some of its packages. Public and protected members of unexported packages are inaccessible outside the module.

Using the module system allows you to share classes among packages within a module without making them visible to the entire world.

Public and protected members of public classes in unexported packages give rise to the two implicit access levels, which are intramodular analogues of the normal public and protected levels.

If you place a module's JAR on your application path, instead of its module path, the packages revert to their non-modular behavior.

See this [article](https://blog.soat.fr/2017/05/java-9-la-revolution-des-modules/).

# Item 16 : In public classes, use accessor methods, not public fields

If a class is accessible outside the package, provide accessor methods to preserve the flexibility of the class's internal representation. However, if a class is package-private or is nested class, there is nothing inherently wrong with exposing its data fields.

While it is never a good idea for a public class to expose fields directly, it is less harmful if the fields are immutable.
```java
    public class Person {

        public final String name;
        public final int age ;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
    }
```

# Item 17 : Minimize mutability

An immutable class is simply a class whose instances cannot be modified. All the information contained in each instance is fixed for the lifetime of the object, so, no changes can ever be observed.

To make a class immutable :
- Don't provide methods that modify the object state
- Make all fields `private final`
- Ensure exclusive access to any mutable component (eg : make defense copies)

Immutable objects :
- Simple : they have one state
- Thread-safe & can be shared freely
- No need to create defensive copies of them
- There's no possibility of a temporary inconsistency

An immutable objects can provide static factory that caches frequently requested instances to avoid creating new instances when existing ones would do. All the boxed primitive classes and `BigInteger` do this.

The major disadvantage of immutable classes is that they require a separate object for each distinct value. Creating this objects can be costly, especially if they are large.

The best approach to create immutable classes is to make constructor `private` or `package-private`, and add static factories.
```java
public class Person {

    private final String name;
    private final int age ;

    private Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public static Person create(String name,int age) {
        return new Person(name,age);
    }
}
```

If you choose to have your immutable class implements `Serializable` and it contains one or more fields that refers to mutable objects, you must provide an explicit `readObject` or `readResolve` method, or use `ObjectInputStream.readUnshared`.

## Take away
- Classes should be immutable unless there's a very good reason to make them mutable
- If a class cannot be immutable, limit mutability
- Declare every field `private final` unless there's a good reason to do otherwise
- Constructor should create fully initialized objects with all their invariants established.

# Item 18 : Favor composition over inheritance

__Inheritance__ is a powerful way to achieve code reuse. It is safe to use inheritance within the same package, but, it is dangerous to use inheritance cross packages. It is suitable when the relation between the 2 classes is `is-a` relationship.

Unlike method invocation, inheritance violates encapsulation; The subclass depends on the superclass implementation.

__Composition__ is giving the class a private field that references an instance of the existing class.
__Forwarding__ : Each instance method in the new class invokes the corresponding method on the contained instance :
```java

public class ForwardingSet<T> implements Set<T> {

    private final Set<T> s ;

    public ForwardingSet(Set<T> s) {
        this.s = s;
    }

    @Override
    public int size() {
        return s.size();
    }

    @Override
    public boolean isEmpty() {
        return s.isEmpty();
    }

    // Other methods ...
}
```

__Wrapper__ : The `InstrumentedSet` is know as wrapper because each `InstrumentedSet` instance contains another `Set` instance. This is also know as __Decorator__ pattern, because it decorates a set by adding instrumentation.
```java
public class InstrumentedSet<T> extends ForwardingSet<T> {

    private int count = 0 ;

    public InstrumentedSet(Set<T> s) {
        super(s);
    }

    @Override
    public boolean add(T t) {
        count ++ ;
        return super.add(t);
    }

    @Override
    public boolean addAll(Collection<? extends T> c) {
        count += c.size() ;
        return super.addAll(c);
    }

    public int getCount() {
        return count;
    }
}
```

Sometimes, the combination of Forwarding and Composition is loosely referred to __Delegation__. Technically, it's not delegation unless the wrapper object passes itself to the wrapped object.

For example, Guava provides forwarding classes for all the collection interface.

If you use inheritance where composition is appropriate, you needlessly expose implementation details, and the resulting API ties you to the original implementation.

# Item 19 : Design and document for inheritance or else prohibit it
The class must document its self-use of overridable methods. The documentation must indicate which overridable method the method invokes, in what sequence, and how the result of each invocation affects subsequent processing.The `@implSpec` tag was added in java 8.

## Advices
- A class may have to provide hooks into its internal working in the form of `protected` method.
- The only way to test a class designed for inheritance is to write subclass, and you should do it before you release it.
- The constructor must not invoke overridable methods, directly or indirectly.
- If you decide to implement `Cloneable` or `Serializable` :
  - neither `clone` nor `readObject` may invoke overridable methods, directly or indirectly.
  - if the class has `readResolve` or `writeReplace` method, make this method `protected`, otherwise, it will be silently ignored by the subclass.
- Prohibit subclassing in classes that are not designed and documented to be the safety subclassed. Otherwise, each time the class changes, there's a chance that subclasses extending the class will break.
  - Declare `final` class
  - or make all constructor `private` or `package-private` and add `public static` factories.

# Item 20 : Prefer interfaces to `abstract` classes

Since the introduction of `Default Method` in java 8, Interface and `abstract` classes allow you to provide implementation for instance method.

So :
- Existing classes can easily be retrofitted to implement a new interface.
- Interface are ideal for defining mixins. A __Mixin__ is a type that a class can implement in addition to its primary type to declare that it provides some optional behavior. Eg `Comparable, Cloneable ...`
- Interfaces allow for the construction of non hierarchical type frameworks. suppose we have `Singer`, and `SongWriter` interfaces. In real life, some singers are song writer. We could imagine interface `SingerSongWriter extends Singer, SongWriter`.
- Interface enable safe powerful functionality enhancement via the __wrapper class__. If `abstract` class defines types, you leave the programmer who want to add functionality with no alternative but inheritance.
- Consider providing obvious implementation in the form of `default` method, and be sure to document with `@SpecImpl`
- Consider using __Tempalte Method Pattern__ : Create an `abstract` skeletal implementation class which implements some interfaces (define the type), and implements the remaining non primitive interface. They are called `AbstractInterface`. For eg : `AbstractCollection`, `AbstractSet`... It would have sense to call them `SkeletalInterface`.

## How to write Skeletal ?
- Study the interface and decide which method are the primitives in abstract skeletal implementation
- Provide default method in the interface for all the method that can be implemented directly atop the primitives
- Do not provide default method for `Object` method
- Write a class implements this interface. The class may contains fields for the task.
- Good documentation is absolutely essential in Skeletal implementation

An interface is generally the best way to define a type that permits multiple implementations. Consider using Skeletal for non trivial interface.

# Item 21 : Design interfaces for posterity

After Java 8, adding a new default method in interface will not impact existing implementations of this interface, but, it is not always possible to write default method that maintains all invariants of every conceivable implementation. In presence of default methods, existing implementations of an interface may compile without error or warning, but fail at runtime.

So adding default methods should be avoided unless the need is critical. However, default methods are extremely useful for providing standard method implementation when the interface is created.

# Item 22 : Use interfaces only to define types

The interface serves as type that can be used to refer to instances of the class.The constant interface pattern is a poor use of interfaces.

You should use one of those recommendations :
- add them to the class or interface. For eg all boxed numerical primitives :
```java
    public final class Double extends Number implements Comparable<Double> {
        public static final double POSITIVE_INFINITY = 1.0D / 0.0;
        public static final double NEGATIVE_INFINITY = -1.0D / 0.0;
        // Other code ...
    }
```
- export them within enum type
- export the constants on noninstantiable utility class
```java
    public final class PartConstants {

        private PartConstants() {
            // Prevent instantiation
        }

        public static final String AA = "AA" ;
    }
```

# Item 23 : Prefer class hierarchies to tagged classes

Let's take an example of class with a tag field :
```java
    public class Figure {

        enum Shape {RECTANGLE, CIRCLE} ;
        private final Shape shape ;

        // Rectangle
        double length;
        double width;

        // Circle
        double radius ;


        public Figure(double length, double width) {
            this.shape=Shape.RECTANGLE ;
            this.length = length;
            this.width = width;
        }

        public Figure(double radius) {
            this.shape=Shape.CIRCLE ;
            this.radius = radius;
        }

        double area() {
            switch (shape) {
                case CIRCLE:
                    return length * width ;
                case RECTANGLE:
                    return Math.PI * radius * radius ;
                default:
                    throw new AssertionError(shape) ;
            }
        }
    }
```

Tagged classes are verbose, error-prone and inefficient. It is just a pallid limitation of a class hierarchy.

To transform tagged class :
- define `abstract` class containing an `abstract` method for each method in the tagged class whose behavior depends on the tag. in this case, there's only `area` method.
- If there are fields used in those methods, put them in the abstract class
- Define concrete subclasses and include for each subclass data fields.

```java
    abstract class FigureHierarchical {
        abstract double area() ;
    }

    class Circle extends FigureHierarchical {
        final double radius ;

        public Circle(double radius) {
            this.radius = radius;
        }

        @Override
        double area() {
            return Math.PI * radius * radius ;
        }
    }

    class Rectangle extends FigureHierarchical {
        final double length;
        final double width;

        public Rectangle(double length, double width) {
            this.length = length;
            this.width = width;
        }

        @Override
        double area() {
            return width * length ;
        }
    }
```

The code is simple and clear. All fields are final. This hierarchy reflects natural hierarchical relationship among types.

# Item 24 : Favor `static` member classes over non static

A nested class is a class defined in another class and should exists only to serve its enclosing class. There are 4 types :

## Non `static` member class
Each instance of non static member class is implicitly associated with an enclosing instance of its containing class. It is impossible to create an instance of a non static member class without an enclosing instance.

One common use of non static class is to define Adapter.
```java
public class AdapterSpot extends Applet {
    private Point clickPoint = null;
    private static final int RADIUS = 7;

    public void init() {
        addMouseListener(new MyMouseAdapter());
    }
    public void paint(Graphics g) {
        g.drawRect(0, 0, getSize().width - 1,
                         getSize().height - 1);
        if (clickPoint != null)
            g.fillOval(clickPoint.x-RADIUS,
                       clickPoint.y-RADIUS,
                       RADIUS*2, RADIUS*2);
    }
    class MyMouseAdapter extends MouseAdapter  {
        public void mousePressed(MouseEvent event) {
            clickPoint = event.getPoint();
            repaint();
        }
    }
    /* no empty methods! */
}
```

## `static` class
One common use of static member class is public helper class. If you declare a member class that does not require access to an enclosing instance, always put the `static` modifier in its declaration.
```java
    public class Outer {

        static class Inner {
            public void printValue() {
                System.out.println("Inside Inner class");
            }
        }

        public static void main(String[] args) {
            Inner inner = new Outer.Inner();
            inner.printValue();

        }
    }
```

## Anonymous classes
class with no name. It is not member of its enclosing class. It was very used before Lambdas
```java
class MyThread  {
    public static void main(String[] args)
    {
        //Here we are using Anonymous Inner class
        //that implements a interface i.e. Here Runnable interface
        Runnable r = new Runnable()
        {
            public void run() {
                System.out.println("Child Thread");
            }
        };

        Thread t = new Thread(r);
        t.start();
        System.out.println("Main Thread");
    }
}
```

With Lambda expression :
```java
    public static void main(String[] args) {
        Thread t = new Thread(() -> System.out.println("Child Thread"));
        t.start();
        System.out.println("Main Thread");
    }
```

## Local classes
Local classes can be declared anywhere a local variable is declared and obeys the same scoping rules. They have names and can be used repeatedly.
```java
    public class Outer {

        public static void main(String[] args) {
            Outer outer = new Outer();

            // Local inner class inside if clause
            class Inner {
                public void printValue() {
                    System.out.println("Inside Inner class");
                }
            }

            Inner inner = new Inner();
            inner.printValue();
        }
    }
```

# Item 25 : Limit source files to a single top-level class

There are no benefits associated with defining multiple top-level classes in a single source file. Do not do that

> DoNotDoThat.java

```java
    class DoNotDoThat {
        void method1(){} ;
    }

    class DoNotDoThatDoNotDoThat {
        void method1(){} ;
    }
```
