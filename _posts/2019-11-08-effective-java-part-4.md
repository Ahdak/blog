---
layout: post
title: "Effective Java - 3rd Edition - Generics"
date: 2019-11-08
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 26 : Don't use raw types

A generic class or interface is a class whose declaration has one or more type parameter; for eg `List<E>`.

__Raw types__ is the name of the generic type used without any accompanying parameter. For example, the raw type of `List<E>` is `List`. Raw types behave as if all of the generic type information were erased from the type declaration.

```java
    // Raw collection type - Don't do that
    private final Collection collection = ... ;

    // Parametrized collection type - type safe
    private final Collection<String> collectionSafe = ... ;
```

From this declaration the compiler knows that the collection will contains only `String` and will guarantee that. the compiler inserts invisble casts of you when retrieving from collections and guarantees that they won't fail.
```java
    class Type {}
    class SubType extends Type {}
    class OtherType {}

    void addWithCast() {
        List<Type> listOfType = Lists.newArrayList() ;
        listOfType.add(new Type()) ;
        listOfType.add(new SubType()) ;
        listOfType.add(new OtherType()) ; // Compilation error
    }
```

If you use raw types, you will lose all the safety and expressiveness benefits of generic.
```java
    class Type {}
    class SubType extends Type {}
    class OtherType {}

    void addWithCast() {
      // Do not do that
      List listOfType = Lists.newArrayList() ;
      listOfType.add(new Type()) ;
      listOfType.add(new SubType()) ;
      listOfType.add(new OtherType()) ; // No compilation error
    }
```

In the second case, it would better and safer to use `List<Object>` that tells the compiler that the list is capable to hold objects of any type.

You might be tempted to use a raw type for all collection whose element type is unknown and doesn't matter; for example you want to write a method that takes two sets and return the number of elements that exists in both sets.
```java
    int countCommoon(Set set1, Set set2) {
        int common = 0 ;
        for (Object o : set1){
            if (set2.contains(o)) {
                common ++ ;
            }
        }
        return common ;
    }
```

The method works, but using raw types is dangerous. It would be better to use unbounded wildcards `int countCommoon(Set<?> set1, Set<?> set2) {...}`. The wildcard type is fafe and the raw type isn't. You can put any elements in raw type collection, but, you can't put any element (other than null) into `Collection<?>`.
```java
    void wilcards() {
        Set<String> stringSet = Sets.newHashSet() ;
        unsafeAdd(stringSet,1); // Runtime exception
    }

    void wildcartAdd(Set<?> set, Object o) {
        set.add(o); // Compilation error
        set.add(null) ; // compiles ok
    }

    void unsafeAdd(Set set, Object o) {
        set.add(o);
    }
```

There are minor few exception; You should use raw type :
- You must used raw types in class literals; use `List.class`; `List<String> does not exist`
- When using `instanceof` operator;
```java
    if (stringSet instanceof Set){ // use raw type
                Set<?> s = (Set<?>) stringSet ; // wildcard type
    }
```

# Item 27 : Eliminate unchecked warnings
When you write the following code
```java
    List<String> list = new ArraysList() ;
```

The compiler will remind you that you did wrong: `Warning : unchecked conversion`.
Since Java 7, the diamond operator `<>` is introduced. The compiler will then infer the correct actual type parameter :
```java
    // Do not do that
    List<String> list = new ArraysList<>() ;
```
Try to eliminate every unchecked warning as you can. That's reduce `ClassCastException` occurrences. If you can't eliminate a warning, but, you can prove that the code is type safe, then suppress the warning using `@SuppressWarning("unchecked")`. Always use this annotation on the smallest scope as possible, and add a comment saying why it is safe to do so.

# Item 28 : Prefer `List` to `Array`

Arrays differ from generic in two important ways :
- Arrays are __covariant__, and generics are __invariant__.
```java
    static void covariant() {
        Object[] objects = new Long[1] ;
        objects[0] = "a string" ; // Exception in thread "main" java.lang.ArrayStoreException: java.lang.String
    }

    static void invariant() {
        List<Object> objectList = new ArrayList<Long>() ; // Compilation error - Incompatible types
    }
```

- Arrays are __reified__ : Arrays know and enforce their element type at runtime. Generics are implemented by __erasure__, they enforce their type constraints only at compile time, and erase (discard) their element type information at runtime. Erasue is what allowed generic types to interpolate freely with legacy code.

Types such `E`, `List<E>` and `List<String>` are non-refiable types. Non refiable type is one whose runtime representation contains less information than its compile time. Because of erasure, the only parametrized type that are refiable are unbounded wilcard parameters such as `List<?>` or `Map<?,?>`.

## Genric Array ?
It is not possible to create Generic Array, because it isn't type safe.
```java
public class Item28<T> {
    void genericArr() {
        T[] types = new T[0] ; // Compilation error - type T cannot be instantiated directly
        List<String>[] stringListArray = new List<String>[1] ; //  Compilation error - Generic Array creation
    }
}
```

## Handle unchecked warning
When you get a generic array creation error or unchecked cast warning on cast to an array type, the best solution is often to use collection types like `List<E>`, in preference to `E[]`. You might sacrifice some conciseness or performance, but in exchange, you get better type safety (you won't get `ClassCastException`) and interoperability.

As Rule, arrays and generics don't mix well. If you find yourself mixing them and getting compile-time errors or warning, replace arrays with `List`.

Let's take the following example with Generic Arrays :
```java
public class Item28<T> {

    private final T[] data ;

    public Item28(Collection<T> data) {
        this.data = (T[]) data.toArray(); // warning unchecked cast
    }
}
```

It's better to use `List<T>`
```java
    private final List<T> data ;

    public Item28(Collection<T> data) {
       this.data = new ArrayList<>(data) ; // No warning
    }
```

# Item 29 : Favor generic types
Let's take the following example :
```java
    public class Stack {
        private Object[] elements ;
        private int size = 0 ;

        public Stack() {
            elements = new Object[10] ;
        }

        void push(Object e){
            ensureCapacity() ;
            elements[size++] = e ;
        }

        Object pull(){
            if (size == 0) {
                throw new EmptyStackException() ;
            }
            Object result = elements[--size] ;
            elements[size] = null ; // Eliminates obsolete reference
            return result ;
        }
    }
```

Let's generify this class by changing `Object` by a generic type `E`. Need to make `elements = (E[]) new Object[10]` because :
- Cannot instantiate `new E[10]`
- Use `Object[10]` and add cast to `E[]`

```java
public class Stack<E> {
    private E[] elements ;
    private int size = 0 ;

    public Stack() {
        elements = (E[]) new Object[10] ; // cast warning
    }

    void push(E e){
        ensureCapacity() ;
        elements[size++] = e ;
    }

    E pull(Object e){
        if (size == 0) {
            throw new EmptyStackException() ;
        }
        E result = elements[--size] ;
        elements[size] = null ; // Eliminates obsolete reference
        return result ;
    }
}
```

The other solution is to use `Object[]`. In this cast, need to add a cast to `E result = (E) elements[--size]`.
```java
public class Stack<E> {
    private Object[] elements ;
    private int size = 0 ;

    public Stack() {
        elements = new Object[10] ;
    }

    void push(E e){
        ensureCapacity() ;
        elements[size++] = e ;
    }

    E pull(){
        if (size == 0) {
            throw new EmptyStackException() ;
        }
        E result = (E) elements[--size] ; // Cast warning
        elements[size] = null ; // Eliminates obsolete reference
        return result ;
    }
}
```

In the both cases, we can add `@SuppressWarning("unchecked")` because the cast is safe.

The first technique is more readable : The array is declared of type `E` and requires a single cast when creating the arrays. This technique is more commonly used, but, it causes __heap pollution__: the runtime type of the array does not match its compile time type (unless `E` is type `Object`).
The second technique requires a cast each time an array element is read.

There are some generic types that restrict the permissible value of their type parameter. The type parameter `E` is known as bounded type parameter.
```java
    class Stack<E extends Type> {}
```

# Item 30 : Favor generic methods
Method can be generic. Static utility methods that operate on parametrized types are usually generic.

Let's take the following utility method which is badly using raw types :
```java
    // Do not do that
    static Set union(Set s1, Set s2) {
        Set result = new HashSet(s1) ; // warning unchecked call to ..
        result.addAll(s2) ; // warning unchecked call to ..
        return result ;
    }
```

Th fix this waring and make the method type safe, modify its declaration to declare a type parameter representing the element type for these sets :
```java
    static <E> Set<E> union(Set<E> s1, Set<E> s2) {
        Set<E> result = new HashSet<>(s1) ; // warning unchecked call to ..
        result.addAll(s2) ;
        return result ;
    }
```

# Item 31 : Use bounded wildcards to increase API flexibility

Let's take the following example
```java
    public class Stack<E> {
      // Some code
      void pushAll(Iterable<E> e){
          ensureCapacity() ;
          elements[size++] = e.iterator().next() ;
      }
    }

    void popAll(Collection<E> dest){
      while(!isEmpty()) {
          dest.add(pop());
      }
    }

    public static void main(String[] args) {
        Stack<Number> stack = new Stack<>() ;
        Iterable<Integer> ints = Set.of(1) ;
        stack.pushAll(ints); // Compilation error - incompatible types: java.lang.Iterable<java.lang.Integer> cannot be converted to java.lang.Iterable<java.lang.Number> (JDK 11)
    }
```

Java provides a special kind of type called bounded wildcard type to deal with this situation `<? extends E>` :
```java
    void pushAll(Iterable<? extends E> e){
        ensureCapacity() ;
        elements[size++] = e.iterator().next() ;
    }
```

Let's try to call `popAll` method :
```java
    Collection<Object> objects = Sets.newHashSet() ;
    stack.popAll(objects); // Compilation error incompatible types: java.util.Collection<java.lang.Object> cannot be converted to java.util.Collection<java.lang.Number>
```

In this case, the fix would be to define a collection of any subtype of `E` with `<? super E>` :
```java
    void popAll(Collection<? super E> dest){
        while(!isEmpty()) {
            dest.add(pop());
        }
    }
```

For maximum flexibility, use wildcard type on input parameter that represent producers or consumers. Here is a mnemonic to help remember : __PECS__ stands for `producer-extends`, and `consumer-super`.

One application of PECS in `max` method; Use `Comparable<? super T>` in preference to `Comparable<T>` :
```java
    public static <T extends Comparable<? super T>> T max(List< ? extends T> elements) ;
```

Do not use bounded wildcard types as return types, because it would force client code to use wildcards. Properly used, wildcards are nearly invisible to the user of a class. It the user of a class has to think about wildcard types, there is probably something wrong with its API.

If type parameter appears only in a method declaration, replace it with wildcard.
```java
    // Parametrized method
    public static <E> void swap(List<E> list, int i ,int j) ;

    // Prefer wildcard
    public static void swap(List<?> list, int i ,int j) ;
```

# Item 32 : Combine generics and varargs judiciously

The purpose of varargs is to allow clients to pass variable number of arguments to a method, but it is a leaky abstraction : An array is created to hold varargs parameters when the method is invoked.

Mixing Generics and varargs can violate type safety :
```java
    void dangerous(List<String>... stringList) {
        Object[] objects  = stringList ;
        objects[0] = List.of(1,2) ; // Heap pollution
        String s = stringList[0].get(0) ; // ClassCastException !
    }
```

In Java 7, `@SafeVarargs` was added and works for `final` methods. This annotation constitutes a promise by the author of a method that it is type safe. If the varargs array is used to transmit a variable number of arguments from the caller to the method, then the method is safe.

The rule to use `@SafeVarargs` is :
- The method doesn't store anything in the varargs parameter arrays, and
- The method doesn't make the array (or a clone) visible to untrusted code

Let's take a look to this method :
```java
    static <E> E[] toArray(E... params) {
        return params ;
    }
```
This method is dangerous : The type of the arrays is determined by the compile-time types of the arguments passed to the method, and, the compiler may not have enough information to make an accurate determination. Because this method returns its varargs parameter array and it can propagate heap pollution up to the call stack.

```java
    static <T> T[] generate(T a , T b) {
        return toArray(a,b) ;
    }

    public static void main(String[] args) {
        String[] ss = toArray("A","B") ; // OK
        String[] ss2 = generate("A","B"); // java.lang.ClassCastException: class [Ljava.lang.Object; cannot be cast to class [Ljava.lang.String; ([Ljava.lang.Object; and [Ljava.lang.String; are in module java.base of loader 'bootstrap')
    }
```

When compiling this method, the compiler generates code to create a varargs parameter array. This code allocates an array of type `Object[]`. Then, a cast is missing in the call of `generate` method.

Here is a sage method with generic varargs parameters. Use `@SafeVarargs` on every method with a varargs parameter of a generic or parametrized type.
```java
    @SafeVarargs
    static <T> List<T> flatten(List<? extends T>... lists) {
        List<T> result = new ArrayList<>() ;
        for(List<? extends T> list : lists)
            result.addAll(list) ;
        return result ;
    }
```

An alternative to using `@SafeVarargs` annotation is to use `List`, and the method signature becomes
```java
    static <T> List<T> flatten(List<List<? extends T>> lists)  ;
```

The main disadvantage of this method is that the client code is a bit more verbose :
```java
    // using flatten(List<? extends T>... lists)
    flatten(List.of("A","B","C"),List.of("D","E","F")) ;

    // using flatten(List<List<? extends T>> lists)
    flatten(List.of(List.of("A","B","C"),List.of("D","E","F"))) ;
```

# Item 33 : Consider type safe heterogeneous containers
Consider the following class that allows client to store and retrieve a favorite instance of arbitrary many type.
```java
public class Item33 {

    Map<Class<?>,Object> map = new HashMap<>() ;

   <T> void put(Class<T> type, T instance) {
        map.put(Objects.requireNonNull(type),instance) ;
    }

    <T> T get(Class<T> type) {
        return type.cast(map.get(type)) ;
    }

    public static void main(String[] args) {
        Item33 item33 = new Item33() ;
        // Put sample
        item33.put(String.class,"A");
        item33.put(Integer.class,1);
        item33.put(Class.class, Item33.class);
        // Get Sample
        String s = item33.get(String.class) ;
    }
}
```

Notice
- The `Map` does not guarantee the type relationship between the key and the value.
- the `get` method dynamically cast the object reference to the type referenced by the `class` object. The method `cast` is type safe. It returns the argument with `T` type, otherwise, throws a `ClassCastException`.

One limitation to this class is that you cannot use `List<T>` as type because it is non-refiable type, and `List<String>.class` does not exist for example.

When using bounded wildcards, it is possible to use `class.asSubClass` method.
```java
    class Type {}
    class SubType extends Type {}
    static void getType(Class<? extends Type> anySubType) {
    }

    public static void main(String[] args) {
     Class<?> anyClass = Integer.class ;
     getType(anyClass);  // Compilation error - incompatible types: java.lang.Class<capture#1 of ?> cannot be converted to java.lang.Class<? extends part4.Item33.Type>
     getType((Class<? extends Type>)anyClass); // warning - Unchecked cast, but compiles
     getType(anyClass.asSubclass(Type.class)); // compiles ok, no warning - Runtime error java.lang.ClassCastException: class java.lang.Integer
 }
```
